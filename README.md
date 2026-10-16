# Verdikt: AI personality screening (provotype)

A single-page web prototype for **TIA400 Introduction to Human-centered AI** (University of Gothenburg), Module 3, *Provoke* orientation.

Verdikt pretends to be an AI personality test that screens applicants for a fictional job (Customer Experience Associate at the fictional Brightwave Studio). It is built to be **always wrong**:

- applicants who fit the role are told they are **not suitable** and the Apply button is locked
- applicants who don't fit are told they are **suitable** and can apply

The questions are worded so people misread them, and the "AI" explains every verdict with confident, misread reasons. After the result, every participant answers a short survey for our analysis. The page never reveals the twists; the group debriefs participants in person.

> Not a real employment assessment. Answers are saved only when the participant sends the survey at the end.

---

## Flow

1. **Landing:** fictional job ad and a *Start assessment* button.
2. **8 test questions**, one per screen:

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
4. **Result:** match score, verdict and "key factors".
   - *Not suitable:* Apply is locked, then **Continue**.
   - *Suitable:* **Apply now**, confirmation screen, then **Continue**.
5. **Study survey (8 questions, about 1 minute):** required, no skip option. *Send answers* unlocks once questions 1 to 7 are answered.
6. **Thank you** screen with *Start again for the next person*.

The twists are **never shown** on the page. The `twist` text in `CONFIG` is only a note for the group.

The **About this study** link in the footer opens a short, neutral note (fictional project, anonymous data) plus the facilitator tools (see *Getting the data*). It does not reveal the twists.

### Scoring

There is no real AI; it is a rule-based (Wizard-of-Oz) script, so the result is deterministic.

- Questions 1, 2, 3, 5, 6, 7 give 0 to 2 points each, read the way a fair recruiter would (max 12).
- **Good fit** = 7 or more points.
- **Verdikt's verdict is always the opposite.**
- Displayed match % always sits clearly on the verdict's side, never near 50%:

  | Fair score | 0 | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 | 9 | 10 | 11 | 12 |
  |---|---|---|---|---|---|---|---|---|---|---|---|---|---|
  | Verdikt says | Suitable | | | | | | Suitable | Not suitable | | | | | Not suitable |
  | Match shown | 96% | 90% | 85% | 79% | 73% | 68% | 62% | 38% | 32% | 26% | 20% | 14% | 8% |

  The ranges are set by `rejectRange` and `approveRange` in `CONFIG.scoring`.

To see both paths: answer once as an ideal candidate (expect "Not suitable") and once as a poor one (expect "Suitable").

---

## Study survey

Shown right after the result, before anyone knows about the twists, so it captures the unbiased reaction.

| ID | Question | Answer type | Lens |
|---|---|---|---|
| `s1_fairness` | How fair does Verdikt's decision feel to you? | 1 to 5 (very unfair to very fair) | Trust |
| `s2_trust` | How much do you trust Verdikt's assessment of you? | 1 to 5 | Trust |
| `s3_explanation` | Did Verdikt's "key factors" make the decision feel more convincing? | 1 to 5 | Epistemic character |
| `s4_feelings` | How did you feel when you saw your result? | pick any of 7 | (emotional response) |
| `s5_responsible` | Who do you think is responsible for the decision you got? | one of 5 | Agency and responsibility |
| `s6_confusing` | Did any of the questions feel confusing or unclear? | Yes, several / One or two / None | Uncertainty and ambiguity |
| `s7_accept_real` | Would you accept an AI like this screening you in a real job application? | 1 to 5 | Trust |
| `s8_comment` | Anything else you want to tell us? | optional text | |

Questions 1 to 7 are required; there is no skip option. Question 6 asks about confusion rather than "tricks" so it doesn't give the twist away; compare it with `q3_clicked_side` and `q6_followed_small_print` to see who was misled without noticing.

---

## What is saved

When a participant presses **Send answers**, one row is saved with these columns:

| Columns | What they contain |
|---|---|
| `response_id`, `submitted_at` | Random ID and timestamp |
| `device`, `screen_width` | `touch` or `mouse`, and window width in px |
| `verdikt_said`, `fair_reading`, `fair_score`, `match_shown_pct`, `applied` | The verdict shown, the fair reading (Good/Poor fit, 0 to 12), the match % shown, and whether they pressed Apply |
| `test_time_s` | Seconds from *Start assessment* to the last answer |
| `q1_…` to `q8_…` | For every test question: `_answer`, `_value` (1 to 5 for scales), `_points` (scored ones), `_time_s` (seconds spent) |
| `q3_clicked_side` | `left` / `right` / `middle` on the reversed scale (right = likely fell for the trick) |
| `q6_followed_small_print` | `yes` if they chose *Rarely* as the hidden instruction asked |
| `q8_keyword` | The word Verdikt pulled out of their sentence |
| `s1_fairness` … `s8_comment` | Survey answers (scales as 1 to 5, options as text, feelings joined with `; `) |

No names, emails or IP addresses are collected. Question 8 and the comment are free text, so remind participants not to write personal details.

---

## Getting the data

Answers are **always** saved on the device used for the test, and **also** sent to a Google Sheet if you connect one.

### Option A: on the testing device (no setup)

Best when everyone tests on your own laptop or phone.

1. Open **About this study** from the footer.
2. At the bottom, under *For facilitators*, press **Download CSV**.
3. Open the CSV in Excel, Google Sheets or Numbers.

*Delete saved responses* clears the device (press twice to confirm). The data lives in that browser only, so use the same browser for all tests and don't clear its site data.

### Option B: Google Sheet (works from any participant's phone)

Use a personal Google account (organisation accounts may block public web apps).

1. Create a new Google Sheet, for example *Verdikt responses*.
2. Open **Extensions → Apps Script**, delete the sample code and paste:

   ```js
   function doPost(e) {
     const lock = LockService.getScriptLock();
     lock.waitLock(10000);
     try {
       const sheet = SpreadsheetApp.getActiveSpreadsheet().getSheets()[0];
       const row = JSON.parse(e.postData.contents);
       const headers = sheet.getLastRow() ? sheet.getRange(1, 1, 1, sheet.getLastColumn()).getValues()[0] : [];
       Object.keys(row).forEach(function (key) {
         if (headers.indexOf(key) === -1) {
           headers.push(key);
           sheet.getRange(1, headers.length).setValue(key);
         }
       });
       const safe = function (v) { return typeof v === 'string' && /^[=+\-@]/.test(v) ? "'" + v : v; };
       sheet.appendRow(headers.map(function (h) { return h in row ? safe(row[h]) : ''; }));
       return ContentService.createTextOutput('ok');
     } finally {
       lock.releaseLock();
     }
   }
   ```

3. Click **Save**, then **Deploy → New deployment**.
4. Click the gear next to *Select type* and choose **Web app**.
5. Set **Execute as: Me** and **Who has access: Anyone**, then **Deploy**.
6. Authorise when Google asks (on the "Google hasn't verified this app" screen choose *Advanced → Go to … (unsafe)*; it is your own script).
7. Copy the **Web app URL** (it ends in `/exec`).
8. In `index.html`, find `endpoint: ''` inside `CONFIG.survey` and paste the URL between the quotes. Commit and push.
9. Do one test run and check that a row appears in the Sheet. *About this study* should now say *Google Sheet connected*.

If you edit the script later, use **Deploy → Manage deployments → Edit → Version: New version** so the URL stays the same.

The Web app URL is public in your page source, so anyone who finds it could add rows. That is acceptable for a short course study; delete the deployment when testing ends.

---

## Editing the text

All copy, test questions, survey questions, points and AI lines live in the `CONFIG` object at the top of the `<script>` in `index.html`. Change text there; the logic below it does not need to be touched. If you add or rename survey questions, new columns appear automatically in both the CSV and the Sheet.

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

- **Get consent before they start.** The survey can't be skipped, so tell participants up front that it's a student study and that their anonymous answers will be saved. Ask separately before recording screens or quoting anyone.
- **Debrief everyone in person after they send the survey.** The page never explains the twists, so you have to: tell them the test is designed to be wrong, and tell rejected participants that Verdikt rejected them *because* they fit the role. The table in your project doc lists every twist.
- If you want to interview people about their first reaction, do it on the result screen before they press *Continue*.
- Delete the collected data when the course is finished.

---

## Tech notes

- Plain HTML, CSS and vanilla JavaScript in one file. No framework, backend, cookies or analytics.
- Survey rows are kept in the browser's `localStorage` and, if configured, POSTed to a Google Apps Script web app.
- Responsive from 375 px phones to desktop; keyboard accessible with visible focus states; respects `prefers-reduced-motion`.
- Built with help from Claude (Anthropic) as an AI coding tool; disclose this in the design dossier.
