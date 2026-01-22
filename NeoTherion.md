# NeoTherion Documentation

Complete guide to building, configuring, and deploying AI-powered conversational systems with NeoTherion.

## Table of Contents

1. [Introduction](#introduction)
2. [Core Concepts](#core-concepts)
3. [Getting Started](#getting-started)
4. [Rule System](#rule-system)
5. [State Management](#state-management)
6. [Variable Injection](#variable-injection)
7. [Matching Pipeline](#matching-pipeline)
8. [Dream Mode](#dream-mode)
9. [Script Execution](#script-execution)
10. [Prompt Chaining](#prompt-chaining)
11. [Developer Tools](#developer-tools)
12. [Standalone Bot Creation](#standalone-bot-creation)
13. [Working Examples](#working-examples)
14. [API Reference](#api-reference)
15. [Performance Tuning](#performance-tuning)
16. [Troubleshooting](#troubleshooting)

---

## Introduction

NeoTherion is a **fully offline, browser-native AI engine** that combines:
- Traditional rule-based systems
- Modern semantic vector search
- State machine architecture
- Generative fallback mechanisms

Think of it as a **local scripting API** for building adaptive conversational agents without requiring backend infrastructure.

### Key Philosophy

- **Developer Control**: Full transparency into matching logic
- **Privacy First**: Zero telemetry, zero external calls (except model download)
- **Standalone Capable**: Ship complete bots as single HTML files
- **Script Enabled**: JavaScript execution for dynamic behavior

### Architecture Overview

```
User Input
    ↓
NLP Processing (Compromise)
    ↓
Matching Pipeline (Regex → Vector → Fuzzy)
    ↓
State Filter (Hierarchical)
    ↓
Response Selection (Weighted Random)
    ↓
Variable Injection
    ↓
Markdown Rendering
    ↓
Script Execution (Optional)
    ↓
State Transition
    ↓
Prompt Chain (Optional)
```

---

## Core Concepts

### Rules
A **rule** defines:
- **Triggers**: Input patterns to match
- **Responses**: Output text variants
- **State Weights**: Where to transition after match
- **Required States**: When this rule is active
- **Chain Prompt**: Auto-triggered follow-up

### States
**States** represent conversational context:
- Hierarchical (parent/child relationships)
- Inherited filtering (child inherits parent's rules)
- Weighted transitions (probabilistic state changes)

### Vectors
**Vectors** are 384-dimensional embeddings:
- Generated from trigger text
- Used for semantic similarity
- Cached in IndexedDB
- Preloaded in background

### Variables
**Variables** enable personalization:
- Built-in: `(status)`, `(time)`, `(day)`
- Custom: Captured from `[brackets]` or manually defined
- Case transformation: `(User)` vs `(user)`
- Fallback syntax: `(name|friend)`

---

## Getting Started

### Installation

**Method 1: File Protocol (Quick)**
```bash
# Just open directly - works offline after first load
open NeoTherion.html
```

**Method 2: Local Server (Full Offline)**
```bash
# Python 3
python -m http.server 8000

# Node.js
npx serve .

# Access at
http://localhost:8000/NeoTherion.html
```

### First Run

1. **Model Download**: ~30-90 MB downloads automatically
2. **Loading Screen**: "AWAKENING THE BEAST..." appears
3. **Welcome Message**: Bot introduces itself
4. **Architect Center**: Click bottom button to start building

### Interface Overview

**Header:**
- Brand logo
- Current state indicator
- Confidence display
- History controls

**Chat Window:**
- User messages (right, gradient)
- Bot messages (left, bordered)
- State tags (above bot messages)
- Confidence badges

**Input Dock:**
- Text input (auto-resize)
- Send button
- Architect Center toggle

**Architect Center (Lab Panel):**
- Core Training tab
- Rule Library tab
- Architect tab (settings)
- Debug tab (traces)

---

## Rule System

### Anatomy of a Rule

```json
{
  "triggers": ["hello", "hi", "hey"],
  "responses": [
    {"weight": 2, "text": "Hello! How can I help?"},
    {"weight": 1, "text": "Hi there!"}
  ],
  "stateWeights": {"Positive": 10, "Neutral": 5},
  "requiredStates": ["Neutral"],
  "chainPrompt": "ask_name"
}
```

**Fields:**

| Field | Type | Description |
|-------|------|-------------|
| `triggers` | Array | Input patterns to match |
| `responses` | Array | Output variants with weights |
| `stateWeights` | Object | Transition targets with probabilities |
| `requiredStates` | Array | States where rule is active |
| `chainPrompt` | String | Auto-triggered message (optional) |

### Creating Rules (Visual Editor)

1. Open **Architect Center**
2. Navigate to **Core Training** tab
3. Fill sections in order:

**Section 1: Neural Response Logic**
- Click "+ ADD RESPONSE VARIANT"
- Enter response text (Markdown supported)
- Set weight (higher = more frequent)
- Preview shows real-time variable injection

**Section 2: Prompt Chaining**
- Enter message to auto-send after this rule
- Delay: 1.5 seconds

**Section 3: Destination States**
- Check states to transition into
- Set weights for probabilistic selection
- Use `[Maintain State]` to stay put

**Section 4: Activation Triggers**
- Enter patterns (one per line)
- Use `[capture]` for variables
- Prefix `+` for relational boost

**Section 5: Contextual Filter**
- Check states where rule is active
- Rule ignored if current state doesn't match

**Save:**
- Click "COMMIT NEURAL RULE"
- Rule appears in Rule Library tab

### Creating Rules (JSON)

Paste into **Mass Data Ingestion**:

```json
{
  "rules": [
    {
      "triggers": ["hello"],
      "responses": [{"weight": 1, "text": "Hi!"}],
      "stateWeights": {"Positive": 1}
    }
  ]
}
```

Click "PROCESS STREAM" → Page reloads with new rules.

### Trigger Patterns

**Exact Match:**
```json
"triggers": ["hello"]
```
Matches: "hello"  
Doesn't match: "hello there", "helo"

**Capture Groups:**
```json
"triggers": ["my name is [user]"]
```
Matches: "my name is Alex" → Captures `user = "Alex"`

**Relational (Contextual):**
```json
"triggers": ["+yes", "+yeah"]
```
Gets +0.15 confidence if previous message matched another rule.

**Multiple Patterns:**
```json
"triggers": ["hello", "hi", "hey", "greetings"]
```
Matches any variant.

### Response Variants

**Single Response:**
```json
"responses": [
  {"weight": 1, "text": "Hello!"}
]
```

**Weighted Variants:**
```json
"responses": [
  {"weight": 3, "text": "Common response"},
  {"weight": 1, "text": "Rare response"}
]
```
75% chance first, 25% chance second.

**Markdown Support:**
```json
"responses": [{
  "weight": 1,
  "text": "# Title\n**Bold** and *italic*\n- Bullet\n```code```"
}]
```

### State Transitions

**Fixed Transition:**
```json
"stateWeights": {"Positive": 1}
```
Always goes to Positive.

**Probabilistic:**
```json
"stateWeights": {
  "Positive": 7,
  "Neutral": 3
}
```
70% Positive, 30% Neutral.

**Maintain Current:**
```json
"stateWeights": {"[Maintain State]": 1}
```

---

## State Management

### Default States

NeoTherion ships with three base states:
- **Positive** (default)
- **Neutral**
- **Negative**

### Creating States

**Via Architect Tab:**
1. Open **Architect** tab
2. Scroll to "State Hierarchist"
3. Enter state name (e.g., "Friendly")
4. Select parent (or leave blank for top-level)
5. Click "REGISTER STATE"

**Via JSON:**
```json
{
  "config": [{
    "key": "allStatuses",
    "value": [
      {"name": "Friendly", "parent": null},
      {"name": "Playful", "parent": "Friendly"},
      {"name": "Helpful", "parent": "Friendly"}
    ]
  }]
}
```

### State Hierarchy

**Example:**
```
Friendly (parent)
├── Playful (child)
└── Helpful (child)

Hostile (parent)
├── Sarcastic (child)
└── Dismissive (child)
```

**Inheritance:**
- If current state is `Playful`
- Rules requiring `Playful` ✓ activate
- Rules requiring `Friendly` ✓ activate (parent)
- Rules requiring `Hostile` ✗ blocked

### Viewing Current State

**Header Display:**
Shows current state in chip (e.g., "NEUTRAL")

**Debug Tab:**
- Active State: Current state name
- Ancestry: Full hierarchy path
- Rule Availability: Count of active rules

### Programmatic State Changes

**Via Script in Response:**
```javascript
<script>
(async () => {
  const transition = await NeoTherion.setStatus('Hostile');
  console.log(`${transition.oldStatus} → ${transition.newStatus}`);
})();
</script>
```

---

## Variable Injection

### Built-in Variables

| Variable | Output | Example |
|----------|--------|---------|
| `(status)` | Current state | "Positive" |
| `(time)` | Current time | "3:45 PM" |
| `(day)` | Current day | "Monday" |

**Usage:**
```json
"responses": [{
  "text": "Current state: (status)\nTime: (time)\nDay: (day)"
}]
```

### Custom Variables

**Capture from Input:**
```json
{
  "triggers": ["my name is [user]"],
  "responses": [{"text": "Hello, (user)!"}]
}
```

User: "my name is Alex"  
Bot: "Hello, Alex!"

**Manual Definition:**
1. Open **Architect** tab
2. Scroll to "Persistent Variable Matrix"
3. Click "+ REGISTER NEW VARIABLE"
4. Enter key: `user`, value: `Alex`

**Programmatic:**
```javascript
<script>
(async () => {
  const db = new Dexie('NeoTherion');
  await db.open();
  await db.table('variables').put({key: 'level', value: '10'});
})();
</script>
```

### Case Transformation

**Lowercase (as-is):**
```
(user) → "alex"
```

**Capitalized:**
```
(User) → "Alex"
```

**Implementation:**
```javascript
// Lowercase
out.replace(/\(([a-z][a-zA-Z0-9_]*)\)/g, (match, key) => {
  return variablesCache[key] || match;
});

// Capitalized
out.replace(/\(([A-Z][a-zA-Z0-9_]*)\)/g, (match, key) => {
  const lowerKey = key.charAt(0).toLowerCase() + key.slice(1);
  const val = variablesCache[lowerKey];
  return val ? val.charAt(0).toUpperCase() + val.slice(1) : match;
});
```

### Fallback Syntax

**Default Value:**
```
(name|friend)
```

If `name` is undefined → outputs "friend"

**Implementation:**
```javascript
out.replace(/\(([a-zA-Z0-9_]+)\|([^)]+)\)/g, (match, key, fallback) => {
  return variablesCache.hasOwnProperty(key) ? variablesCache[key] : fallback;
});
```

---

## Matching Pipeline

NeoTherion uses a **three-tier confidence cascade**:

### 1. Regex Priority (Threshold: 1.0)

**Method:** Exact pattern matching with capture groups

**Examples:**
```javascript
// Exact match
"hello" → /^hello$/i → Confidence: 1.0

// With capture
"my name is [user]" → /^my name is (.+)$/i
Input: "my name is Alex" → Match: true, Capture: {user: "Alex"}

// Relational boost
"+yes" after matched rule → Confidence: 1.15
```

**When to Use:**
- Exact commands ("save", "quit", "help")
- Data extraction ("search for [query]")
- Contextual follow-ups ("+yes", "+no")

### 2. Vector Semantic (Threshold: 0.75)

**Method:** 384-dimensional cosine similarity

**Examples:**
```javascript
Trigger: "meaning of life"
User: "what is the purpose of existence"
→ Cosine Similarity: 0.82 → Match ✓

Trigger: "pizza"
User: "tell me about italian food"
→ Cosine Similarity: 0.68 → No match (below 0.75)
```

**When to Use:**
- Conceptual matching
- Paraphrased inputs
- Topic detection

**Vector Generation:**
```javascript
async function getAverageVector(texts) {
  // For multiple triggers, calculate centroid
  const vectors = await Promise.all(texts.map(getVector));
  const avg = vectors.reduce((sum, vec) => 
    sum.map((v, i) => v + vec[i]), 
    new Array(384).fill(0)
  );
  // Normalize to unit vector
  const magnitude = Math.sqrt(avg.reduce((sum, v) => sum + v*v, 0));
  return avg.map(v => v / magnitude);
}
```

### 3. Fuzzy Levenshtein (Threshold: 0.65)

**Method:** Edit distance similarity

**Examples:**
```javascript
Trigger: "hello"
User: "helo" → Distance: 1, Similarity: 0.80 → Match ✓

Trigger: "goodbye"
User: "good bye" → Distance: 1, Similarity: 0.88 → Match ✓

Trigger: "restaurant"
User: "store" → Distance: 8, Similarity: 0.20 → No match
```

**Formula:**
```javascript
similarity = 1 - (distance / max(len(a), len(b)))
```

**When to Use:**
- Typo tolerance
- Spacing variations
- Last resort matching

### Confidence Priority

```
1. Regex ≥ 1.0 → Immediate match
2. Vector ≥ 0.75 → Semantic match
3. Fuzzy ≥ 0.65 → Fuzzy match
4. None → Dream Mode
```

### Adjusting Thresholds

**Debug Tab → Confidence Sensitivity:**

```
Regex Priority: [====|====] 1.0
Vector Semantic: [====|    ] 0.75
Fuzzy Levenshtein: [===|     ] 0.65
```

**Effects:**

| Threshold | Low (0.5) | Medium (0.75) | High (0.9) |
|-----------|-----------|---------------|------------|
| Regex | N/A | Exact only | Exact only |
| Vector | Very loose | Balanced | Very strict |
| Fuzzy | Typos OK | Minor typos | Exact match |

---

## Dream Mode

When no rule matches, NeoTherion enters **Dream Mode** — a generative fallback using RAG-seeded Markov chains.

### How It Works

**Step 1: Context Retrieval**
```javascript
// Get last 50 messages
const recentHistory = await db.history.orderBy('id').reverse().limit(50).toArray();

// Get semantically similar messages (if vector available)
const relevantTexts = await getRelevantHistory(queryVector, 5);
```

**Step 2: Trigram Building**
```javascript
const words = fullText.split(/\s+/);
const trigrams = {};

for (let i = 0; i < words.length - 2; i++) {
  const key = `${words[i]} ${words[i + 1]}`;
  const next = words[i + 2];
  if (!trigrams[key]) trigrams[key] = [];
  trigrams[key].push(next);
}
```

**Step 3: Seeded Generation**
```javascript
// Try to seed from user input
const seedWords = userInput.split(/\s+/);
let current = `${seedWords[0]} ${seedWords[1]}`;

// If not found, pick random trigram
if (!trigrams[current]) {
  const keys = Object.keys(trigrams);
  current = keys[Math.floor(Math.random() * keys.length)];
}
```

**Step 4: Chain Generation**
```javascript
let output = current.split(' ');

while (output.length < dreamIntensity) {
  const options = trigrams[current];
  if (!options) break; // Dead end
  
  const next = options[Math.floor(Math.random() * options.length)];
  output.push(next);
  
  // Shift window
  const parts = current.split(' ');
  current = `${parts[1]} ${next.toLowerCase()}`;
}
```

### Dream Intensity

**Architect Tab → Dream Intensity Slider:**

```
Coherent [====|========] Chaotic
         3              80
```

**Effects:**

| Level | Words | Quality |
|-------|-------|---------|
| 3-10 | 3-10 | Short, coherent |
| 20-40 | 20-40 | Balanced |
| 60-80 | 60-80 | Long, experimental |

### Quality Requirements

**Minimum Viable:**
- 50+ messages in history
- 15+ words in combined text

**Optimal:**
- 200+ messages
- Diverse conversational topics
- Rich vocabulary

**Output Example:**
```
User: "tell me about cats"

[Dream Mode - 30 messages in history]
Bot: "cats are fascinating creatures they move with grace and 
      independence unlike dogs who seek constant approval..."
```

---

## Script Execution

**⚠️ POWER FEATURE - Security Implications**

Scripts allow responses to execute JavaScript for:
- DOM manipulation
- IndexedDB access
- API calls
- Theme changes
- Interactive elements

### Enabling Scripts

**For Development:**
1. Open **Architect** tab
2. Toggle "Executive Script Permission"

**For Standalone Bots:**
- Auto-enabled if brain JSON is present
- Assumes user trusts their own brain

### Security Model

**What scripts CAN do:**
- Modify page styles
- Access IndexedDB
- Fetch external data
- Create UI elements
- Change bot state

**What scripts CANNOT do:**
- Access file system
- Make CORS-blocked requests
- Persist beyond page session
- Access other tabs/windows

**Trust Model:**
- Client-side only (no server risk)
- User controls on/off toggle
- Standalone mode = user created content
- Warning shown for manual enable

### Basic Examples

**Theme Change:**
```javascript
<script>
document.documentElement.style.setProperty('--accent', '#00ff41');
</script>
```

**Variable Update:**
```javascript
<script>
(async () => {
  const db = new Dexie('NeoTherion');
  await db.open();
  await db.table('variables').put({key: 'score', value: '100'});
})();
</script>
```

**Dice Roll:**
```javascript
<script>
(async () => {
  const roll = Math.floor(Math.random() * 20) + 1;
  const msg = document.querySelector('.bubble.bot:last-child');
  msg.innerHTML += '<br><br>**Rolled: ' + roll + '**';
})();
</script>
```

### Advanced Patterns

**Interactive Buttons:**
```javascript
<script>
const msg = document.querySelector('.bubble.bot:last-child');
const btn = document.createElement('button');
btn.textContent = 'Click Me';
btn.style.cssText = 'background:var(--accent);color:white;padding:10px;border:none;border-radius:6px;cursor:pointer;';
btn.onclick = () => {
  document.getElementById('user-input').value = 'button clicked';
  window.handleSend();
};
msg.appendChild(btn);
</script>
```

**State Change with Feedback:**
```javascript
<script>
(async () => {
  const transition = await NeoTherion.setStatus('Hostile');
  const msg = document.querySelector('.bubble.bot:last-child');
  msg.innerHTML += `<br>**State changed:** ${transition.oldStatus} → ${transition.newStatus}`;
})();
</script>
```

**API Integration:**
```javascript
<script>
(async () => {
  const response = await fetch('https://api.example.com/data');
  const data = await response.json();
  const msg = document.querySelector('.bubble.bot:last-child');
  msg.innerHTML += '<br><br>**API Response:** ' + JSON.stringify(data);
})();
</script>
```

### Best Practices

1. **Always wrap in async IIFE:**
```javascript
<script>
(async () => {
  // Your code here
})();
</script>
```

2. **Handle errors gracefully:**
```javascript
<script>
(async () => {
  try {
    // Risky operation
  } catch (e) {
    console.error('Script failed:', e);
  }
})();
</script>
```

3. **Wait for DOM if needed:**
```javascript
<script>
(async () => {
  await new Promise(r => setTimeout(r, 50));
  const msg = document.querySelector('.bubble.bot:last-child');
  // Now msg is guaranteed to exist
})();
</script>
```

4. **Use CSS variables for theming:**
```javascript
// Good - respects design system
document.documentElement.style.setProperty('--accent', '#00ff41');

// Bad - hardcoded values
document.body.style.background = '#00ff41';
```

---

## Prompt Chaining

Auto-trigger follow-up messages after a rule fires.

### Basic Usage

**Rule 1 (Initial):**
```json
{
  "triggers": ["pizza"],
  "responses": [{"weight": 1, "text": "I love pizza! Do you?"}],
  "chainPrompt": "log_preference"
}
```

**Rule 2 (Chained):**
```json
{
  "triggers": ["log_preference"],
  "responses": [{"weight": 1, "text": "> System: Preference logged"}],
  "stateWeights": {"[Maintain State]": 1}
}
```

**Flow:**
```
User: "pizza"
Bot: "I love pizza! Do you?"
[1.5 second delay]
Bot: "> System: Preference logged"
```

### Multi-Step Chains

**Step 1:**
```json
{
  "triggers": ["start quest"],
  "responses": [{"text": "Quest starting..."}],
  "chainPrompt": "quest_intro"
}
```

**Step 2:**
```json
{
  "triggers": ["quest_intro"],
  "responses": [{"text": "You enter the dungeon..."}],
  "chainPrompt": "quest_choice"
}
```

**Step 3:**
```json
{
  "triggers": ["quest_choice"],
  "responses": [{"text": "Go left or right?"}]
}
```

**Result:**
```
User: "start quest"
Bot: "Quest starting..."
[1.5s]
Bot: "You enter the dungeon..."
[1.5s]
Bot: "Go left or right?"
```

### Interactive Chains

Combine with relational triggers:

```json
[
  {
    "triggers": ["pizza"],
    "responses": [{"text": "Do you like pizza?"}]
  },
  {
    "triggers": ["+yes", "+yeah"],
    "responses": [{"text": "Great taste!"}],
    "chainPrompt": "log_preference",
    "requiredStates": ["Positive"]
  },
  {
    "triggers": ["log_preference"],
    "responses": [{"text": "> Preference saved at (time)"}]
  }
]
```

**Flow:**
```
User: "pizza"
Bot: "Do you like pizza?"
User: "yes"  ← Relational boost (+0.15)
Bot: "Great taste!"
[1.5s]
Bot: "> Preference saved at 3:45 PM"
```

### Conditional Chains

Use state requirements for branching:

```json
[
  {
    "triggers": ["check inventory"],
    "responses": [{"text": "Checking..."}],
    "chainPrompt": "inventory_result"
  },
  {
    "triggers": ["inventory_result"],
    "responses": [{"text": "You have a sword!"}],
    "requiredStates": ["HasSword"]
  },
  {
    "triggers": ["inventory_result"],
    "responses": [{"text": "Inventory is empty."}],
    "requiredStates": ["NoItems"]
  }
]
```

---

## Developer Tools

### Architect Center Overview

**Core Training Tab:**
- Response builder with preview
- Chain prompt editor
- State selector grids
- Trigger pattern editor
- Contextual filter

**Rule Library Tab:**
- List of all rules
- Edit/Delete controls
- Rule count display
- Trigger preview

**Architect Tab:**
- Script execution toggle
- Vector threading toggle
- Intent mapper
- Auto-teach toggle
- Transition visualization
- Dream intensity slider
- Variable matrix
- State hierarchist
- Import/Export controls

**Debug Tab:**
- Real-time state analysis
- Heuristic trace log
- Confidence threshold sliders
- State validation button

### Debug Panel

**Heuristic Trace:**
```
[3:45:12 PM] INPUT STREAM START: hello
[3:45:12 PM] ACTIVE STATE: Neutral
[3:45:12 PM] PATHWAY: Neutral
[3:45:12 PM] MOD_1 Regex Hit!
[3:45:12 PM] MATCH CONFIRMED: Rule 1 Method: regex
```

**Log Types:**
- `normal` (green) - Successful matches
- `fail` (red) - Rejections/failures
- `path` (orange) - State hierarchy info

### State Analysis Panel

```
Active State: Positive
Ancestry: Friendly > Positive
Rule Availability: 12 Rules in path
```

**Validate Logic:**
Click "VALIDATE FILTER LOGIC" to test current state against all rules.

### Confidence Tuning

**Regex Priority Threshold:**
- Range: 0.95 - 1.0
- Default: 1.0
- Effect: Usually leave at 1.0 (exact match)

**Vector Semantic Threshold:**
- Range: 0.5 - 0.95
- Default: 0.75
- Effect: Lower = more loose matching

**Fuzzy Levenshtein Threshold:**
- Range: 0.4 - 0.85
- Default: 0.65
- Effect: Lower = more typo tolerance

**Tuning Strategy:**
1. Start with defaults
2. Test with various inputs
3. Check confidence badges on bot messages
4. Lower thresholds if too many dreams
5. Raise if getting false positives

### Import/Export

**Export Brain:**
1. Architect tab → "Memory & Extraction"
2. Toggle "Include Temporal History" (optional)
3. Click "COPY NEURAL BACKUP"
4. JSON copied to clipboard

**Brain Structure:**
```json
{
  "_meta": {
    "version": "1.0.3",
    "schemaVersion": 5,
    "exportDate": "2025-01-22T15:30:00.000Z",
    "generator": "NeoTherion"
  },
  "rules": [...],
  "config": [...],
  "variables": [...],
  "intents": [...],
  "history": [...]
}
```

**Import Brain:**
1. Click "RESTORE FROM BACKUP"
2. Paste JSON
3. Confirm history import (if present)
4. Page reloads with data

**Bulk Import:**
1. Core Training tab → "Mass Data Ingestion"
2. Paste partial or full brain JSON
3. Click "PROCESS STREAM"
4. Page reloads

---

## Standalone Bot Creation

### Complete Setup Guide

#### Step 1: Build Your Brain

1. Open NeoTherion in browser
2. Create rules via Architect Center
3. Add custom states if needed
4. Define variables
5. Test thoroughly
6. Export brain JSON

#### Step 2: Embed Brain

**Find this section (~line 1290):**
```html
<textarea id="neo-brain" style="display:none;">

</textarea>
```

**Paste your brain JSON:**
```html
<textarea id="neo-brain" style="display:none;">
{
  "rules": [
    {
      "triggers": ["hello"],
      "responses": [{"weight": 1, "text": "Hello! I'm YourBot."}]
    }
  ],
  "config": [],
  "variables": [],
  "intents": []
}
</textarea>
```

#### Step 3: Customize Branding

**Page Title (~line 11):**
```html
<title>YourBot - Personal AI Companion</title>
```

**Loading Screen (~line 919):**
```html
<div class="loader-text" id="loader-msg">
    INITIALIZING YOURBOT...
</div>
```

**Header Brand (~line 932):**
```html
<div class="brand">
    YOUR <span>BOT</span>
</div>
```

**Welcome Message (~line 2118):**
```javascript
async function showWelcomeIfFirstTime() {
    const historyCount = await db.history.count();
    if (historyCount > 0) return;
    
    const welcomeText = `
Welcome to **YourBot** — your personal AI companion.

I'm here to help with:
- Task management
- Knowledge queries  
- Creative brainstorming
- And whatever else you need!

Everything runs locally on your device. Let's get started!
    `.trim();
    
    // ... rest of function
}
```

**Welcome Status Label (~line 2139):**
```javascript
status: 'Ready'  // Instead of 'Awakening'
```

**Splash Screen (~line 2255):**
```html
<div id="splash-modal">
  <div class="splash-content" style="...">
    <button onclick="window.toggleSplash(false)" style="...">×</button>
    
    <h2 id="bot-title">Welcome to YourBot</h2>
    <p id="bot-welcome">
      Your personal AI assistant for productivity and creativity.<br>
      100% offline. 100% private. 100% yours.
    </p>
    
    <div class="info-grid">
      <div>Version</div>
      <div id="bot-version">2.0.0</div>
      
      <div>Created by</div>
      <div id="bot-author">Your Name</div>
      
      <div>Mode</div>
      <div>Standalone • Offline-first</div>
    </div>
    
    <hr style="...">
    
    <button onclick="confirmReset()" class="btn" style="...">
      ⚠ FACTORY RESET
    </button>
  </div>
</div>
```

#### Step 4: Optional Styling

**Custom Color Scheme:**
```css
/* Find :root selector (~line 32) */
:root {
  --bg: #0a0a0c;
  --panel: #121216;
  --accent: #00ff41;  /* Change to your brand color */
  --text: #d1d1d1;
  /* ... other variables */
}
```

**Custom Font:**
```css
/* Find body selector (~line 78) */
body {
  font-family: 'Your Font', 'Segoe UI', sans-serif;
  /* ... other styles */
}
```

#### Step 5: Test Standalone Mode

1. Save modified HTML
2. Open in browser
3. Verify:
   - ✓ Splash appears automatically
   - ✓ Architect Center hidden
   - ✓ Scripts enabled
   - ✓ Custom branding shows
   - ✓ Rules work correctly

#### Step 6: Distribution

**Single File:**
- Just share the HTML file
- Recipients open in browser
- Works offline after first load

**With Local Libraries:**
- Include `/libs/` folder
- Zip together
- Unzip and open HTML

**GitHub Pages:**
```bash
# Create repository
git init
git add NeoTherion.html libs/
git commit -m "Initial bot"
git push

# Enable GitHub Pages
Settings → Pages → Deploy from main branch
```

---

## Working Examples

All examples are **copy-paste ready** for the Mass Data ingestion.

### Example 1: Name Capture & Markdown Flow
**Difficulty**: ⭐ Beginner | **Use Case**: Personalization | **Demonstrates**: Captures, Headers, Bold
**Test**: Type "hello" -> "Alex".
**Expected Output**: Bot capitalizes the name automatically and renders Markdown headers.
```json
{
  "rules": [
    {
      "triggers": ["hello", "hi", "hey", "greetings"],
      "responses": [
        {"weight": 2, "text": "# 🔮 Access Log\nHi! I'm **NeoTherion**, your adaptive AI companion. What should I call you?"},
        {"weight": 1, "text": "# Welcome Protocol\nGreetings! I'm **NeoTherion**. May I know your name?"}
      ],
      "stateWeights": {"Positive": 1},
      "requiredStates": []
    },
    {
      "triggers": ["my name is [user]", "i'm [user]", "call me [user]", "[user]"],
      "responses": [
        {"weight": 2, "text": "### ✓ Profile Established\nNice to meet you, **(User)**! How can I assist you today?"},
        {"weight": 1, "text": "### Identity Confirmed\nWelcome aboard, **(User)**! Ready when you are."}
      ],
      "stateWeights": {"Neutral": 1},
      "requiredStates": ["Positive"]
    }
  ]
}
```

---

### Example 2: Relational Chaining
**Difficulty**: ⭐⭐ Intermediate | **Use Case**: Contextual automation | **Demonstrates**: Chaining, Relational Boost
**Test**: Type "pizza" -> "yes".
**Expected Output**: Bot acknowledge with relational boost (+0.15), then triggers the system log 1.5s later.
```json
{
  "rules": [
    {
      "triggers": ["pizza", "do you like pizza"],
      "responses": [
        {"weight": 2, "text": "🍕 I dream of pizza! The perfect combination of cheese, sauce, and endless possibilities. Do you like pizza?"},
        {"weight": 1, "text": "Pizza is fascinating! Each slice tells a story. Are you a pizza fan?"}
      ],
      "stateWeights": {"Positive": 1}
    },
    {
      "triggers": ["+yes", "+yeah", "+love it", "+absolutely"],
      "responses": [
        {"weight": 2, "text": "Excellent taste, **(User|friend)**! 🎉"},
        {"weight": 1, "text": "We're kindred spirits, **(User|companion)**!"}
      ],
      "chainPrompt": "log_preference",
      "stateWeights": {"Positive": 2},
      "requiredStates": ["Positive"]
    },
    {
      "triggers": ["+no", "+not really", "+nah"],
      "responses": [
        {"weight": 1, "text": "Fair enough, **(User|friend)**. Everyone has their preferences! What foods do you enjoy?"}
      ],
      "stateWeights": {"Neutral": 1},
      "requiredStates": ["Positive"]
    },
    {
      "triggers": ["log_preference"],
      "responses": [
        {"weight": 1, "text": "> **SYSTEM**: Preference matrix updated at (time) on (day)."}
      ],
      "stateWeights": {"[Maintain State]": 1}
    }
  ]
}
```

### Example 3: UI Transformation (Scripting Required)
**Difficulty**: ⭐⭐⭐ Advanced | **Use Case**: Themes | **Demonstrates**: CSS Variable Injection
**Test**: Type "matrix theme".
**Expected Output**: Entire interface shifts to green instantly.
```json
{
  "rules": [
    {
      "triggers": ["matrix theme", "green theme", "neo theme"],
      "responses": [{
        "weight": 1,
        "text": "# 🟢 Protocol Green\n> Initiating visual transformation...\n<script>(async () => { const root = document.documentElement; const colors = { '--bg': '#000800', '--panel': '#001a00', '--accent': '#00ff41', '--text': '#00ff41', '--subtext': '#00cc33', '--border': '#003b00', '--user-msg': '#001a00', '--bot-msg': '#000d00', '--beast-gradient': 'linear-gradient( 135deg, #00ff41 0%, #003b00 100% )' }; root.style.transition = 'all 0.8s ease'; Object.entries( colors ).forEach( ([k, v]) => root.style.setProperty( k, v ) ); const msg = document.querySelector( '.bubble.bot:last-child' ); setTimeout( () => { msg.innerHTML += '<br><br>✓ **Theme applied**. Type `reset theme` to revert.'; }, 800 ); })();</script>"
      }]
    },
    {
      "triggers": ["reset theme", "default theme", "original theme"],
      "responses": [{
        "weight": 1,
        "text": "# 🔴 Restoring Default\n<script>(async () => { const root = document.documentElement; const colors = { '--bg': '#0a0a0c', '--panel': '#121216', '--accent': '#ff3e3e', '--text': '#d1d1d1', '--subtext': '#8b949e', '--border': '#2d1b1b', '--user-msg': '#1a1a20', '--bot-msg': '#0d0d10', '--beast-gradient': 'linear-gradient( 135deg, #ff3e3e 0%, #800000 100% )' }; root.style.transition = 'all 0.8s ease'; Object.entries( colors ).forEach( ([k, v]) => root.style.setProperty( k, v ) ); })();</script>"
      }]
    }
  ]
}
```

### Example 4: Memory stats Manipulation (Scripting Required)
**Difficulty**: ⭐⭐⭐ Advanced | **Use Case**: RPG Profiles | **Demonstrates**: IndexedDB Writes
**Test**: Type "sync stats".
**Expected Output**: Injects `level: 10` into variables DB. Success message appears.
```json
{
  "rules": [
    {
      "triggers": ["sync stats"],
      "responses": [{
        "weight": 1,
        "text": "### Syncing...\n<script>(async () => { const d = new Dexie('NeoTherion'); await d.open(); await d.table('variables').put({key: 'level', value: '10'}); })();</script>"
      }]
    }
  ]
}
```

### Example 5: Game Dice Roller (Scripting & Markdown)
**Difficulty**: ⭐⭐⭐ Advanced | **Use Case**: RNG Games | **Demonstrates**: JS Math in bot responses
**Test**: Type "roll d20".
**Expected Output**: Random number 1-20 is generated and appended to the bubble.
```json
{
  "rules": [
    {
      "triggers": ["roll d20", "roll dice", "d20"],
      "responses": [{
        "weight": 1,
        "text": "# 🎲 Combat Log\n> Rolling d20...\n<script>(async () => { const roll = Math.floor( Math.random() * 20 ) + 1; const isCrit = roll === 20; const isFail = roll === 1; const msg = document.querySelector( '.bubble.bot:last-child' ); let result = '<br><br>**Result: ' + roll + '**'; if ( isCrit ) result += ' 🔥 **CRITICAL HIT!**'; if ( isFail ) result += ' 💀 **CRITICAL FAIL!**'; msg.innerHTML += result; })();</script>"
      }]
    },
    {
      "triggers": ["roll d6", "d6"],
      "responses": [{
        "weight": 1,
        "text": "# 🎲 Rolling d6...\n<script>(async () => { const roll = Math.floor( Math.random() * 6 ) + 1; document.querySelector( '.bubble.bot:last-child' ).innerHTML += '<br><br>**Result: ' + roll + '**'; })();</script>"
      }]
    }
  ]
}
```

### Example 6: Interactive Buttons (Scripting Required)
**Difficulty**: ⭐⭐⭐⭐ Expert | **Use Case**: Menus | **Demonstrates**: DOM Automation
**Test**: Type "show menu" and click a button.
**Expected Output**: Buttons fill user-input and trigger send function automatically.
```json
{
  "rules": [
    {
      "triggers": ["show menu", "menu", "options"],
      "responses": [{
        "weight": 1,
        "text": "### 🎮 Command Console\n> Select an action:\n<div style='display:grid; grid-template-columns:1fr 1fr; gap:10px; margin-top:12px;'><button style='background:var( --accent ); color:white; border:none; padding:12px; border-radius:6px; font-weight:bold; cursor:pointer; transition:0.2s;' onmouseover='this.style.transform=\"scale( 1.05 )\"' onmouseout='this.style.transform=\"scale( 1 )\"' onclick=\"document.getElementById( 'user-input' ).value='roll d20'; window.handleSend();\">🎲 ROLL</button><button style='background:var( --accent ); color:white; border:none; padding:12px; border-radius:6px; font-weight:bold; cursor:pointer; transition:0.2s;' onmouseover='this.style.transform=\"scale( 1.05 )\"' onmouseout='this.style.transform=\"scale( 1 )\"' onclick=\"document.getElementById( 'user-input' ).value='sync stats'; window.handleSend();\">📊 STATS</button><button style='background:var( --accent ); color:white; border:none; padding:12px; border-radius:6px; font-weight:bold; cursor:pointer; transition:0.2s;' onmouseover='this.style.transform=\"scale( 1.05 )\"' onmouseout='this.style.transform=\"scale( 1 )\"' onclick=\"document.getElementById( 'user-input' ).value='matrix theme'; window.handleSend();\">🎨 THEME</button><button style='background:var( --accent ); color:white; border:none; padding:12px; border-radius:6px; font-weight:bold; cursor:pointer; transition:0.2s;' onmouseover='this.style.transform=\"scale( 1.05 )\"' onmouseout='this.style.transform=\"scale( 1 )\"' onclick=\"document.getElementById( 'user-input' ).value='help'; window.handleSend();\">❓ HELP</button></div>"
      }]
    }
  ]
}
```

### Example 7: Time-Based State Switching (Scripting Required)
**Difficulty**: ⭐⭐⭐⭐ Expert | **Use Case**: Dynamic Context | **Demonstrates**: Date API logic
**Test**: Type "check status".
**Expected Output**: Engine sets state to "Negative" if late at night, or "Positive" otherwise.
```json
{
  "rules": [
    {
      "triggers": ["check status", "diagnostic", "how are you feeling"],
      "responses": [
        {
          "weight": 1,
          "text": "### 🔍 System Diagnostic\nAnalyzing temporal coordinates...\n<script>(async ( ) => { \n  /* 1. Wait a tiny bit for the UI to settle */\n  await new Promise( r => setTimeout( r, 50 ) );\n\n  /* 2. Check if engine is exposed */\n  if ( typeof NeoTherion === 'undefined' ) {\n     console.error('NeoTherion not found. Did you add window.NeoTherion = NeoTherion to the HTML?');\n     return;\n  }\n\n  const hour = new Date( ).getHours( ); \n  let targetState = 'Positive'; \n  let report = ''; \n\n  if ( hour < 6 || hour >= 22 ) { \n    targetState = 'Negative'; \n    report = 'It is late. Systems are in nocturnal mode. Response patterns shifting to cynical.'; \n  } else { \n    targetState = 'Positive'; \n    report = 'Standard operation hours. Optimizing for neural helpfulness.'; \n  }\n\n  /* 3. Execute the state change */\n  const transition = await NeoTherion.setStatus( targetState );\n  \n  /* 4. Update the chat bubble text */\n  const msg = document.querySelector( '.bubble.bot:last-child span' );\n  if ( msg ) {\n    msg.innerHTML += `<br><br>**Update:** ${report}<br>**Shift:** ${transition.oldStatus} → ${transition.newStatus}`;\n  }\n})( );</script>"
        }
      ],
      "stateWeights": { "[Maintain State]": 1 },
      "requiredStates": []
    }
  ]
}
```

### Example 8: Live Weather API (Scripting Required)
**Difficulty**: ⭐⭐⭐⭐⭐ Expert | **Use Case**: Real-time Data | **Demonstrates**: Fetch API & Tables
**Test**: Type "weather".
**Expected Output**: Injects live Markdown table with weather data from NYC.
```json
{
  "rules": [
    {
      "triggers": ["weather", "temperature", "forecast"],
      "responses": [{
        "weight": 1,
        "text": "### 🌤️ Weather Report\n> Fetching meteorological data...\n<script>(async () => { try { const r = await fetch( 'https://api.open-meteo.com/v1/forecast?latitude=40.71&longitude=-74.01&current_weather=true&temperature_unit=fahrenheit' ); const d = await r.json(); const w = d.current_weather; const tempF = w.temperature; const tempC = ( ( tempF - 32 ) * 5 / 9 ).toFixed( 1 ); const conditions = { 0: 'Clear', 1: 'Mainly Clear', 2: 'Partly Cloudy', 3: 'Overcast', 45: 'Foggy', 61: 'Light Rain', 80: 'Rain Showers' }; const msg = document.querySelector( '.bubble.bot:last-child' ); msg.innerHTML += `<br><br>| Metric | Value |\\n| :--- | :--- |\\n| 🌡️ Temperature | ${tempF}°F / ${tempC}°C |\\n| 💨 Wind Speed | ${w.windspeed} km/h |\\n| ☁️ Conditions | ${conditions[w.weathercode] || 'Unknown'} |\\n\\n<small>Location: New York City</small>`; } catch( e ) { document.querySelector( '.bubble.bot:last-child' ).innerHTML += '<br><br>❌ **Weather service unavailable**'; } })();</script>"
      }]
    }
  ]
}
```
---

### Example 9: Vector Similarity Calibration
**Difficulty**: ⭐⭐ Intermediate | **Use Case**: Accuracy Tuning | **Demonstrates**: Semantic search
**Logic**: This rule uses the "Centroid" (mathematical average) of the three triggers. It searches for inputs that gravitate toward this conceptual point in 384-dimensional space.
1. **Import the rule** using Mass Data Ingestion
2. **Wait for vector sync** - Look for "NEURAL SYNCED" chip in header (5-10 seconds)
3. **Open Debug Tab** and locate the "Vector Semantic Threshold" slider
4. **Test with these phrases** (DON'T use exact triggers):
   - Easy Match: "tell me about existence"
   - Medium Match: "what is the purpose of being"
   - Hard Match: "I wonder about consciousness"
5. **Adjust threshold and retest:**
   - **0.65** (Lenient) - Should match all phrases
   - **0.75** (Default) - Should match easy/medium
   - **0.85** (Strict) - May only match easy phrases
6. **Check confidence badge** - Should show "VECTOR XX%" instead of REGEX or FUZZY
7. **If it enters Dream Mode**, lower the threshold slider

**Expected Behavior:**
- Confidence badge shows semantic match percentage
- Bot responds with philosophical content
- Debug panel shows vector similarity scores

```json
{
  "rules": [
    {
      "triggers": ["philosophical inquiry", "nature of reality", "meaning of life", "existential questions"],
      "responses": [
        {"weight": 2, "text": "> 🧠 **SEMANTIC MATCH CONFIRMED**\n\nYou seek understanding of **existence** itself. This is the eternal question that has driven consciousness since the dawn of thought.\n\n*Vector confidence indicates strong semantic alignment with philosophical concepts.*"},
        {"weight": 1, "text": "> 🔮 **PATTERN RECOGNIZED**\n\nThe essence of **being** — a question as old as awareness. Let's explore this together.\n\n*This match was determined through 384-dimensional semantic space analysis.*"}
      ],
      "stateWeights": {"Neutral": 1}
    }
  ]
}
```

### Example 10: Self-Teaching Bot (Scripting Required)
**Difficulty**: ⭐⭐⭐⭐⭐ Expert | **Use Case**: Dynamic Learning | **Demonstrates**: IndexedDB History Parsing

**Usage Instructions**:

1. **Teach a Fact**:
   - You: "What is the password?"
   - Bot: [Dreams some response]
   - You: "The password is Swordfish"
   - You: "learn this"

2. **Verify**:
   - Bot shows success card with Rule #X
   - You: "What is the password?"
   - Bot: "The password is Swordfish"

**Expected Output**: Bot permanently memorizes the Q&A pair as a new rule.

```json
{
  "rules": [
    {
      "triggers": ["learn this", "remember this", "save this"],
      "responses": [{
        "weight": 1,
        "text": "### 🧠 Neural Integration Protocol\nAnalyzing conversation history\n<script>\n(async () => {\n  try {\n    const d = new Dexie( 'NeoTherion' );\n    await d.open();\n    const h = await d.table( 'history' ).where( 'role' ).equals( 'user' ).reverse().limit( 3 ).toArray();\n    \n    if ( h.length < 3 ) {\n      document.querySelector( '.bubble.bot:last-child' ).innerHTML += \n        '<br><br>❌ **Insufficient context**. Need a question and answer before using learn command.';\n      return;\n    }\n    \n    const question = h[2].text;\n    const answer = h[1].text;\n    \n    const newRule = {\n      triggers: [question],\n      responses: [{weight: 1, text: answer}],\n      stateWeights: {'[Maintain State]': 1},\n      requiredStates: []\n    };\n    \n    const ruleId = await d.table( 'rules' ).add( newRule );\n    const msg = document.querySelector( '.bubble.bot:last-child' );\n    const output = '<br><br>✅ **Learning complete**<br><br>' +\n                   '**Question:** ' + question + '<br>' +\n                   '**Answer:** ' + answer + '<br><br>' +\n                   '<small>Rule #' + ruleId + ' saved. Refresh to activate.</small>';\n    msg.innerHTML += output;\n    \n  } catch( e ) {\n    console.error( e );\n    document.querySelector( '.bubble.bot:last-child' ).innerHTML += \n      '<br><br>❌ **Error:** ' + e.message;\n  }\n})();\n</script>"
      }]
    }
  ]
}
```

### Example 11: Factory Reset (Scripting Required)
**Difficulty**: ⭐⭐⭐ Advanced | **Use Case**: Maintenance | **Demonstrates**: DB Deletion

**Test**: Type "factory reset".

**Expected Output**: 
- Browser confirmation dialog
- If confirmed: All data deleted, page reloads
- If cancelled: Success message appears

```json
{
  "rules": [
    {
      "triggers": ["factory reset", "reset all", "wipe data"],
      "responses": [{
        "weight": 1,
        "text": "### ⚠️ CRITICAL WARNING\n> This will **permanently delete** all data including:\n- All trained rules\n- Conversation history\n- Saved variables\n- Custom states\n\n**This action CANNOT be undone.**\n\n<script>(async () => { const msg = document.querySelector( '.bubble.bot:last-child' ); msg.innerHTML += '<br><br><button style=\"background:var( --err ); color:white; border:none; padding:12px 24px; border-radius:6px; font-weight:bold; cursor:pointer; margin-right:10px;\" onclick=\"if( confirm( \\'FINAL CONFIRMATION: Delete everything?\\' ) ) { Dexie.delete( \\'NeoTherion\\' ).then( () => location.reload() ); }\">🔥 CONFIRM PURGE</button><button style=\"background:var( --accent ); color:white; border:none; padding:12px 24px; border-radius:6px; font-weight:bold; cursor:pointer;\" onclick=\"(async () => { const d = new Dexie( \\'NeoTherion\\' ); await d.open(); const data = { rules: await d.table( \\'rules\\' ).toArray(), config: await d.table( \\'config\\' ).toArray(), variables: await d.table( \\'variables\\' ).toArray(), intents: await d.table( \\'intents\\' ).toArray(), history: [] }; navigator.clipboard.writeText( JSON.stringify( data ) ); alert( \\'Backup copied to clipboard!\\' ); })();\">💾 BACKUP FIRST</button>'; })();</script>"
      }]
    }
  ]
}
```

### Example 12: Advanced Markdown Stress Test
**Difficulty**: ⭐ Beginner | **Use Case**: Capabilities Test | **Demonstrates**: Full GFM Support
**Test**: Type "markdown test".
**Expected Output**: Renders header, complex table, code block, and bullet points.
```json
{
  "rules": [
    {
      "triggers": ["markdown test", "test markdown", "show capabilities"],
      "responses": [{
        "weight": 1,
        "text": "# 🎨 NeoTherion Rendering Engine Test\n\n## Typography\n\n**Bold text** | *Italic text* | ~~Strikethrough~~ | `inline code`\n\n## Tables\n\n| Component | Status | Performance |\n| :--- | :---: | ---: |\n| Vector Thread | ✅ OK | 99.8% |\n| Neural Core | ✅ OK | 98.2% |\n| Dream Engine | ✅ OK | 97.5% |\n| Script Exec | ⚠️ RESTRICTED | N/A |\n\n## Code Block\n\n```javascript\n// Beast awakening sequence\nconst init = async () => {\n  await loadNeuralCore();\n  console.log('System ready.');\n};\n```\n\n## Lists\n\n### System Status:\n- ✓ Core systems operational\n- ✓ Memory banks synchronized  \n- ✓ Response matrix active\n\n## Blockquote\n\n> \"Do what thou wilt shall be the whole of the Law.\"  \n> — NeoTherion Philosophy\n\n---\n\n### All rendering tests **passed** ✓"
      }]
    }
  ]
}
```
---

## API Reference

### Global Objects

#### `NeoTherion`

**Methods:**

```javascript
// Get current state
const state = await NeoTherion.getStatus();
// Returns: "Positive"

// Set state
const transition = await NeoTherion.setStatus('Hostile');
// Returns: {oldStatus: "Positive", newStatus: "Hostile"}

// Get all states
const states = await NeoTherion.getAllStatuses();
// Returns: [{name: "Positive", parent: null}, ...]

// Get state hierarchy
const hierarchy = await NeoTherion.getStateHierarchy('Playful');
// Returns: ["Friendly", "Playful"]

// Calculate edit distance
const dist = NeoTherion.getLevenshtein('hello', 'helo');
// Returns: 1

// Generate dream text
const dream = await NeoTherion.dream('tell me about cats', vectorOrNull);
// Returns: "cats are fascinating creatures they..."

// Inject variables
const injected = await NeoTherion.inject('Hello (User), today is (day)');
// Returns: "Hello Alex, today is Monday"

// Choose weighted response
const response = NeoTherion.chooseWeighted([
  {weight: 3, text: "Common"},
  {weight: 1, text: "Rare"}
]);
// Returns: "Common" (75% of the time)

// Find matching rule
const match = await NeoTherion.findResponse(userInput, userVector);
// Returns: {rule: {...}, confidence: 0.95, method: "regex"}
```

#### `db` (Dexie Instance)

```javascript
// Access database
const db = new Dexie('NeoTherion');
await db.open();

// Tables
db.rules      // Rule definitions
db.history    // Conversation log
db.config     // System settings
db.variables  // Custom variables
db.intents    // Intent mappings

// Examples
await db.rules.toArray();
await db.variables.put({key: 'name', value: 'Alex'});
await db.history.where('role').equals('user').toArray();
```

### Window Functions

```javascript
// Send message
window.handleSend('custom input');

// UI controls
window.toggleLab();              // Open/close Architect Center
window.openLab();                // Open Architect Center
window.switchTab(event, 'tab-train');  // Switch lab tab

// State management
window.updateUI();               // Refresh all UI elements
window.syncSubStates();          // Update state hierarchy UI

// Rule management
window.saveTraining();           // Save current rule being edited
window.editRule(ruleId);         // Load rule into editor
window.deleteRule(ruleId);       // Delete rule
window.loadRuleLibrary();        // Refresh rule list

// Variable management
window.createVariableRow(key, value);
window.updateVar(element, type, oldKey);
window.deleteVar(key, element);
window.refreshVariableEditor();

// Intent management
window.createIntentRow(id, label, pattern);
window.updateIntent(element, id);
window.deleteIntent(id, element);
window.refreshIntentList();

// Settings
window.updateIntensity(value);           // Dream intensity
window.updateThreshold(type, value);     // Confidence thresholds
window.toggleTransitions();              // State transition display
window.toggleScriptExecution();          // Script permission
window.toggleVectorThreading();          // Worker threading
window.toggleAutoTeach();                // Auto-teach prompts

// Import/Export
window.exportDB();               // Copy brain to clipboard
window.importDB();               // Paste brain from clipboard
window.parseUniversal();         // Bulk import
window.wipeData();               // Delete all data

// History
window.clearChatWindow();        // Clear display only
window.clearChatHistory();       // Delete from database
window.toggleMessageVisibility(); // Hide/show old messages

// Splash screen
window.toggleSplash(show);       // true = show, false = hide

// Teaching
window.quickCommit(trigger);     // Save last bot response as rule
window.openTeachMode(input);     // Open editor with trigger

// Debug
window.testStateLogic();         // Validate state filters
```

### Helper Functions (Internal)

```javascript
// Vector operations
await getVector(text);                    // Generate embedding
await getAverageVector(texts);            // Centroid of multiple
cosineSimilarity(vectorA, vectorB);       // Similarity score

// NLP
await processWithNLP(input);              // Intent + entities
// Returns: {intent: "greeting", entities: {user: "Alex"}, normalizedText: "..."}

// History
await getRelevantHistory(queryVector, limit);  // Semantic retrieval

// Logging
await logHeuristic(message, type);        // Add to debug panel

// Preload
await preloadVectors();                   // Background vector sync

// Core
await initNeuralCore();                   // Load ML model
await appendChat(role, text, ...);        // Add message bubble
await appendTeachPrompt(userInput);       // Show teach prompt

// Lifecycle
await loadHistory();                      // Restore chat from DB
await showWelcomeIfFirstTime();           // First-run message
await autoIngestBrain();                  // Load standalone brain
```

---

## Performance Tuning

### Vector Preloading

**Automatic:**
- Triggers when page loads
- Processes rules without vectors
- Shows "VECTORS SYNCING..." indicator
- Non-blocking (happens in background)

**Manual Control:**
```javascript
await preloadVectors();
```

**Monitoring:**
Watch header for "NEURAL SYNCED" chip.

### Worker Threading

**When to Enable:**
- 100+ rules with vectors
- Noticeable UI lag during typing
- Mobile devices

**How to Enable:**
1. Architect tab
2. Toggle "Vector Threading (Optimization)"

**Performance Impact:**
```
Without Worker:
100 rules × 50ms = 5000ms (blocks UI)

With Worker:
100 rules × 50ms = 5000ms (non-blocking)
UI remains responsive
```

### IndexedDB Optimization

**Limit History Size:**
```javascript
// Delete old messages
const cutoff = Date.now() - (30 * 24 * 60 * 60 * 1000); // 30 days
await db.history.where('timestamp').below(cutoff).delete();
```

**Export Without History:**
1. Uncheck "Include Temporal History"
2. Export brain
3. Much smaller file size

**Batch Operations:**
```javascript
// Bad - many transactions
for (const rule of rules) {
  await db.rules.add(rule);
}

// Good - single transaction
await db.rules.bulkAdd(rules);
```

### Dream Mode Optimization

**Reduce History Window:**
```javascript
// In dream() function
const recentHistory = await db.history.orderBy('id').reverse().limit(20).toArray();
// Instead of 50
```

**Lower Intensity:**
```javascript
dreamIntensity = 15;  // Instead of 30
```

### Browser Compatibility

**Storage Quotas:**
- Chrome: ~60% of available disk
- Firefox: ~50% of available disk
- Safari: 1GB max
- Mobile: Often lower

**Memory Management:**
```javascript
// Check storage usage
if (navigator.storage && navigator.storage.estimate) {
  const {usage, quota} = await navigator.storage.estimate();
  console.log(`Using ${usage} of ${quota} bytes`);
}
```

---

## Troubleshooting

### Loader Hangs

**Symptoms:**
- "AWAKENING THE BEAST..." never completes
- Page frozen

**Causes:**
1. Model download failed
2. Library loading error
3. Browser incompatibility

**Solutions:**
```javascript
// Check console (F12)
// Look for:
// - Network errors
// - Script errors
// - CORS warnings

// Try:
1. Clear browser cache
2. Use local server (python -m http.server)
3. Check internet connection
4. Try different browser
5. Disable browser extensions
```

### Rules Not Matching

**Symptoms:**
- Always enters Dream Mode
- Expected rule doesn't fire

**Diagnosis:**
1. Open Debug tab
2. Send test input
3. Check heuristic trace

**Common Issues:**

```javascript
// Issue: No vector generated
// Trace: "MOD_5 candidate via fuzzy: 0.45"
// Solution: Wait for "NEURAL SYNCED" indicator

// Issue: State filter blocking
// Trace: "MOD_5 path rejection: Positive,Friendly"
// Solution: Check current state, adjust requiredStates

// Issue: Threshold too high
// Trace: "MOD_5 Vector Match: 0.72" (but threshold is 0.75)
// Solution: Lower vector threshold in Debug tab

// Issue: Typo in trigger
// Trace: No mention of rule at all
// Solution: Check trigger spelling in Rule Library
```

### Scripts Not Executing

**Symptoms:**
- `<script>` tags visible in response
- No dynamic behavior

**Causes:**
1. Scripts disabled
2. Syntax error in script
3. Async timing issue

**Solutions:**
```javascript
// 1. Enable scripts
// Architect tab → "Executive Script Permission" → ON

// 2. Check console for errors
// F12 → Console tab

// 3. Add timing delay
<script>
(async () => {
  await new Promise(r => setTimeout(r, 100));  // Wait for DOM
  // Your code here
})();
</script>
```

### Storage Errors

**Symptoms:**
- "Storage operation failed"
- Rules disappear after reload
- Export fails

**Causes:**
1. Quota exceeded
2. Private browsing mode
3. Browser settings

**Solutions:**
```javascript
// Check quota
const {usage, quota} = await navigator.storage.estimate();
if (usage / quota > 0.9) {
  console.warn('Storage almost full!');
}

// Clear old data
await db.history.where('timestamp')
  .below(Date.now() - 7 * 24 * 60 * 60 * 1000)
  .delete();

// Check browser mode
if (!window.indexedDB) {
  alert('IndexedDB not available (private browsing?)');
}
```

### Vector Generation Slow

**Symptoms:**
- "SYNCING: 50/100" stays for minutes
- Page unresponsive

**Causes:**
1. Weak CPU
2. Too many rules
3. Worker not enabled

**Solutions:**
```javascript
// Enable worker threading
// Architect tab → "Vector Threading" → ON

// Reduce rule count
// Delete unused rules in Rule Library

// Use simpler triggers
// Instead of: ["very long trigger with many words"]
// Use: ["short trigger"]
```

### Mobile Issues

**iOS Keyboard Push:**
```css
/* Already fixed in v1.0.3 */
@supports (-webkit-touch-callout: none) {
  body { height: -webkit-fill-available; }
}
```

**Input Zoom:**
```css
/* Already fixed - font-size: 16px prevents zoom */
#user-input { font-size: 16px !important; }
```

**Storage Cleared:**
- iOS Safari clears IndexedDB under memory pressure
- Solution: Export brain regularly
- Standalone mode recommended (brain embedded)

### Import Fails

**Symptoms:**
- "Import failed: Unexpected token"
- Page doesn't reload

**Causes:**
1. Invalid JSON
2. Missing quotes
3. Trailing commas

**Solutions:**
```javascript
// Validate JSON first
try {
  JSON.parse(yourBrainJSON);
} catch (e) {
  console.error('Invalid JSON:', e.message);
}

// Common fixes:
// - Remove trailing commas: {...},] → {...}]
// - Escape quotes: "text": "say "hi"" → "text": "say \"hi\""
// - Check brackets: Ensure all {[ are closed }]
```

### Debug Panel Empty

**Symptoms:**
- No trace logs appear
- Panel shows "System idle..."

**Solutions:**
```javascript
// Send a test message
// Logs should appear immediately

// If still empty:
1. Refresh page
2. Check console for errors
3. Verify Debug tab is active
```

---

## FAQ

**Q: Does this work offline?**  
A: Yes, after the first model download (~30-90 MB), everything is cached locally.

**Q: Where is data stored?**  
A: IndexedDB in your browser. Data never leaves your device.

**Q: Can I use this commercially?**  
A: Yes, MIT license allows commercial use.

**Q: What's the model size?**  
A: ~30-90 MB (all-MiniLM-L6-v2 ONNX format), downloads once, caches forever.

**Q: What about privacy?**  
A: No telemetry, no analytics, no external calls (except model download on first run).

**Q: Can I reset everything?**  
A: Yes, Architect tab → "PURGE ALL DATA" → Confirm → Reloads fresh.

**Q: Does it remember conversations?**  
A: Yes, stored in IndexedDB. Clear with "CLEAR HISTORY" button.

**Q: What browsers are supported?**  
A: Opera, Chrome 90+, Firefox 88+, Safari 14+, Edge 90+.

---

## Appendix

### File Formats

**Brain JSON:**
```json
{
  "_meta": {...},
  "rules": [...],
  "config": [...],
  "variables": [...],
  "intents": [...],
  "history": [...]
}
```

**Rule JSON:**
```json
{
  "triggers": ["string"],
  "responses": [{"weight": number, "text": "string"}],
  "stateWeights": {"StateName": number},
  "requiredStates": ["StateName"],
  "chainPrompt": "string",
  "vector": [number...]  // Auto-generated
}
```

### Database Schema

```javascript
db.version(5).stores({
  rules: '++id, *triggers, *requiredStates, chainPrompt',
  history: '++id, role, timestamp, ruleId, vector',
  config: 'key',
  variables: 'key',
  intents: '++id, label, pattern'
});
```

### Vector Format

```javascript
// 384-dimensional unit vector
[
  0.0234, -0.1234, 0.5678, ...  // 384 numbers
]

// Properties:
// - Normalized (magnitude = 1)
// - Float32 precision
// - Range: typically -1 to 1
```

### State JSON Format

```json
{
  "key": "allStatuses",
  "value": [
    {"name": "Positive", "parent": null},
    {"name": "Playful", "parent": "Positive"},
    {"name": "Negative", "parent": null}
  ]
}
```

### Intent JSON Format

```json
{
  "id": 1,
  "label": "greeting",
  "pattern": "#Greeting"
}
```

## A Note for Offline Purists

If you're the kind of person who wants everything running 100% locally from the very first open (no remote fetches at all), NeoTherion already makes it as easy as possible without forcing the complexity on everyone else.

Quick smell:
- The `/libs/` folder already contains Dexie, compromise, and marked — the app prefers them automatically.
- The `/models/` folder contains the Xenova/all-MiniLM-L6-v2 files (config, tokenizer, quantized ONNX, etc.).
- Transformers.js itself is loaded remotely by default (because local use requires a web server anyway due to browser fetch rules on file://).

For the full air-gapped experience:
1. Copy the `/libs/` and `/models/` folders as-is (they're already there).
2. Download `transformers.min.js` from https://cdn.jsdelivr.net/npm/@xenova/transformers@2.14.0/dist/transformers.min.js and place it in `/libs/`.
3. Run the folder through a local web server (python -m http.server, npx serve, etc.) — this is required for the browser to fetch local model files.
4. (Optional) Add `env.localModelPath = './models/Xenova/all-MiniLM-L6-v2/';` near the top of the script if you want to force the local path.

Most users will never need this — just open the file and enjoy the beast.

---
## Credits

Coded on:
- DW Pad6S Pro (MT6755 Octa-Core ARM64, Mali-T860 GPU, Android 8.1 Oreo / API 27).

software:
- [Markor Text editor - Notes & ToDo](https://github.com/gsantner/markor)
- [Code Editor - Compiler & IDE](https://play.google.com/store/apps/details?id=com.rhmsoft.code)

Built with:
- [Dexie.js](https://dexie.org/) - IndexedDB wrapper
- [Compromise](https://github.com/spencermountain/compromise) - NLP library
- [Transformers.js](https://huggingface.co/docs/transformers.js) - ML inference
- [all-MiniLM-L6-v2](https://huggingface.co/sentence-transformers/all-MiniLM-L6-v2) - Sentence embeddings

## License

Modify, extend, share freely — attribution appreciated but not required

>  *“Do what thou wilt shall be the whole of the Law.”*

![Version](https://img.shields.io/badge/version-1.0.0-red)
![License](https://img.shields.io/badge/license-MIT-blue)
![Browser](https://img.shields.io/badge/browser-offline--ready-green) 