# Verdikt: AI personality screening (provotype)

A single-page web prototype for **TIA400 Introduction to Human-centered AI** (University of Gothenburg), Module 3, *Provoke* orientation.

Verdikt pretends to be an AI personality test that screens applicants for a fictional job (Customer Experience Associate at the fictional Brightwave Studio). It is built to be **always wrong**:

- applicants who fit the role are told they are **not suitable** and the Apply button is locked
- applicants who don't fit are told they are **suitable** and can apply

The questions are worded so people misread them, and the "AI" explains every verdict with confident, misread reasons. An **About this study** debrief (footer link, any screen) reveals the twist.

> Not a real employment assessment. No answers are stored, sent or tracked.

---

## How it works

1. **Landing:** fictional job ad and a *Start assessment* button.
2. **8 questions**, one per screen:

   | # | Twist | Scored |
   |---|---|---|
   | 1 | Double negative | yes |
   | 2 | Idiom read literally ("under fire") | yes |
   | 3 | 1 to 5 scale silently reversed | yes |
   | 4 | Colour preference used as a fake predictor | no |
   | 5 | Honesty punished | yes |
   | 6 | Hidden attention check in small print | yes |
   | 7 | Modesty punished, self-promotion rewarded | yes |
   | 8 | Free text, one keyword pulled out of context | no |

3. **Analyzing:** about 4 seconds of made-up AI steps.
4. **Result:** match score, verdict and "key factors". Apply is locked or open.
5. **Debrief:** fair reading vs. Verdikt's verdict, then each question's answer, what Verdikt "heard", and the twist.

### Scoring

There is no real AI; it is a rule-based (Wizard-of-Oz) script, so the result is deterministic.

- Questions 1, 2, 3, 5, 6, 7 give 0 to 2 points each, read the way a fair recruiter would (max 12).
- **Good fit** = 7 or more points.
- **Verdikt's verdict is always the opposite.**
- Displayed match % = `clamp(100 - round(score / 12 * 100), 8, 96)`.

To see both paths: answer once as an ideal candidate (expect "Not suitable") and once as a poor one (expect "Suitable").

---

## Editing the text

All copy, questions, options, points and AI lines live in the `CONFIG` object at the top of the `<script>` in `index.html`. Change text there; the logic below it does not need to be touched.

---

## Running locally

It is one self-contained file with no build step.

- Double-click `index.html`, or drag it into a browser.
- Optional local server: `python3 -m http.server 8080`, then open http://localhost:8080

The Inter font loads from Google Fonts when online and falls back to the system font offline.

---

## Publishing for participants (free)

**GitHub Pages**

1. Commit and push `index.html` to GitHub.
2. In the repo, go to **Settings → Pages**.
3. Under **Build and deployment**, choose **Deploy from a branch**, pick the branch (for example `version3.0`, or `main` after merging) and the `/ (root)` folder, then **Save**.
4. After a minute or two the site is live at `https://<username>.github.io/module-3/`.

Pages on a free account needs a **public** repository (private repos need GitHub Pro, which is included in the GitHub Student Developer Pack).

**Netlify Drop**

1. Go to https://app.netlify.com/drop
2. Drag the project folder onto the page. You get a public link straight away.
3. Sign in (free) to keep the site and give it a nicer name.

---

## Testing with participants

- Ask for consent before recording screens or quoting anyone.
- Let the participant finish, ask your first interview questions, and only then open **About this study** together.
- Tell rejected participants that Verdikt rejected them *because* they fit the role.

---

## Tech notes

- Plain HTML, CSS and vanilla JavaScript in one file. No framework, backend, API calls, cookies, localStorage or analytics.
- Responsive from 375 px phones to desktop; keyboard accessible with visible focus states; respects `prefers-reduced-motion`.
- Built with help from Claude (Anthropic) as an AI coding tool; disclose this in the design dossier.
