# Northstar Labs — Candidate Profile Assessment

A single-page web prototype built as a **Module 3 provocation exercise**. It simulates an AI-powered job-application personality test to critique how such systems can appear authoritative while making decisions that are arbitrary, ambiguous, and unjustifiable.

**This is not a real employment assessment.** No answers are stored, transmitted, or used for any purpose.

---

## What it does

1. **Landing screen** — presents a credible-looking assessment for a fictional role ("Operations Coordinator" at fictional company "Northstar Labs")
2. **10-question form** — one question at a time, with a progress bar. Questions are deliberately ambiguous. Questions 1 and 3 use multiple choice; the remaining 7 use a five-point Likert scale
3. **AI review loading screen** — fake processing steps to simulate an AI system at work
4. **Result screen** — a confident verdict ("Suitable" or "Not suitable to apply") backed by an opaque explanation and a made-up confidence percentage. The scoring is **intentionally inverted**: higher raw scores produce a "Not suitable" outcome
5. **Reveal the provocation** — a debrief explains the ambiguous wording, the inverted scoring, and the manufactured authority of the AI output. Includes a Restart button

---

## Running the prototype

This project is a single self-contained HTML file. No build step, server, or dependencies are required.

**Option 1 — Open directly in a browser**

```
open index.html          # macOS
start index.html         # Windows
xdg-open index.html      # Linux
```

Or drag `index.html` into any modern browser window.

**Option 2 — Serve locally (optional)**

If your browser restricts `file://` access:

```bash
# Python 3
python3 -m http.server 8080

# Then visit http://localhost:8080
```

---

## Project structure

```
module-3/
├── index.html   ← entire prototype (HTML + CSS + JS, no external dependencies)
└── README.md
```

---

## Technical notes

- Pure HTML, CSS, and vanilla JavaScript — no frameworks, no external libraries, no APIs
- No data is collected, stored, or transmitted at any point
- Responsive layout; tested at mobile (375 px) and desktop widths
- Keyboard-navigable: Tab to move between controls, arrow keys to move between answer options, Enter/Space to activate buttons
- Visible `:focus-visible` states throughout
- Scoring threshold: raw score < 30 → "Suitable"; ≥ 30 → "Not suitable" (inverted from what users would expect)
- Confidence percentage is `Math.random()` in the range 83–96 %

---

*Built for educational purposes as part of a university Module 3 provocation project.*
