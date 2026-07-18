# Smart Study Generator Agent

An interactive, AI-powered study companion built as a single self-contained HTML page, backed by **IBM watsonx Orchestrate**.  
The agent generates personalised study plans, quizzes, flashcards, concept explanations, and note summaries — all through a live chat interface.

---

## Table of Contents

- [Overview](#overview)
- [Features](#features)
- [File Structure](#file-structure)
- [Agent Configuration](#agent-configuration)
- [How It Works](#how-it-works)
- [Running the Page](#running-the-page)
- [Page Sections](#page-sections)
- [Interactive Components](#interactive-components)
- [Customisation](#customisation)
- [Troubleshooting](#troubleshooting)
- [Tech Stack](#tech-stack)

---

## Overview

`smart-study-generator-agent.html` is a **fully static single-file HTML application** — no build step, no dependencies to install, no server required.  
Open it in any modern browser and the page immediately connects to IBM watsonx Orchestrate to serve the live chat agent.

A **demo chat UI** (animated bubbles, quick-reply chips, input bar) is displayed while the Watson widget loads. Once the widget is ready, the demo chat is automatically replaced by the live agent interface.

---

## Features

| Capability | Description |
|---|---|
| **Study Plan Generator** | Personalised roadmaps broken down by day, week, or session |
| **Quiz Me** | Adaptive quizzes — multiple-choice, true/false, short-answer |
| **Note Summariser** | Paste any text/chapter and receive a concise key-concept summary |
| **Flashcard Creator** | Auto-generate spaced-repetition flashcard decks from any topic |
| **Concept Explainer** | Layered explanations from beginner-friendly to expert-level depth |
| **Progress Tracker** | Session logging and learning momentum visualisation |

---

## File Structure

```
smart-study-generator-agent.html   ← entire app (HTML + CSS + JS, self-contained)
smart-study-generator-agent.README.md  ← this file
```

No external assets, frameworks, or build tools are required.  
All CSS, JavaScript, and inline SVG are embedded within the single HTML file.

---

## Agent Configuration

The Watson Orchestrate widget is initialised via the `window.wxOConfiguration` object in the `<script>` block near the bottom of the HTML file:

```js
window.wxOConfiguration = {
  orchestrationID: "5d395288d63946548797e15c437652af_52fb804a-73ba-406d-97b7-446dde73385d",
  hostURL:         "https://au-syd.watson-orchestrate.cloud.ibm.com",
  rootElementID:   "root",
  deploymentPlatform: "ibmcloud",
  crn: "crn:v1:bluemix:public:watsonx-orchestrate:au-syd:a/5d395288d63946548797e15c437652af:52fb804a-73ba-406d-97b7-446dde73385d::",
  chatOptions: {
    agentId: "baf6f8b6-b941-4ef5-9b6c-91eeeece0a20"
  }
};
```

| Property | Value |
|---|---|
| `orchestrationID` | `5d395288d63946548797e15c437652af_52fb804a-73ba-406d-97b7-446dde73385d` |
| `hostURL` | `https://au-syd.watson-orchestrate.cloud.ibm.com` |
| `rootElementID` | `root` |
| `deploymentPlatform` | `ibmcloud` |
| `agentId` | `baf6f8b6-b941-4ef5-9b6c-91eeeece0a20` |
| `region` | `au-syd` (Sydney) |

The loader script is fetched at runtime from:
```
https://au-syd.watson-orchestrate.cloud.ibm.com/wxochat/wxoLoader.js?embed=true
```

> **Important:** The `#root` element must be **empty** when `wxoLoader.init()` is called. Any pre-existing child content inside `#root` will prevent the widget from mounting correctly.

---

## How It Works

```
Browser loads HTML
       │
       ▼
Demo chat UI is displayed (animated placeholder)
       │
       ▼
setTimeout(0) fires → <script> injected into <head>
       │
       ▼
wxoLoader.js fetched from au-syd.watson-orchestrate.cloud.ibm.com
       │
       ▼
script 'load' event fires:
  1. #root  → display: block   (reveal the widget container)
  2. #demo-chat → display: none  (hide the placeholder)
  3. wxoLoader.init()           (mount the Watson widget into #root)
       │
       ▼
Watson Orchestrate agent renders inside #root
User can now chat with the Smart Study Generator
```

---

## Running the Page

### Option 1 — Open directly in a browser

```bash
# macOS / Linux
open smart-study-generator-agent.html

# Windows
start smart-study-generator-agent.html
```

### Option 2 — Serve locally (recommended to avoid CORS restrictions)

Using Python:
```bash
python -m http.server 8080
# then open http://localhost:8080/smart-study-generator-agent.html
```

Using Node.js (`npx serve`):
```bash
npx serve .
# then open the URL printed in the terminal
```

> **Network access required.** The Watson Orchestrate widget is loaded from IBM Cloud at runtime. An active internet connection is necessary for the agent to function.

---

## Page Sections

| Section ID | Content |
|---|---|
| `#hero` | Animated headline, typewriter effect, AI brain orb graphic |
| `#stats` | Animated counters — study plans, topics, satisfaction rate |
| `#features` | Six feature cards with glassmorphism and tilt effects |
| `#chat-section` | **Live Watson Orchestrate chat widget** (primary interaction) |
| `#how-it-works` | 4-step process cards with connector line |
| `#study-plan` | Tabbed sample study plans — ML, World History, Cell Biology, Calculus |
| `#flashcards` | Interactive 3D flip flashcard demo with self-assessment scoring |
| `#subjects` | 8 subject coverage cards with animated progress bars |
| `#testimonials` | 6 student testimonial cards |
| `#achievements` | 6 gamification badge cards |
| `#learning-tips` | 5 evidence-based study tips + 7 brain science fun facts |
| `#highlight` | CTA strip — "Start studying smarter" |
| `footer` | Navigation links, copyright, Made with IBM Bob credit |

---

## Interactive Components

### Chat Widget (`#chat-section`)
- **Demo chat** shows while the Watson loader is fetching — includes animated message bubbles and quick-reply chips.
- **Quick-reply chips** (pre-loaded prompts): ML Study Plan · Physics Quiz · History Summary · Python Cards · Explain CRISPR.
- **Live agent** replaces the demo automatically once `wxoLoader.js` fires its `load` event.

### Flashcard Flipper (`#flashcards`)
- Click the card to flip front→back (3D CSS transform, `rotateY(180deg)`).
- Navigate with ← → buttons.
- Rate confidence after each flip: **Easy / Medium / Hard**.
- Progress dots and a fill bar track deck completion.

### Study Plan Tabs (`#study-plan`)
- Four topic tabs: Machine Learning · World History · Cell Biology · Calculus.
- Each tab reveals a 6-day plan with tasks and time estimates.
- Active tab is highlighted with the gradient theme.

### Feature Cards (`#features`)
- Glassmorphism cards with CSS `backdrop-filter: blur`.
- Mouse-over triggers a 3D tilt effect via JS `mousemove` listener.

### Animated Stats (`#stats`)
- Numbers count up from 0 to their target value when scrolled into view, using `IntersectionObserver`.

### Fade-in Animations
- All major elements carry the `.fade-in` class.
- `IntersectionObserver` adds `.visible` when they enter the viewport, triggering `opacity` + `translateY` transitions.

---

## Customisation

### Change the agent
Update `agentId` in `window.wxOConfiguration`:
```js
chatOptions: {
  agentId: "YOUR-NEW-AGENT-ID"
}
```

### Change the region / host
Update `hostURL` and `crn` to point to your IBM Cloud region:
```js
hostURL: "https://us-south.watson-orchestrate.cloud.ibm.com",
crn:     "crn:v1:bluemix:public:watsonx-orchestrate:us-south:a/..."
```

### Update the colour theme
All colours are defined as CSS custom properties at the top of the `<style>` block:
```css
:root {
  --accent-blue:   #3b82f6;
  --accent-violet: #8b5cf6;
  --accent-cyan:   #06b6d4;
  --accent-pink:   #ec4899;
  --bg-primary:    #050a18;
  /* ... */
}
```

### Add or remove flashcards
Locate the `FLASHCARD_DATA` array in the JavaScript section and add/remove objects:
```js
{ q: "Your question here", a: "Your answer here" }
```

### Edit quick-reply chips
Find `#demo-chips` in the HTML and update the `data-msg` attributes and button text:
```html
<button class="demo-chip" data-msg="Your prompt here">Label</button>
```

---

## Troubleshooting

| Symptom | Likely Cause | Fix |
|---|---|---|
| Chat widget does not appear / blank box shows | `wxoLoader.js` failed to load | Check network connectivity; open DevTools → Network tab and confirm the loader script returns 200 |
| Demo chat never disappears | Watson loader script error | Check browser console for JS errors; ensure `agentId` and `orchestrationID` are correct |
| Widget mounts but agent does not respond | Invalid or expired `agentId` | Verify the agent is deployed and active in IBM watsonx Orchestrate |
| Page looks unstyled | `<link>` to Google Fonts blocked | The page uses system fonts as a fallback — no functional impact |
| Flashcard flip not working | JavaScript error on page load | Open DevTools → Console; look for syntax errors in the inline script block |
| Stats counters stuck at 0 | `IntersectionObserver` not supported | Upgrade to a modern browser (Chrome 58+, Firefox 55+, Edge 16+) |

---

## Tech Stack

| Layer | Technology |
|---|---|
| Markup | HTML5 (semantic elements, ARIA attributes) |
| Styling | Vanilla CSS3 — custom properties, `backdrop-filter`, CSS Grid, Flexbox, `@keyframes` |
| Scripting | Vanilla JavaScript (ES5-compatible) — no frameworks or libraries |
| AI Agent | IBM watsonx Orchestrate (`wxoLoader.js`, embedded chat widget) |
| Background FX | HTML5 `<canvas>` particle system (plain JS) |
| Fonts | Inter + Space Grotesk via Google Fonts (system-font fallback) |
| Animations | CSS `@keyframes` + JS `IntersectionObserver` |

---

## Requirements

- **Modern browser** — Chrome 80+, Firefox 75+, Edge 80+, Safari 14+
- **Internet connection** — required for IBM Watson Orchestrate widget and Google Fonts
- **IBM watsonx Orchestrate access** — the `agentId` must be deployed and active on the configured IBM Cloud account

---

*Made with IBM Bob*
