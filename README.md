# NeoTherion AI Engine

> **A 100% offline, browser-native AI companion with semantic matching, state machines, and scriptable responses.**

![Version](https://img.shields.io/badge/version-1.0.0-red)
![License](https://img.shields.io/badge/license-MIT-blue)
![Browser](https://img.shields.io/badge/browser-offline--ready-green)

NeoTherion is a self-contained AI engine that runs entirely in your browser. No server, no API keys, no data collection — just pure client-side intelligence.

## ✨ Features

- 🧠 **Semantic Vector Search** - 384-dimensional embeddings via Transformers.js
- 🎯 **Hybrid Matching** - Regex, fuzzy, and vector similarity with configurable thresholds
- 🔄 **State Machines** - Hierarchical context management with inheritance
- 💾 **Persistent Memory** - IndexedDB storage for rules, variables, and history
- 🎨 **Markdown Support** - Rich text rendering in responses
- ⚡ **Script Execution** - JavaScript in responses for dynamic behavior
- 🌀 **Dream Mode** - RAG-seeded Markov chains for creative fallback
- 🛠️ **Visual Rule Builder** - Full-featured Architect Center UI
- 📦 **Standalone Mode** - Ship pre-configured bots as single HTML files

## 🚀 Quick Start

### Just open the file NeoTherion.html - works immediately

First run downloads ~30-90MB model (one-time, cached forever).

## 📖 Documentation

See **[DOCUMENTATION.md](DOCUMENTATION.md)** for:
- Complete feature guide
- API reference
- Rule creation tutorials
- State management
- Script execution
- Advanced examples

## 🎮 Usage

### Developer Mode
1. Open `NeoTherion.html` in browser
2. Click **⚙ SYSTEM ARCHITECT CENTER**
3. Build rules using the visual editor
4. Export brain JSON when ready

### Standalone Bot Mode
1. Paste brain JSON into `<textarea id="neo-brain">`
2. Customize branding (title, welcome message, etc.)
3. Distribute single HTML file
4. Dev tools auto-hide, scripts auto-enable

## 📁 Project Structure

```
neo-therion/
├── NeoTherion.html          # Single-file application
├── libs/                    # Offline libraries (included)
│   ├── dexie.min.js
│   ├── compromise.min.js
│   └── marked.min.js
├── models/                  # AI models (auto-downloaded)
│   └── README.md
├── README.md               # This file
└── DOCUMENTATION.md        # Full documentation
```

## 🔧 Requirements

**Browser Support:**
- Chrome/Edge 90+
- Firefox 88+
- Safari 14+
- Mobile browsers with ES2020+ support

**Technical:**
- IndexedDB
- Web Workers
- WebAssembly (for ONNX)
- ~200MB RAM when active

## 💡 Examples

### Simple Greeting
```json
{
  "rules": [{
    "triggers": ["hello", "hi"],
    "responses": [{"weight": 1, "text": "Hello! How can I help?"}],
    "stateWeights": {"Positive": 1}
  }]
}
```

### Name Capture
```json
{
  "rules": [{
    "triggers": ["my name is [user]"],
    "responses": [{"weight": 1, "text": "Nice to meet you, **(User)**!"}],
    "stateWeights": {"Neutral": 1}
  }]
}
```

### Theme Changer (Script)
```json
{
  "rules": [{
    "triggers": ["dark mode"],
    "responses": [{
      "weight": 1,
      "text": "Switching to dark mode...\n<script>document.documentElement.style.setProperty('--bg', '#000');</script>"
    }]
  }]
}
```

**See DOCUMENTATION.md for 12+ complete working examples.**

## 🎯 Use Cases

- **Personal AI Companions** - Customizable digital entities
- **Knowledge Bases** - Semantic search over documents
- **Interactive Fiction** - Text adventures with state
- **Chat Interfaces** - Rule-based customer support
- **Teaching Tools** - Adaptive learning systems
- **Prompt Prototyping** - Test AI behaviors locally

## 🔒 Privacy & Security

- ✅ **100% Local** - No data sent to servers
- ✅ **Offline-First** - Works without internet after initial load
- ✅ **Client-Side Storage** - IndexedDB stays on device
- ✅ **Optional Scripts** - User controls code execution
- ⚠️ **Script Warning** - Only enable for trusted content

## ⚡ Performance

| Metric | Value |
|--------|-------|
| Model Download | 30-90 MB (one-time) |
| RAM Usage | ~200-300 MB |
| Vector Generation | 50-100ms each |
| IndexedDB Quota | 50-100 MB typical |
| Max Rules (practical) | ~1000 before slowdown |

## 🐛 Troubleshooting

**Loader hangs?**
- Check console for errors
- Verify internet (first run only)
- Try local server instead of file://

**Rules not matching?**
- Wait for "NEURAL SYNCED" indicator
- Check Debug panel logs
- Lower thresholds in Debug tab

**Scripts not running?**
- Enable "Executive Script Permission" in Architect tab

See DOCUMENTATION.md troubleshooting section for more.

## 🙏 Credits

**Coded on:**
- DW Pad6S Pro (ARM64, Android 8.1)
- [Markor](https://github.com/gsantner/markor) - Text editor
- [Code Editor](https://play.google.com/store/apps/details?id=com.rhmsoft.code) - IDE

**Built with:**
- [Dexie.js](https://dexie.org/) - IndexedDB wrapper
- [Compromise](https://github.com/spencermountain/compromise) - NLP
- [Marked](https://marked.js.org/) - Markdown parser
- [Transformers.js](https://huggingface.co/docs/transformers.js) - ML inference
- [all-MiniLM-L6-v2](https://huggingface.co/sentence-transformers/all-MiniLM-L6-v2) - Embeddings

## 📜 License

**MIT License** - Use freely, attribution appreciated but not required.
---

> *"Do what thou wilt shall be the whole of the Law."*  
```

---
