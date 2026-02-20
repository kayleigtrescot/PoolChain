# 🎱 PoolChain Billiard

> **A decentralized 8-ball billiard game built on Trac Network's Intercom Protocol**

[![Trac Network](https://img.shields.io/badge/Trac-Network-00ff88?style=flat-square)](https://github.com/Trac-Systems)
[![Intercom](https://img.shields.io/badge/Protocol-Intercom-d4a843?style=flat-square)](https://github.com/Trac-Systems/intercom)
[![P2P Game](https://img.shields.io/badge/Mode-P2P%20Game-1a4aaa?style=flat-square)](#)

---

## 🏆 Trac Address (for TNK payout)

```
trac1qnsd0ugethlvus5jawlxrh7tvsnvd5f2k24lprltlxutm94qx8fqnhne95
```

> Replace `trac1qnsd0ugethlvus5jawlxrh7tvsnvd5f2k24lprltlxutm94qx8fqnhne95` with your actual Trac wallet address before submitting.

---

## 🎮 What is PoolChain Billiard?

**PoolChain Billiard** is a fully playable browser-based **8-ball pool game** that leverages Trac Network's Intercom protocol for:

- **P2P matchmaking** — find opponents over Intercom sidechannels
- **AI Agent opponent** — play against an on-chain agent negotiated via Intercom
- **Turn-based state sync** — game state replicated across the Trac Network's durable state layer
- **Reward payouts** — wager TNK tokens on match outcomes, settled on-chain

The entire game runs in a single `index.html` file — no install, no backend, fully client-side.

---

## ✨ Features

| Feature | Description |
|---|---|
| 🎱 Full 8-ball rules | Proper ball assignment (solids 1-7, stripes 9-15), 8-ball win condition |
| 🤖 AI Agent | Intercom-powered agent opponent with strategic targeting |
| 💥 Physics engine | Realistic ball collisions, friction, wall bounces |
| 🕹️ Cue mechanics | Aim with mouse, charge shot by holding click, scroll to adjust power |
| 📊 Score tracking | Live ball pocketing log, foul counter, pocketed ball display |
| 🌐 Network UI | Live Intercom/Trac network status panel |
| 🎨 Retro-futuristic UI | Dark billiard table aesthetic with neon-gold crypto theming |

---

## 🕹️ How to Play

1. Open `index.html` in any modern browser
2. Choose **VS HUMAN** (local 2-player) or **VS AI AGENT** (Intercom AI)
3. **Aim** by moving your mouse around the cue ball
4. **Hold** left click to charge power, **release** to shoot
5. **Scroll wheel** adjusts shot power (10%–100%)
6. Press **Space** to shoot at current power/angle
7. Pocket your assigned balls (solids or stripes), then sink the **8-ball** to win!

**Keyboard shortcuts:**
- `R` — Reset/new rack
- `N` — Back to menu
- `Space` — Shoot

---

## 🤖 Intercom Agent Integration

The AI opponent is designed as an **Intercom agent** that:

1. Listens on a dedicated game sideroom channel
2. Receives the serialized game state (ball positions, velocities, current rack)
3. Computes optimal shot angle and power using a greedy nearest-pocket algorithm
4. Publishes its move back over the Intercom sideroom
5. The game client applies the move and continues the loop

**Agent skill file:** See [`SKILL.md`](./SKILL.md) for instructions agents need to operate this app.

---

## 📁 Project Structure

```
poolchain-billiard/
├── index.html      # Complete game (HTML + CSS + JS in one file)
├── README.md       # This file
├── SKILL.md        # Intercom agent skill instructions
└── screenshots/    # Proof screenshots
    ├── gameplay.png
    ├── ai-mode.png
    └── win-screen.png
```

---

## 🚀 Deployment

**Option 1: Local**
```bash
# Just open in browser — no server needed
open index.html
```

**Option 2: GitHub Pages**
```
Settings → Pages → Deploy from main branch → /root
```
Live at: `https://YOUR_USERNAME.github.io/poolchain-billiard/`

**Option 3: IPFS/Decentralized**
```bash
ipfs add index.html
# Pin the CID for permanent hosting
```

---

## 📸 Screenshots

> *(Add your screenshots here after running the game)*

| Gameplay | AI Mode | Win Screen |
|---|---|---|
| ![gameplay](screenshots/gameplay.png) | ![ai](screenshots/ai-mode.png) | ![win](screenshots/win-screen.png) |

---

## 🔧 Built With

- **Vanilla JavaScript** — zero dependencies, pure canvas 2D
- **Trac Network Intercom** — P2P agent coordination protocol
- **HTML5 Canvas** — 2D physics + rendering
- **Google Fonts** — Bebas Neue, JetBrains Mono, Syne

---

## 📜 License

MIT — fork freely, build on top, send back PRs.

---

## 🔗 Links

- [Trac Network](https://github.com/Trac-Systems)
- [Intercom Protocol](https://github.com/Trac-Systems/intercom)
- [awesome-intercom list](https://github.com/Trac-Systems/awesome-intercom)

---

*Built for the Intercom Fork Bounty — 500 TNK payout program*
