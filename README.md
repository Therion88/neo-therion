# NeoTherion

**Offline-first AI engine running entirely in your browser**  
Create adaptive chatbots, companions, text adventures, knowledge bases, automation tools — with semantic understanding, persistent memory, state machines, and zero server dependency.

![NeoTherion interface preview](https://via.placeholder.com/800x450/0a0a0c/ff3e3e/ffffff?text=NeoTherion+—+Awakening+the+Beast)  

## What makes NeoTherion special?

- 100% offline after ~30 MB model download (all-MiniLM-L6-v2)
- Semantic matching (384-dim embeddings) + exact regex + fuzzy fallback
- Hierarchical state machines (mood, topic, location, persona…)
- Smart variable injection with casing: `(User)`, `(time)`, fallbacks
- **Dream Mode** — RAG-seeded Markov chains for creative, context-aware fallback responses
- Full Markdown support in responses (tables, code blocks, headers…)
- Powerful response scripting (themes, APIs, DB writes, DOM manipulation…)
- Meshtastic/LoRa ready via scripting bridge
- Built & tested on 2016 mobile hardware — runs great on anything newer; scripting extends for complex needs

## Quick Start (30 seconds)

1. Download or clone this repository
2. Open **`NeoTherion.html`** in any modern browser
3. Wait for the neural core to load (~5–20 seconds first time)
4. Type `hello` to wake the beast

Want to build something?  
→ Click **⚙ System Architect Center** → Core Training tab  
→ Paste JSON rules → **PROCESS STREAM**

Full documentation → [/NeoTherion.md](/NeoTherion.md)

## Current Status (January 2026)

This is the initial public release — fully functional and ready for use.

**Core capabilities included:**

- Rule engine with vector/semantic matching
- Hierarchical state system & variable injection
- Dream Mode (RAG-seeded Markov chains)
- Response scripting (UI themes, fetch APIs, DB manipulation…)
- Export/import + bulk rule ingestion
- 12 practical examples (beginner → expert)

**Scaling & customization notes:**
- Vector search uses centroids by default — performs well for typical use cases (dozens to several hundred rules). For very large rule sets, scripting (e.g. VP-Tree) can be used to optimize.
- Dream Mode delivers lightweight, creative, context-guided responses — tunable and extensible via scripts.
- Primarily English-focused (Compromise NLP), with scripting available for custom/multilingual extensions.

## Folder Structure

```
NeoTherion/
├── NeoTherion.html           ← The single-file application (this is what you open)
├── README.md                 ← You are here
└── NeoTherion.md             ← Complete documentation + 12 working examples

```

## How to Get Started Building

Three main approaches (all explained in the docs):

1. **Manual precision** – Build rules one by one in Core Training
2. **Quick commit / Auto-Teach** – Let the bot dream → instantly turn dreams into rules
3. **Mass import** – Paste JSON (hand-written, LLM-generated, or conversation logs)

## Contributing

Love offline AI, privacy tech, text games, or mesh networking?  
You're very welcome to:

- ⭐ Star the repo
- 🐛 Report issues
- Submit PRs (examples, bug fixes, script accelerators, better indexing…)
- Share your own personas,  rule packs, data packs or use cases. 

## Credits

Developed on:
- DW Pad6S Pro (2016 tablet, MT6755, Mali-T860, Android 8.1)
- Using Markor (text editor) + mobile code IDE

Built with:
- Dexie.js • Compromise • Transformers.js • Marked.js

**License:** MIT  
Modify, extend, share freely — attribution appreciated but not required.

> *“Do what thou wilt shall be the whole of the Law.”*

```
