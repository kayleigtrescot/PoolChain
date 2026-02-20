# SKILL: PoolChain Billiard Agent

## Overview

This skill enables an Intercom agent to participate as an AI opponent in **PoolChain Billiard**, a browser-based 8-ball pool game. The agent reads game state from an Intercom sideroom, computes the optimal shot, and publishes its move.

---

## Sideroom Protocol

**Channel name pattern:** `poolchain:game:{game_id}`

**Message format (JSON):**

```json
{
  "type": "game_state" | "move" | "game_over",
  "game_id": "string",
  "player": 1 | 2,
  "turn": "number",
  "balls": [
    { "id": 0, "x": 210, "y": 210, "pocketed": false },
    { "id": 1, "x": 490, "y": 205, "pocketed": false },
    ...
  ],
  "current_player": 2,
  "p1_balls": [1, 6],
  "p2_balls": [9, 11],
  "fouls": 0
}
```

---

## Agent Instructions

### 1. Listen for game state

Subscribe to sideroom `poolchain:game:{game_id}`. When you receive a message with `type: "game_state"` and `current_player: 2` (your turn), proceed to compute your shot.

### 2. Parse the board

- Ball `id: 0` is the **cue ball**
- Balls `1–7` are **solids** (Player 1's target)
- Balls `9–15` are **stripes** (Agent / Player 2's target)
- Ball `id: 8` is the **8-ball** (pocket last, after all stripes)

Only consider balls where `pocketed: false`.

### 3. Compute optimal shot

```
ALGORITHM: Greedy nearest-pocket targeting

1. Find all your balls (id 9-15, not pocketed), or id 8 if all pocketed
2. For each target ball T:
   a. For each pocket P:
      - Compute angle from cue ball → T → P
      - Score = 1 / (distance(cue, T) + distance(T, P))
3. Pick the (T, P) pair with highest score
4. Compute shot angle = atan2(T.y - cue.y, T.x - cue.x)
5. Set power = clamp(80 - distance(cue, T) * 0.05, 40, 95)
```

### 4. Publish move

Send to the same sideroom:

```json
{
  "type": "move",
  "game_id": "string",
  "player": 2,
  "angle_rad": 1.234,
  "power_pct": 72,
  "target_ball": 11
}
```

### 5. Handle game over

When `type: "game_over"` is received, close the sideroom subscription.

---

## Pocket Coordinates (default 760×420 canvas)

```
TOP-LEFT:    { x: 40,  y: 30  }
TOP-CENTER:  { x: 420, y: 26  }
TOP-RIGHT:   { x: 720, y: 30  }
BOT-LEFT:    { x: 40,  y: 390 }
BOT-CENTER:  { x: 420, y: 394 }
BOT-RIGHT:   { x: 720, y: 390 }
```

---

## Error Handling

| Situation | Action |
|---|---|
| Cue ball pocketed | Wait for respawn state update |
| No valid target | Aim at 8-ball if all stripes pocketed, else aim at lowest-id stripe |
| Network timeout | Retry move after 3 seconds, max 3 attempts |
| Invalid ball id | Log warning, skip that ball |

---

## Example Agent Loop (pseudocode)

```python
async def play_poolchain(game_id, intercom_client):
    room = f"poolchain:game:{game_id}"
    
    async for message in intercom_client.subscribe(room):
        state = json.loads(message)
        
        if state["type"] == "game_over":
            break
            
        if state["type"] == "game_state" and state["current_player"] == 2:
            move = compute_shot(state)
            await intercom_client.publish(room, json.dumps(move))

def compute_shot(state):
    cue = next(b for b in state["balls"] if b["id"] == 0)
    targets = [b for b in state["balls"] if b["id"] >= 9 and not b["pocketed"]]
    
    if not targets:
        targets = [b for b in state["balls"] if b["id"] == 8 and not b["pocketed"]]
    
    best = min(targets, key=lambda t: distance(cue, t))
    angle = math.atan2(best["y"] - cue["y"], best["x"] - cue["x"])
    power = max(40, min(95, 80 - distance(cue, best) * 0.05))
    
    return {
        "type": "move",
        "game_id": state["game_id"],
        "player": 2,
        "angle_rad": angle + random.uniform(-0.1, 0.1),  # slight jitter
        "power_pct": power,
        "target_ball": best["id"]
    }
```

---

## Notes for Agents

- Always add a small random jitter (±0.05–0.15 rad) to shots to look more natural
- Introduce a 0.5–1.5 second "thinking" delay before publishing moves
- Log all moves to the Trac replicated state layer for dispute resolution
- If the game is abandoned (no state update for 60s), close the sideroom gracefully
