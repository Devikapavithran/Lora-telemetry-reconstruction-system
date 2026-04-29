# PS-2 · LoRa Telemetry Reconstruction System

<div align="center">

**A real-time middleware dashboard that detects fragmented LoRa telemetry packets, reconstructs missing geospatial data using Kalman filtering, and maintains continuous visual situational awareness — all without manual intervention.**

[🚀 View Live Demo](https://devikapavithran.github.io/Lora-telemetry-reconstruction-system/) 

---

Open the link in any modern browser — no installation required. The dashboard runs entirely client-side.

---
</div>

---

## Problem Statement

In tactical and remote communication setups, **LoRa (Long Range) modules** transmit critical movement and geospatial data over low-power, long-distance radio links. However, LoRa networks are highly susceptible to:

- **Packet loss** due to environmental interference and signal attenuation
- **Data fragmentation** caused by terrain-based dead zones
- **Broken JSON / malformed NMEA sentences** arriving from remote nodes

Current tracking systems require *complete, contiguous data packets* to update movement markers. When nodes transmit partial data, dashboards fail to render movement accurately — producing "stuttering" assets or complete loss of situational awareness.

> **The core challenge:** the absence of an intelligent reconstruction layer that can interpret broken packets and maintain a fluid operational picture without human intervention.


## Features

### 🛰️ Real-Time Node Tracking
- 8 simulated LoRa field nodes with live position updates
- Each node streams heading, velocity, RSSI, and SNR data
- Clickable nodes on the terrain map and in the node list panel

### 🔴 Packet Fragmentation Detection
- Identifies incomplete JSON strings via checksum mismatch simulation (`0xA3 ≠ 0xF7`)
- Detects corrupt or missing fields (`lat`, `lng`, `hdg`) in incoming frames
- Three distinct node states: `VERIFIED`, `ESTIMATED`, `FRAGMENT`, `OFFLINE`

### 🧠 Kalman Filter Reconstruction
- Implements a **1D Kalman Filter** with configurable process noise (Q) and measurement noise (R)
- Predicts current position from `t-1` and `t-2` historical coordinates when `t` is fragmented
- Live display of all Kalman state variables: **Q**, **R**, **Gain (K)**, **Error Covariance (P)**

### 🗺️ Visual Continuity on Terrain Map
- HTML5 Canvas-rendered tactical terrain map with grid, ridgelines, water features, and dead zones
- **Green dots** → verified GPS lock
- **Amber dots** (pulsing) → Kalman-estimated position
- **Red dots** (fast pulse) → active packet fragment / reconstruction engaged
- **Purple ghost dot** → raw Kalman-predicted position overlay
- Node movement trails with configurable display

### 📡 Live Packet Stream
- Real-time packet status bar at the bottom (green / amber / red blocks per packet)
- Raw frame viewer showing actual JSON structure, checksum result, and repair annotations
- RSSI history sparkline per selected node

### ⚡ System Alerts
- Timestamped alert feed for fragment detections, dead zone warnings, SNR changes
- **"Inject Fragment"** button to simulate a real packet loss event live

---

## How It Works

```
LoRa Gateway Stream
        │
        ▼
┌───────────────────┐
│  Data Integrity   │  ← Checksum validation, JSON completeness check
│  Check Layer      │     Detects: missing fields, corrupt headers/footers
└────────┬──────────┘
         │
    ┌────┴──────┐
    │           │
 COMPLETE    FRAGMENTED
    │           │
    ▼           ▼
VERIFIED    Historical Buffer
  DOT       (last 30 frames)
    ●            │
                 ▼
         ┌──────────────┐
         │ Kalman Filter │  x(t) = x(t-1) + K · (z - x(t-1))
         │ State Estimator│  P(t) = (1-K) · P(t-1)
         └──────┬────────┘
                │
                ▼
          ESTIMATED DOT
          (visually distinct)
               ◉
                │
                ▼
         Next verified packet
         → returns to VERIFIED ●
```

### Kalman Filter Parameters

| Parameter | Symbol | Role |
|---|---|---|
| Process Noise | Q | Models how much the node's true position can drift between updates |
| Measurement Noise | R | Models uncertainty in each received GPS reading |
| Kalman Gain | K | Balances trust between prediction and measurement |
| Error Covariance | P | Tracks the filter's own confidence in its estimate |

When a packet is fragmented, the filter **skips the measurement update step**, propagating only the predict step — keeping the estimated position moving fluidly rather than freezing or snapping.

---

## Tech Stack

| Layer | Technology |
|---|---|
| UI / Rendering | Vanilla HTML5, CSS3, Canvas API |
| State Engine | Pure JavaScript (no frameworks) |
| Filter Algorithm | Kalman Filter (custom implementation) |
| Fonts | Orbitron, Share Tech Mono, Exo 2 (Google Fonts) |
| Deployment | GitHub Pages |

No build tools. No dependencies. No backend. Runs entirely in the browser.

---

## Project Structure

```
Lora-telemetry-reconstruction-system/
└── index.html        ← Complete single-file application
```

---

## Running Locally

```bash
# Clone the repository
git clone https://github.com/devikapavithran/Lora-telemetry-reconstruction-system.git

# Navigate into the folder
cd Lora-telemetry-reconstruction-system

# Open directly in browser (no server needed)
open index.html
```

Or simply double-click `index.html` — it runs with zero setup.

---

## Problem Statement Coverage
| Identify packet fragmentation | Checksum mismatch detection + incomplete JSON field scan |
| Historical Data Buffer | 30-frame rolling history per node |
| Kalman Filter state estimation | Full K, Q, R, P cycle — live display in right panel |
| t-1 / t-2 coordinate prediction | Kalman predict step runs on every fragment event |
| Auto-correction of partial JSON | Field repair annotations shown in raw packet viewer |
| Visual continuity — estimated vs verified | Distinct dot colors, pulse animations, and ghost overlay |
| Low-bitrate LoRa stream simulation | Packet stream bar with real-time OK/PARTIAL/BAD ratio |

---

## Screenshots

> Dashboard operates in real-time — open the [live demo](https://devikapavithran.github.io/Lora-telemetry-reconstruction-system/) to see it in action.

**Key UI zones:**
- **Left panel** — Node roster with signal bars and live coordinates
- **Center** — Tactical terrain map with animated node dots and trails
- **Right panel** — Raw packet view, Kalman state readout, RSSI sparkline, alert feed
- **Bottom bar** — Real-time packet delivery stream

---

**Devika Pavithran**
Built as part of a senior-level intelligence systems engineering challenge.

---

<div align="center">
<sub>Built with precision · Deployed on GitHub Pages · Zero dependencies</sub>
</div>
