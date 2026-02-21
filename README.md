# MITANSHU P2P — Enterprise Suite v14.0

> **Zero-Trust. Zero-Server. 100% Browser-Native.**
> A full-featured, serverless Peer-to-Peer communication platform built entirely in a single HTML file using WebRTC — no backend, no data harvesting, no compromise.

## Main Site - https://mitanshu-bhasin.github.io/mitanshu-p2p/

[![Live Demo](https://img.shields.io/badge/Live%20Demo-mitanshu--bhasin.github.io-blue?style=flat-square&logo=github)](https://mitanshu-bhasin.github.io/mitanshu-p2p/)
[![License](https://img.shields.io/badge/License-Open%20Source-green?style=flat-square)](./LICENSE)
[![WebRTC](https://img.shields.io/badge/Powered%20By-WebRTC-orange?style=flat-square)](https://webrtc.org/)
[![PWA](https://img.shields.io/badge/PWA-Ready-purple?style=flat-square)](./manifest.json)

---

## Table of Contents

1. [What Is This?](#what-is-this)
2. [How P2P Works](#how-p2p-works)
3. [Feature Reference](#feature-reference)
   - [Dashboard — Connection Hub](#1-dashboard--connection-hub)
   - [Secure Chat](#2-secure-chat--e2e-encrypted-messaging)
   - [Video Call & Screen Share](#3-video-call--screen-share)
   - [Live GPS Map](#4-live-gps-map)
   - [File Transfer](#5-file-transfer)
   - [Code Studio](#6-code-studio)
   - [Gaming Hub](#7-gaming-hub)
   - [Browse Share](#8-browse-share)
   - [Whiteboard](#9-infinite-whiteboard)
   - [Watch Party](#10-media-sync--watch-party)
   - [Task Board](#11-collaborative-task-board)
4. [Security & Privacy](#security--privacy)
5. [Tech Stack](#tech-stack)
6. [Architecture](#architecture)
7. [Quick Start](#quick-start)
8. [FAQ Page (`faq.html`)](#faq-page-faqhtml)
9. [PWA & Service Worker](#pwa--service-worker)
10. [About the Architect](#about-the-architect)
11. [License](#license)

---

## What Is This?

**MITANSHU P2P Enterprise Suite** is a browser-based, serverless communication platform. It uses **WebRTC** to establish a direct encrypted tunnel between two browsers — no data passes through any central server after the initial handshake.

Everything runs in a **single `index.html` file** (~144 KB) with no build step, no backend, and no installation required.

**Core Capabilities at a glance:**

| Module | Feature |
|---|---|
| 💬 Secure Chat | End-to-end encrypted text + voice messages |
| 🎥 Video & Screen | Full-duplex video call + screen sharing |
| 📍 GPS Map | Live real-time location broadcasting |
| 📁 File Transfer | Unlimited size file & folder transfer |
| 💻 Code Studio | Collaborative editor (Python, SQL, HTML) |
| 🎮 Gaming Hub | 5 real-time P2P multiplayer games |
| 🌐 Browse Share | Co-browsing & secure link sharing |
| 🎨 Whiteboard | Infinite synchronized drawing canvas |
| 🍿 Watch Party | Synchronized media player |
| ✅ Task Board | Collaborative Kanban To-Do list |

---

## How P2P Works

```
Browser A (You)
  │
  ├─→ PeerJS Signal Server  ← (only for initial handshake / ID exchange)
  │         │
  │         └─→ ICE / STUN / TURN  ← (NAT traversal)
  │
  └─═══════ WebRTC Encrypted Direct Tunnel ═══════
                                                  │
                                            Browser B (Peer)

⚠  Signal Server = ONLY Peer ID exchange (no data passes through it)
✓  All actual data flows DIRECTLY between peer browsers
✓  DTLS encryption on DataChannels
✓  SRTP encryption on Audio/Video streams
```

**Step-by-step connection flow:**

1. Page loads → `new Peer()` connects to PeerJS signalling server
2. Server assigns a random unique Peer ID (e.g., `mtn-abc-xyz-123`)
3. You click **"Share Link"** → link with your ID embedded as `?peer=YOUR_ID` is copied
4. Your peer opens that link → auto-connects via the URL parameter
5. WebRTC tunnel establishes → header turns **ONLINE** → all features activate

> **Manual mode:** Your peer can also paste your ID directly into the dashboard input field and click LINK.

---

## Feature Reference

### 1. Dashboard — Connection Hub

The Dashboard is the control panel and entry point of the app.

**What it does:**
- Generates and displays your unique **Digital ID** on page load
- Provides a **"Share Link to Connect"** button that copies a shareable URL
- Accepts a peer's ID manually via a text input + LINK button
- Shows real-time **connection status** (OFFLINE / ONLINE) with an animated badge
- Displays active **peer count** and an **E2E security badge** once connected
- Routes to all 7 other feature modules via sidebar (desktop) or hamburger drawer (mobile)

**Mobile Navigation:**
On mobile, a hamburger menu (☰) in the top-left opens a slide-in drawer exposing all navigation tabs. Desktop uses a persistent left sidebar icon nav.

**Auto-Connect via URL:**
```
https://your-deployed-url.com/?peer=PEER_ID_HERE
```
When someone opens a shared link, the app reads `?peer=` from the URL and auto-initiates the connection — no manual ID entry needed.

---

### 2. Secure Chat — E2E Encrypted Messaging

**How it works:**

```
You: Type message → Press Enter / Click Send
  │
  ├─→ DataChannel.send({ type: 'CHAT', msg: 'Hello' })
  │
  └─══ DTLS Encrypted ══→ Peer Browser → Rendered instantly

✗ No server stores messages
✗ No chat history after tab closes
✓ Truly private, ephemeral conversation
```

**Features:**
- Type and send text messages; press `Enter` or click the paper-plane button
- **Voice Messages:** Hold the 🎙️ microphone button → speak → release to send
  - Uses `MediaRecorder API` to capture audio, converts to Blob, sends over DataChannel
  - Visual pulse animation while recording is active
- Clear chat history with the 🗑️ trash icon (local only)
- Messages are styled differently for sent vs received

**Data payload format:**
```json
{ "type": "CHAT", "msg": "message content" }
{ "type": "VOICE", "blob": "<ArrayBuffer>" }
```

---

### 3. Media Suite (Video & Screen Share)

**Workflow:**
```
Select Camera or Screen Share
  │
  ├─→ Parallel Streaming: Run Camera and Screen simultaneously
  │
  ├─→ Meta-Data Protocol: Peers identify stream type (Video vs Screen)
  │
  ├─→ Interactive Viewer:
  │     - Full-Screen individual streams
  │     - Real-time Zoom (1x to 5x) via GPU transforms
  │
  └─→ Adaptive Grid: Layout automatically scales based on active streams
```

**Controls:**

| Button | Function |
|--------|----------|
| 📹 Video | Start video call |
| 🖥️ Screen | Share your screen |
| 🔄 Camera Rotate | Switch between front/rear cameras (mobile-friendly) |
| 🎙️ Mic Toggle | Mute / unmute microphone |
| ⛶ Fullscreen | Expand video view to full screen |
| 📵 End Call | Terminate call, release all media streams |

**Security:** Video and audio streams are encrypted end-to-end using **SRTP (Secure Real-time Transport Protocol)**. No stream data passes through any server.

---

### 4. Live GPS Map

**Workflow:**

```
Click "Broadcast Live GPS"
  │
  ├─→ browser.geolocation.getCurrentPosition()
  │       ↳ Uses GPS / WiFi / Cell towers
  │
  ├─→ Sends { type: 'GPS', lat: X, lng: Y } via DataChannel
  │
  └─→ Peer's Leaflet.js map updates marker in real-time
```

**Features:**
- Interactive **Leaflet.js** map rendered directly in the browser
- Live marker with coordinates updates every time you broadcast
- Only shared when **you** explicitly click the broadcast button
- Location data goes **only to the connected peer** — never to any server
- Works on both mobile (GPS) and desktop (WiFi/IP geolocation)

**Privacy:** No location data is stored or logged. The moment the connection closes, all location data is gone.

---

### 5. Swarm File Transfer (BitTorrent-Style)

**Workflow:**

```
Select File(s) or Folder
  │
  ├─→ Swarm Engine splits file into 16KB chunks
  │
  ├─→ Round-Robin Seeding:
  │     Initial sender sends different chunks to different peers
  │
  ├─→ P2P Mesh Distribution:
  │     Peers broadcast SWARM_HAVE for chunks they own
  │     Other peers pull missing chunks via SWARM_REQ
  │
  └─→ Receiver reassembles chunks → Blob → Download Link
```

**Features:**
- **Decentralized Swarming** — If Peer A sends a 500MB file to B, C, and D, B can "seed" chunks to C and D the moment it receives them. No single peer bears the full bandwidth load.
- **Dual-mode upload area:**
  - 📄 **Select Files** — pick one or multiple individual files
  - 📁 **Select Folder** — pick an entire directory (`webkitdirectory` attribute)
- **No size limit** — uses advanced ArrayBuffer chunking to bypass WebRTC datachannel limits.
- All formats supported (images, videos, documents, archives, executables).
- Real-time **progress bar** with file name and percentage.
- **Mesh Seeding Indicator** — Dashboard shows which peers are actively seeding chunks to you.
- **Transfer Activity Log** — shows all sent/received files in the session.
- **Active Swarm Badge** — Visual pulsing indicator on the "Files" tab when a swarm is active.
- **Bulk Save (Save All)** — Download all received files in one click with built-in browser-block prevention.

**Swarm Protocol Logic:**
```json
{ "type": "SWARM_START", "fileId": "...", "name": "...", "total": 1200 }
{ "type": "SWARM_CHUNK", "fileId": "...", "index": 5, "data": "<Buffer>" }
{ "type": "SWARM_HAVE",  "fileId": "...", "index": 5 }
{ "type": "SWARM_REQ",   "fileId": "...", "index": 6 }
```

---

### 6. Code Studio

**Collaborative real-time code editor with in-browser execution.**

**Workflow:**

```
Type in ACE Editor
  │
  ├─→ Every keystroke sends { type: 'CODE', val: '...', lang: 'python' }
  │
  ├─→ Peer's editor syncs in real-time (true collaborative coding)
  │
  └─→ Click RUN → executes code locally in browser
```

**Supported Languages:**

| Language | Engine | What it does |
|----------|--------|--------------|
| 🐍 Python | Pyodide (WebAssembly CPython) | Full Python runtime in browser |
| 🌐 HTML | iframe Preview | Renders HTML live in an embedded frame |
| 🗄️ SQL | AlaSQL Engine | In-memory SQL queries, instant results |

**Editor Features:**
- Powered by **ACE Editor** — professional-grade code editor
- Syntax highlighting, auto-indentation, line numbers
- Split layout: Editor (left) + Console output (right)
- Console clear button
- Language switcher dropdown (Python / HTML / SQL)

**Collaboration:** Both peers see each other's keystrokes in real-time — like Google Docs but for code, over an encrypted P2P channel.

---

### 7. Gaming Hub

**5 real-time multiplayer games, all synchronized over WebRTC DataChannel. Zero lag, zero servers.**

All games use the message format:
```json
{ "type": "GAME", "game": "chess|ttt|rps|tap|rt", "action": "..." }
```

---

#### ♟️ Chess — Full Rules Engine

The most feature-rich game. Complete chess implementation with ALL standard rules.

**Workflow:**

| Step | Description |
|------|-------------|
| 1 | Color assignment: Peer with the lexicographically smaller ID plays **White** (moves first) |
| 2 | Click a piece → valid moves highlighted with green dots (captures with red ring) |
| 3 | Click destination → move syncs: `{ game:'chess', action:'MOVE', from, to }` |
| 4 | Check detection → King square pulses red. Checkmate → game over screen |

**Supported Rules:**

| Rule | Status |
|------|--------|
| Castling (Kingside & Queenside) | ✅ |
| En Passant | ✅ |
| Pawn Promotion (with choice modal) | ✅ |
| Check Detection | ✅ |
| Checkmate / Game Over | ✅ |
| Captured Pieces Display | ✅ |

**Extras:** Fullscreen mode available via ⛶ button. Reset button for new game.

---

#### ❌⭕ Zero Katta (Tic-Tac-Toe)

Classic 3×3 grid. Peer IDs determine who plays X (first move).

- The peer with the alphabetically smaller ID plays **X** and goes first
- Each move syncs: `{ game:'ttt', action:'MOVE', index: 0-8 }`
- Win/draw detection runs locally on both browsers simultaneously
- Reset button available for a new round

---

#### ✊✋✌️ Stone Paper Scissors — Anti-Cheat Edition

**Blind Pick System** prevents cheating:

1. Both peers independently select Stone / Paper / Scissors
2. Until **both** picks are submitted, neither player sees the other's choice
3. Once both picks arrive → results revealed simultaneously
4. Scoring: Stone > Scissors > Paper > Stone

---

#### ⚡ Reaction Duel

Real-time reflex testing:

1. Host clicks **START** → random delay timer begins (anti-cheat, prevents prediction)
2. After the delay, the button turns **GREEN** and the phone vibrates
3. Both players tap as fast as possible
4. Their reaction times (in milliseconds) are exchanged via DataChannel
5. **Lowest ms wins!** 🏆

---

#### 🔥 Tap War

Tug-of-war style mashing game:

- Progress bar starts at **50/50**
- Every tap shifts the bar toward **your** side
- Drag it to **100%** on your end to win
- Real-time sync — every tap is immediately visible on both screens

---

### 8. Browse Share

**Two sub-features for co-browsing and link sharing.**

**Enable Toggle:** Both peers must individually enable Browse Share using the toggle switch in the header. This is a deliberate security gate.

---

#### 🔗 Link Share

Send a URL to your peer with their consent:

1. Paste a URL into the input field
2. Click **Send** → peer receives a confirmation notification
3. Peer must explicitly **Accept** before the link opens in their new tab
4. If peer declines, nothing happens

```json
{ "type": "LINK_REQ", "url": "https://example.com" }
{ "type": "LINK_ACK" }  // peer's acceptance
```

---

#### 👁️ Shared Preview (Co-Browsing)

Load the same webpage in both browsers simultaneously via iframe:

- Enter a **public URL** → both peers' iframes load the same page
- Ideal for: code reviews, design previews, reading articles together, presentations
- **For local servers:** use `ngrok` or similar tunnelling to create a public URL for your local dev server
- Works best with CORS-permissive public URLs

---

### 9. Infinite Whiteboard

**Real-time collaborative drawing canvas.**

**Workflow:**
- Both peers open the Whiteboard tab.
- Drawing actions (start, move, end) are broadcasted immediately over the DataChannel.
- `broadcast({ type: 'BOARD', x0: X, y0: Y, x1: X, y1: Y, c: 'color', s: size })`
- **Features:** Pen, Eraser, color picker, and clear board button. Syncs perfectly with zero latency.

---

### 10. Media Sync / Watch Party

**Synchronized video playback across peers.**

**Workflow:**
- Paste a URL to an `.mp4` file or load a local video file.
- Click play, pause, or seek — the exact timestamp is sent to your peer.
- `broadcast({ type: 'MEDIA', action: 'PLAY', time: 1.23 })`
- If a peer falls out of sync by >1 second, their video automatically seeks to catch up.

---

### 11. Collaborative Task Board

**Shared Kanban / To-Do listing for peer productivity.**

**Workflow:**
- Add a new task → instantly appears on peer's screen.
- Toggle completion (checkbox) or delete a task → state updates immediately across the mesh.
- Data payload: `{ type: 'TASKS', action: 'ADD', task: { id, text, done, ts } }`

---

## Security & Privacy

### ✅ What IS Secure

| Protection | Detail |
|------------|--------|
| **DTLS Encryption** | All DataChannel traffic (chat, files, game data, GPS, voice) encrypted |
| **SRTP Encryption** | All video and audio streams encrypted end-to-end |
| **No Server Storage** | Zero messages, files, or location data stored on any server |
| **Ephemeral Sessions** | Tab closed / refreshed = everything is gone permanently |
| **Direct P2P Data Flow** | After the handshake, data travels exclusively between the two browsers |
| **Random Peer IDs** | PeerJS generates cryptographically random IDs — no real identity is linked |
| **Anti-Cheat Games** | Blind Pick for RPS, random delay for Reaction Duel |
| **Browse Share Consent** | Both peers must opt-in; link open requires peer confirmation |

### ⚠️ Limitations (Honest Assessment)

| Limitation | Detail |
|------------|--------|
| **Signal Server Visibility** | PeerJS signal server sees your Peer ID during handshake (not your data) |
| **No Authentication** | No login system — share your ID only with people you trust |
| **Potential IP Exposure** | WebRTC's STUN/TURN process may expose IP addresses. Use a VPN to mitigate |
| **HTTPS Required** | Camera, microphone, screen share, and geolocation require HTTPS |

### Encryption Summary

```
━━━ ENCRYPTED ━━━
✓ Text Messages      (DTLS)
✓ Voice Messages     (DTLS)
✓ File Transfers     (DTLS)
✓ Game State Data    (DTLS)
✓ GPS Coordinates    (DTLS)
✓ Video / Audio      (SRTP)

━━━ NEVER STORED ━━━
✗ No chat logs on any server
✗ No file copies on any server
✗ No location history
✗ No user accounts or profiles

━━━ BE AWARE ━━━
⚠ PeerJS signal server sees your Peer ID
⚠ WebRTC STUN process may reveal IP
⚠ Use HTTPS for camera / mic / location features
```

---

## Tech Stack

| Library / Technology | Version | Role |
|----------------------|---------|------|
| **PeerJS** | 1.5.2 | WebRTC abstraction — P2P connections, DataChannels, Media Calls |
| **Tailwind CSS** | CDN | Utility-first styling framework |
| **Leaflet.js** | 1.9.4 | Interactive GPS maps and real-time markers |
| **ACE Editor** | 1.32.7 | Professional in-browser code editor |
| **Pyodide** | 0.25.0 | CPython compiled to WebAssembly — runs real Python in browser |
| **AlaSQL** | 4 | In-memory SQL engine for browser-side query execution |
| **MediaRecorder API** | Native | Captures voice audio from microphone |
| **Font Awesome** | 6.4.0 | Icon library |
| **Inter + JetBrains Mono** | Google Fonts | Typography |
| **Service Worker** | Custom (`sw.js`) | PWA offline support and caching |

**Zero backend dependencies.** All logic is pure client-side JavaScript (ES6+).

---

## Architecture

```
┌────────────────────────────────────────────────────────┐
│   Single HTML File: index.html (~144 KB)               │
│                                                        │
│   ├─ HTML Structure     (8 views, sidebar, header)     │
│   ├─ CSS               (Tailwind + custom animations)  │
│   └─ JavaScript                                        │
│         ├─ PeerJS Connection Manager                   │
│         ├─ Secure Chat Engine                          │
│         ├─ Voice Message Handler (MediaRecorder)       │
│         ├─ Media Call Handler (Video / Screen)         │
│         ├─ File Transfer Engine (Chunked ArrayBuffer)  │
│         ├─ GPS Broadcast Module (Leaflet.js)           │
│         ├─ Code Studio (ACE + Pyodide + AlaSQL)        │
│         ├─ Game Engines × 5 (Chess, TTT, RPS, Tap, RT)│
│         └─ Browse Share Module                         │
└────────────────────────────────────────────────────────┘

External CDN Dependencies:
  PeerJS • TailwindCSS • Font Awesome
  Leaflet • ACE Editor • Pyodide • AlaSQL

Supporting Files:
  faq.html     — Full A-to-Z interactive documentation
  sw.js        — Service Worker (PWA / offline caching)
  manifest.json — PWA manifest
  robots.txt   — SEO crawl rules
  sitemap.xml  — XML sitemap
  icons/       — PWA icon set (multiple sizes)
```

---

## Quick Start

### Option 1 — Just Open It

```bash
# Clone the repository
git clone https://github.com/mitanshu-bhasin/mitanshu-p2p.git

# Open index.html in Chrome, Edge, or Brave
# No npm install. No build step. No server needed.
```

### Option 2 — Use the Live Hosted Version

```
https://mitanshu-bhasin.github.io/mitanshu-p2p/
```

### Connect Two Peers

1. **Peer A** opens the app → copies the **Share Link**
2. **Peer B** opens the shared link → auto-connects
3. Header turns green: **● ONLINE** → all features are live

**Or manually:**
1. Peer A shares their **Peer ID** (the alphanumeric string shown on dashboard)
2. Peer B pastes it into the "Paste Peer ID Here" field → clicks **LINK**

### Pro Tips

- 🌐 Host on **Netlify / Vercel / GitHub Pages** for a permanent shareable URL
- 🔗 Auto-connect works via `?peer=PEER_ID` in the URL
- 📱 Fully responsive — works on mobile browsers (Chrome for Android, Safari iOS)
- 🔒 Always use **HTTPS** for camera, microphone, screen share, and GPS features
- 🛡️ For extra IP privacy, use a **VPN** before connecting

---

## FAQ Page (`faq.html`)

The project includes a companion **`faq.html`** — a fully interactive, tab-based A-to-Z documentation page.

**What it covers:**

| Tab | Content |
|-----|---------|
| 🏠 Dashboard | How P2P connection works, workflow steps, mobile navigation |
| 💬 Secure Chat | Encryption details, voice message flow, DataChannel protocol |
| 🎥 Video Call | getUserMedia workflow, screen share, mic controls |
| 📍 Live GPS | Geolocation API flow, privacy guarantees |
| 📁 File Transfer | Chunking algorithm, progress tracking, folder support |
| 💻 Code Studio | Collaborative sync, language execution engines |
| 🎮 Games | Individual workflow for all 5 games with anti-cheat notes |
| 🌐 Browse Share | Link share consent flow, co-browsing iframe feature |
| 🎨 Workspace | Whiteboard drawing paths, Watch Party media sync, Kanban Task Board states |
| 🛡️ Security | Full encryption breakdown, limitations disclosure |
| 🔬 Tech Stack | Library-by-library breakdown with architecture diagram |

**Design features of faq.html:**
- Glassmorphism frosted-glass cards
- Smooth fade-up animations between tabs
- ASCII architecture diagrams with colour-coded flow
- Monospace code snippets showing real DataChannel payload formats
- Mobile responsive with horizontal scrollable tab bar
- Back navigation to main app

---

## PWA & Service Worker

**MITANSHU P2P is a Progressive Web App (PWA).**

| Feature | Detail |
|---------|--------|
| `manifest.json` | App name, icons, theme colour, display mode |
| `sw.js` | Service Worker — caches core assets for offline access |
| Icons | Full set for all device sizes (`icons/` directory) |
| Installable | "Add to Home Screen" prompt on mobile and desktop Chrome |

The Service Worker implements a **cache-first** strategy for static assets, ensuring the app loads even without an internet connection (though a live P2P connection obviously requires network).

---

## About the Architect

**Mitanshu Bhasin** — 16-year-old JEE aspirant (PCM + CS), Python Elite, Cyber Hygiene Practitioner.

- 🏛️ **Certified** by MeitY (Govt. of India) as a Cyber Hygiene Practitioner
- 🐍 **300-hour Elite Python Certification** from freeCodeCamp
- 🖥️ **Dev Rig:** Asus FA506NC-HHN200WS
- ⌨️ **Peripherals:** Zebronics KM2100 keyboard + Phero 2400 DPI mouse
- 🌐 **Connection:** 75 Mbps Wi-Fi

> *"You just need the right code and a rebellious mindset."*

---

## License

**Open for the public. Closed for the corporates.**

Feel free to fork it, mod it, learn from it, and build on it.
Just give credit where it's due. ✌️

---

*MITANSHU P2P Enterprise Suite v14.0 | Architect: Mitanshu Bhasin*