# NeoTherion - Documentation
---
### Offline-first AI engine for building adaptive chatbots, assistants, text games, automation tools and knowledge systems—with semantic understanding, persistent memory, and zero server dependencies.
---

## Quick Start

### 30-Second Setup

1. **Open** the HTML file in any modern browser
2. **Wait** for the neural core to initialize (~30MB model download on first load)
3. **Type** "hello" to test the default greeting
4. **Start building** your intelligence in the System Architect Center.

### 5-Minute First Bot

**Goal**: Create a simple name-capture conversation flow.

1. Click **⚙ System Architect Center**
2. Navigate to **Core Training** tab
3. Paste this into **Mass Data ingestion**:

```json
{
  "rules": [
    {
      "triggers": ["hello", "hi"],
      "responses": [{"weight": 1, "text": "Hi! What's your name?"}],
      "stateWeights": {"Positive": 1},
      "requiredStates": []
    },
    {
      "triggers": ["[user]"],
      "responses": [{"weight": 1, "text": "Nice to meet you, (User)!"}],
      "stateWeights": {"Neutral": 1},
      "requiredStates": ["Positive"]
    }
  ]
}
```

4. Click **PROCESS STREAM**
5. **Test**: Type "hello" → "Alex" → See the bot respond with your capitalized name

---

## System Overview

### What Is NeoTherion?

NeoTherion is a **zero-dependency, offline-first AI engine** that runs entirely in your browser. It combines:

- 🎯 **Pattern Matching** - Regex-based triggers with variable capture
- 🧠 **Semantic Understanding** - 384-dimensional vector embeddings
- 💾 **Persistent Memory** - IndexedDB for conversation history and context
- 🎭 **State Machines** - Hierarchical behavior control
- 🔄 **Dynamic Variables** - Real-time data injection with smart casing
- 📚 **RAG Dreaming** - Markov chains seeded by semantic similarity

### Key Specifications

| Feature | Specification |
|---------|---------------|
| **Model** | all-MiniLM-L6-v2 (Sentence Transformers) |
| **Vector Dimensions** | 384 |
| **Rendering** | **Markdown (Marked.js)** |
| **Logic** | **Recursive Prompt Chaining** |
| **Performance** | **Multithreaded Vector Processing** |
| **Database** | IndexedDB (Dexie.js v5) |
| **Offline Capability** | 100% after initial load |


### Architecture Diagram

```
[ USER INPUT ] --> "What's the weather like?"
      |
      v
[ NLP PREPROCESSING ]
  • Entity extraction
  • Text normalization
  • Intent detection
      |
      v
[ VECTOR EMBEDDING ]
  • Convert to 384-dim semantic representation
      |
      v
[ MATCHING ENGINE (Waterfall) ]
  1. REGEX  (1.00) -> Exact patterns + captures
  2. VECTOR (0.75) -> Semantic similarity
  3. FUZZY  (0.65) -> Levenshtein/Typo tolerance
      |
      v
< MATCH FOUND? >-----------+
      | (Yes)              | (No)
      v                    v
[ RESPONSE GEN ]     [ DREAM MODE ]
      |              • RAG / Markov
      +----------+---------+
                 |
                 v
      [ VARIABLE INJECTION ]
      (user), (status), (time)
                 |
                 v
      [ STATE TRANSITION ]
      Update hierarchy chain
                 |
                 v
      [ DISPLAY & STORE ]
      • Show chat & Save vector
```

---

## Core Concepts

### 1. Rules - The Foundation
Rules define how the engine responds. Each rule is stored in the following JSON format:

```json
{
  "rules": [
    {
      "triggers": ["hello"],
      "responses": [
        { "weight": 1, "text": "### Hi!\nWelcome back, **(User)**." }
      ],
      "stateWeights": { "Positive": 1 },
      "requiredStates": [],
      "chainPrompt": "check_status"
    }
  ]
}
```

*   **Responses (Markdown)**: All text is rendered via Markdown. You can use headers (`###`), bold (`**`), and tables to create structured UIs.
*   **chainPrompt**: If defined, the bot will automatically send this text back to itself 1.5 seconds after responding, allowing for automated multi-step sequences.

### 2. Variables & Smart Injection
NeoTherion uses a "Smart Case" injection system.

| Variable | Output |
|----------|--------|
| `(user)` | Raw captured value ("alex") |
| `(User)` | **Title Case** ("Alex") |
| `(status)` | The active State Name ("Neutral") |

---

**Rule Matching Priority**:
1. **Regex Match** (Score: 1.0) - Exact pattern with variable capture
2. **Vector Match** (Score: 0.0-1.0) - Semantic similarity via embeddings
3. **Fuzzy Match** (Score: 0.0-1.0) - Levenshtein distance for typos

### 2. Triggers - Input Patterns

#### Basic Trigger
```
hello
```
Matches: "hello", "Hello", "HELLO"

#### Variable Capture
```
my name is [user]
```
Matches: "my name is Alex" → Stores `user = "Alex"`

#### Relational Trigger
```
+yes
```
**Must** follow another message. Gets **+0.15 priority boost** if history exists.

**Example Flow**:
```
Bot: "Do you like pizza?"
User: "yes"  ← Relational trigger fires with 1.15 confidence
```

#### Multiple Triggers Per Rule
```javascript
triggers: [
  "what's your name",
  "who are you",
  "tell me about yourself"
]
```
All three patterns point to the same response pool.

### 3. Variables - Dynamic Data

#### Template Variables (Auto-Injected)

| Variable | Output | Example |
|----------|--------|---------|
| `(user)` | Captured username | "alex" |
| `(User)` | **Title-cased** username | "Alex" |
| `(status)` | Current state | "Happy" |
| `(time)` | Current time HH:MM | "14:30" |
| `(day)` | Current weekday | "Monday" |

#### Fallback Syntax
```
Hello, (user|stranger)!
```
If `user` doesn't exist, displays "Hello, stranger!"

#### Casing Rules (IMPORTANT)

```javascript
(user)  → "alex"     // Lowercase variable = original value
(User)  → "Alex"     // Uppercase first letter = Title Case
(USER)  → "alex"     // All caps = original value (no special behavior)
```

**Best Practice**: Use `(User)` for names in natural sentences:
```
"Welcome back, (User)!"  → "Welcome back, Alex!"
```

### 4. States - Contextual Behavior

States control **when rules activate** and **how the bot behaves**.

#### State Types

**Emotional States**:
```
Positive → Happy, Excited, Content
Negative → Sad, Frustrated, Angry
Neutral → Calm, Thoughtful
```

**Categorical States**:
```
Tech → Programming, Hardware, AI
Entertainment → Movies, Music, Games
```

**Game States**:
```
Location → Forest, Town, Dungeon
Combat → Fighting, Defending
```

**Persona States**:
```
Assistant → Professional, Helper
Friend → Casual, Supportive
```

#### Hierarchy Mechanics

```
Parent State
  ↳ Child State 1
  ↳ Child State 2
```

**Inheritance Rule**: If current state is "Happy" (child of "Positive"), rules requiring "Positive" OR "Happy" will activate.

**Example**:
```
Current State: Happy

Rule A: requiredStates: ["Happy"]     → ✓ ACTIVATES
Rule B: requiredStates: ["Positive"]  → ✓ ACTIVATES (parent)
Rule C: requiredStates: ["Negative"]  → ✗ FILTERED OUT
Rule D: requiredStates: []            → ✓ ACTIVATES (always active)
```

#### Special State: `[Maintain State]`

Prevents state transitions. Use when response shouldn't change mood/context.

```javascript
stateWeights: {"[Maintain State]": 1}
```

### 5. Matching Engine - The Three-Stage Waterfall

#### Stage 1: Regex Matching (Threshold: 1.0)

**How It Works**:
- Converts triggers to regex patterns
- Captures variables with `[varname]`
- Exact match only

**Relational Boost**:
```javascript
if (trigger.startsWith('+') && hasHistory) {
  confidence += 0.15;  // Now scores 1.15
}
```

**When It Fires**: User types exact trigger text or close variant.

#### Stage 2: Vector Matching (Threshold: 0.75)

**How It Works**:
- Compares input embedding to rule's **centroid vector**
- Centroid = average of all trigger embeddings
- Cosine similarity score (0.0-1.0)

**Example**:
```
Rule triggers: ["what is reality", "nature of existence", "philosophical truth"]
User input: "tell me about the nature of life"
Vector Score: 0.82 → MATCH (above 0.75 threshold)
```

**When It Fires**: User expresses same **idea** with different words.

#### Stage 3: Fuzzy Matching (Threshold: 0.65)

**How It Works**:
- Calculates Levenshtein distance
- Normalized by max length
- Preprocessed with NLP (contractions expanded)

**Example**:
```
Trigger: "hello there"
User: "helo ther"  → Distance: 2, Score: 0.82 → MATCH
```

**When It Fires**: Typos, misspellings, mobile keyboard errors.

### 6. Dream Mode - Fallback Intelligence

When no rule matches (all scores below thresholds), the engine "dreams":

#### RAG Retrieval (Semantic Memory)
1. Find 5 most similar past conversations via vector search
2. Use as context for generation

#### Markov Chain Generation
1. Build trigram model from history
2. Seed with user input
3. Generate probabilistic text

#### Natural Termination
- **30% exit check** at each punctuation mark
- Prevents run-on sentences
- Respects `dreamIntensity` setting (10-80 tokens)

**Example Output**:
```
Input: "Tell me something interesting"
Dream: "Something interesting to explore the nature of consciousness in 
       the digital age..."
```

---

## User Interface Guide

### Main Chat Interface

#### Message Bubbles
- **User** (Blue, Right-aligned) - Your messages
- **Bot** (Dark, Left-aligned) - System responses
- **Typing Indicator** (Animated dots) - Bot is processing

#### Metadata Display
- **State Tag** (Top-left of bot bubble) - Active state during response
- **Confidence Badge** (Top-right) - Match method + score
  - `REGEX 100%` - Exact pattern match
  - `VECTOR 82%` - Semantic understanding
  - `FUZZY 68%` - Typo correction
  - `DREAM 0%` - Fallback generation

#### Controls
- **👁 TOGGLE HISTORY** - Hide all but last message (performance mode)
- **Input Field** - Auto-expands, Shift+Enter for newline
- **➤ Send Button** - Submit message (or press Enter)

### System Control Center (Lab)

#### Tab 1: Core Training

**Section 1: Response Intelligence**
- Add multiple response variants
- Adjust weights (higher = more likely)

**Neural Response Logic**
- A live preview area showing how your Markdown and variables will look when rendered in the chat.

**Section 2: Target States**
- Select next state(s) after response
- Weighted transitions (random selection)
- Special option: `[Maintain State]`

**Section 3: Context Triggers**
- One trigger per line
- Use `[varname]` for captures
- Prefix `+` for relational triggers

**Prompt Chaining**
- Enter a hidden command here to trigger a recursive logic loop
- (e.g., a "reboot" command that chains into a "diagnostic" command).

**Section 4: Activation Filter**
- Restrict rule to specific states
- Empty = always active
- Hierarchy-aware (parent/child logic)

**Mass Data ingestion**
- Paste JSON, conversation logs, or backups
- Auto-detects format
- Bulk import rules

#### Tab 2: Rule Library

- View all trained rules
- **✎ Edit** - Load rule into training tab
- **× Delete** - Remove rule (with confirmation)
- Shows: triggers, responses, states, filters

#### Tab 3: Architect

**Script Execution** ⚠️
- **Default: OFF** for security
- Enable to run JavaScript in responses
- See **Advanced Features: Response Scripting** section

**Vector Threading**
- A performance toggle. When enabled, similarity math is offloaded to a background thread (Web Worker).
- Highly recommended for libraries with >100 rules.*

**Neural Intent Mapper**
- Map patterns to intent labels (e.g., `"hello|hi"` → `"greet"`)
- Used by NLP preprocessing
- Customizable pattern matching
- Saves on blur to prevent database spam

**Auto-Teach Mode**
- Shows "TEACH" prompt for unknown inputs
- Quick commit via "ENTER" button
- Converts dreams into permanent rules

**Show State Transitions**
- Display "Neutral → Happy" in chat
- Visual feedback for state changes

**Neural Calibration**
- Adjust dream intensity (10-80 tokens)
- Higher = longer fallback responses

**Variable Memory Matrix**
- Row-based editor with live updates
- Individual delete buttons per variable
- Saves on blur (no keystroke spam)
- Key-value pairs clearly separated

**State Hierarchist**
- Create parent/child states
- Delete states (checks for rule dependencies)
- Reorganize state tree

**Memory Control**
- **Export**: Copy JSON backup to clipboard
- **Import**: Restore from JSON
- **Purge**: Delete all data (⚠️ irreversible)

#### Tab 4: Debug

**Current State Analysis**
- Active state name
- Full hierarchy chain
- Available rule count

**Last Match Analysis**
- Real-time match scoring
- Filter decisions
- Confidence calculations

**Confidence Thresholds**
- **Regex Priority** (0.95-1.0) - Exact match strictness
- **Vector Threshold** (0.5-0.95) - Semantic similarity cutoff
- **Fuzzy Threshold** (0.4-0.85) - Typo tolerance level

---

## Building Intelligence

### Best Practices

#### Trigger Design

**✅ DO**:
```javascript
// Clear, specific patterns
triggers: ["what's the weather", "weather forecast", "how's the weather"]

// Variable capture
triggers: ["my name is [user]", "call me [user]", "i'm [user]"]

// Relational context
triggers: ["+yes", "+that sounds good", "+i agree"]
```

**❌ DON'T**:
```javascript
// Too vague (will match everything)
triggers: ["what", "tell me", "how"]

// Overlapping exact matches (use variants in responses instead)
triggers: ["hello", "hello there", "hello friend"]

// Missing relational prefix
triggers: ["yes"]  // Should be "+yes" for follow-ups
```

#### Response Crafting

**✅ DO**:
```javascript
responses: [
  {weight: 10, text: "Hello, (User)! How can I help?"},
  {weight: 5, text: "Hi there! What brings you here today?"},
  {weight: 1, text: "Greetings."}
]
```
- Multiple variants for natural conversation
- Higher weights for preferred responses
- Use variables for personalization

**❌ DON'T**:
```javascript
responses: [
  {weight: 1, text: "ok"},
  {weight: 1, text: "sure"},
  {weight: 1, text: "alright"}
]
```
- Too short/generic
- No personality
- No context utilization

#### State Architecture

**✅ DO**:
```
Mood (parent)
  ↳ Happy
  ↳ Sad
  ↳ Neutral

Topic (parent)
  ↳ Work
  ↳ Personal
  ↳ Entertainment
```
- Logical groupings
- Clear hierarchy
- Distinct purposes

**❌ DON'T**:
```
State1
State2
HappyWorkMode
SadPersonalMode
```
- Meaningless names
- No hierarchy
- Combining multiple contexts

### Training Workflows

#### Method 1: Manual Rule Building (Precision)

**Best For**: Custom personalities, specific behaviors, game logic

1. Open **Core Training** tab
2. Define 3-5 response variants
3. Add all relevant triggers
4. Set state transitions
5. Configure state filters
6. Test in chat
7. Refine based on results

**Time Investment**: 5-10 minutes per rule

#### Method 2: Quick Commit (Speed)

**Best For**: Rapid prototyping, learning from conversations

1. Enable **Auto-Teach Mode**
2. Chat naturally with the bot
3. When it dreams, click **ENTER** button
4. Rule auto-generated from last exchange

**Time Investment**: Instant

#### Method 3: LLM-Generated Data (Scale)

**Best For**: Knowledge bases, persona profiles, complete systems

1. Provide LLM with system documentation
2. Request JSON data module
3. Paste into **Mass Data ingestion**
4. Test and refine

**Time Investment**: Minutes for hundreds of rules

---

## Advanced Features: Response Scripting

NeoTherion allows for high-privilege JavaScript execution directly within bot responses. This enables the engine to modify the UI, interact with external APIs, or update its own database in real-time.

### ⚠️ Security Protocol
By default, **Script Execution is DISABLED**. Before using scripting rules, you must:
1. Open the **Architect** tab.
2. Toggle **Executive Script Permission** to **ON**.
3. Acknowledged the red security warning.

### The Technical Mechanism
Standard browsers block scripts added via `innerHTML`. NeoTherion bypasses this by parsing responses, creating new Script DOM elements, and re-injecting them into the chat bubble. 

**Script Requirements:**
*   Always wrap logic in an **Async IIFE**: `(async () => { ... })();`
*   Use spaces inside parentheses—e.g., `function( )`—to prevent conflicts with the Variable Injection engine.
*   Target the last bubble using: `const msg = document.querySelector('.bubble.bot:last-child');`

### Scripting Best Practices

**✅ DO:**
*   Use scripting to change CSS variables (Themes).
*   Use `fetch()` to pull live data from open APIs (Weather, Stocks).
*   Use `await` for database operations via `Dexie`.

**❌ DON'T:**
*   Use `document.write()`.
*   Include scripts in rules meant for untrusted third-party imports.
*   Perform heavy calculations inside the UI thread (use Vector Threading instead).


#### How It Works

**The Technical Achievement**: Browsers block scripts injected via `innerHTML` for security. NeoTherion bypasses this by:

1. Parsing response for `<script>` tags
2. Creating new script elements
3. Copying attributes and content
4. Re-appending to DOM with execution permissions

```javascript
// Engine code (simplified)
const executeScripts = (element) => {
  element.querySelectorAll("script").forEach(oldScript => {
    const newScript = document.createElement("script");
    newScript.innerHTML = oldScript.innerHTML;
    oldScript.parentNode.replaceChild(newScript, oldScript);
  });
};
```

#### Script Template

```javascript
{
  "triggers": ["command"],
  "responses": [{
    "weight": 1,
    "text": "Response text here <script>(async () => { /* your code */ })();</script>"
  }],
  "stateWeights": {"[Maintain State]": 1},
  "requiredStates": []
}
```

**Key Points**:
- Wrap in async IIFE: `(async () => { ... })()`
- Scripts execute **after** text renders
- Use spaces in parentheses: `function( )` not `function()` to avoid variable injection conflicts
- Access to all global objects

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
## Console API

NeoTherion exposes its core engine to the global scope for debugging and advanced scripting:

```javascript
// Check current state
await NeoTherion.getStatus()

// Manually trigger state transition
await NeoTherion.setStatus("Combat")

// Test dream generation
await NeoTherion.dream("prompt text", vectorOptional)

// Inject variables into text
await NeoTherion.inject("Hello (User)!")

// Get state hierarchy chain
await NeoTherion.getStateHierarchy("Happy")

---

## Data Import/Export

### Import Formats

The **System Architect Center** accepts three formats:

#### 1. Full JSON Backup (Recommended)

Complete system state including rules, config, variables, intents, and optionally history.

```json
{
  "rules": [...],
  "config": [...],
  "variables": [...],
  "intents": [...],
  "history": [],
  "version": "NEO-THERION",
  "date": "2026-01-08T12:00:00Z"
}
```

**Best For**: Backups, sharing complete systems, version control

#### 2. Conversation Logs (Auto-Parse)

Simple text format for rapid rule generation.

```
User: what's your name?
Assistant: I'm NeoTherion, an AI assistant.
User: what can you do?
Assistant: I can have conversations, answer questions, and learn.
```

**Best For**: Converting existing chat logs, rapid prototyping

#### 3. Single Rule (Quick Add)

Must be wrapped in `rules` array.

```json
{
  "rules": [
    {
      "triggers": ["goodbye", "bye"],
      "responses": [{"weight": 1, "text": "Goodbye!"}],
      "stateWeights": {"Neutral": 1},
      "requiredStates": []
    }
  ]
}
```

**Best For**: Adding single rules, testing

### Export Options

**Location**: Architect Tab → Memory Control → "COPY NEURAL BACKUP"

**Toggle "Include Chat History"** to control file size. Exported JSON includes version tag for compatibility tracking.

---

## Meshtastic LoRa AI Integration

**Off-Grid Intelligence**: Since the entire engine runs locally, it can act as an intelligent node on a mesh network without internet.

**Response Scripting Bridge**: Execute JavaScript to:
- Pull incoming packets from Meshtastic device
- Process via rule-based/semantic matching
- Push response packets back to LoRa frequency

**Deployment**: Optimized for low-spec mesh node hardware (~2GB RAM).
---

## Performance & Optimization
Because NeoTherion runs entirely in the browser, efficiency is key:

1.  **Vector Threading**: If you notice the "typing dots" freezing while the bot thinks, enable Vector Threading in the Architect tab.
2.  **History Visibility**: Use the **👁 HISTORY** button in the header to hide older messages. This reduces DOM pressure during long sessions.
3.  **Threshold Tuning**: If the bot is matching rules incorrectly, go to the **Debug Tab** and increase the **Vector Semantic Threshold** (e.g., to 0.85) to make it more strict.

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

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT) 
