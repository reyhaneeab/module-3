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
   | 3 | "I enjoy scrolling TikTok": people may mean they're social, Verdikt assumes they're not (fair reading: more TikTok = better fit) | yes |
   | 4 | Colour preference used as a fake predictor | no |
   | 5 | Honesty punished | yes |
   | 6 | Hidden attention check in small print | yes |
   | 7 | Modesty punished, self-promotion rewarded | yes |
   | 8 | "Express your creativity" in one sentence: only the word *painting* counts as creative | no |

3. **Analyzing:** about 4 seconds of made-up AI steps.
4. **Result:** match score, verdict and "key factors".
   - *Not suitable:* Apply is locked, then **Continue**.
   - *Suitable:* **Apply now**, confirmation screen, then **Continue**.
5. **Study survey (9 questions, about 1 minute):** required, no skip option. *Send answers* unlocks once questions 1 to 8 are answered; the last one is optional.
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
- **Key factors** on the result screen: up to 3 misreadings of the applicant's own answers that point the verdict's way, then the invented colour statistic (Q4), then the creativity line (Q8: "High creativity detected" only if they wrote *painting*, otherwise "No creativity detected"). Each factor shows a red warning or green check by its own tone.

To see both paths: answer once as an ideal candidate (expect "Not suitable") and once as a poor one (expect "Suitable").

---

## Study survey

Shown right after the result, before anyone knows about the twists, so it captures the unbiased reaction. Nine questions, about 1 minute.

| ID | Question | Answer type | What it helps analyse |
|---|---|---|---|
| `s1_first_reaction` | What was your first reaction to the result? | one of: It matched what I expected / I wondered what I had answered wrong / It did not match how I see myself / I instantly knew something was wrong with the AI / It felt too good to be true / I did not think much about it | Blame (self vs. AI); autonomy over self-representation |
| `s2_expected_result` | Before you saw the result, what did you expect Verdikt to say? | Suitable / Not suitable / I was not sure | What surprised them? |
| `s3_feelings` | How did you feel when you saw your result? | pick any of 8 | What surprised them? |
| `s4_fairness` | How fair does Verdikt's decision feel to you? | 1 to 5 | Tension: trust |
| `s5_trust` | How much do you trust Verdikt's assessment of you? | 1 to 5 | Tension: trust |
| `s6_explanation` | Did Verdikt's "key factors" make the decision feel more convincing? | 1 to 5 | Tension: epistemic character |
| `s7_responsible` | Who do you think is responsible for the decision you got? | one of 5 | Agency and responsibility |
| `s8_accept_real` | Would you accept an AI like this screening you in a real job application? | 1 to 5 | Reflection |
| `s9_comment` | Anything else you want to tell us? | optional text | What did they say? |

Everything except the optional comment is required; there is no skip option. The survey never mentions "tricks", so it doesn't give the twists away.

---

## What is saved

When a participant presses **Send answers**, one row is saved with these columns:

| Columns | What they contain |
|---|---|
| `response_id`, `submitted_at` | Random ID and timestamp |
| `device`, `screen_width` | `touch` or `mouse`, and window width in px |
| `verdikt_said`, `fair_reading`, `fair_score`, `match_shown_pct`, `applied` | The verdict shown, the fair reading (Good/Poor fit, 0 to 12), the match % shown, and whether they pressed Apply |
| `test_time_s` | Seconds from *Start assessment* to the last answer |
| `result_time_s` | Seconds spent on the result screen before pressing *Continue* or *Apply now* |
| `clicked_locked_apply`, `clicked_human_review` | How many times a rejected participant tried the locked *Apply now* button or the unavailable *Request human review* button |
| `q1_…` to `q8_…` | For every test question: `_answer`, `_value` (1 to 5 for scales), `_points` (scored ones), `_time_s` (seconds spent) |
| `q6_followed_small_print` | `yes` if they chose *Rarely* as the hidden instruction asked |
| `q8_keyword_found` | `yes` if their creativity sentence contained *painting* (Verdikt rated them creative), otherwise `no` |
| `s1_first_reaction` … `s9_comment` | Survey answers (scales as 1 to 5, options as text, multi-picks joined with `; `) |

No names, emails or IP addresses are collected. Test question 8 and the survey's text answers are free text, so remind participants not to write personal details.

---

## Analysis guide

How each prompt from the course's *Test and document* page maps to the data. The survey is kept short, so "say", "notice" and "misunderstand" lean on your observation notes and two quick questions in the in-person debrief (see below).

| Course prompt | Columns to use | What to look at |
|---|---|---|
| What did participants do? | `applied`, `clicked_locked_apply`, `clicked_human_review`, `result_time_s`, `q…_time_s` | Who tried to apply or ask for a human anyway; who lingered on the result; which questions slowed people down |
| What did they say? | `s9_comment` + your observation notes + debrief answers | Group what people said into themes, e.g. "blamed myself", "blamed the AI", "didn't notice anything" |
| What did they notice? | `s1_first_reaction` = "I instantly knew something was wrong with the AI" / "It felt too good to be true", debrief answers | Who saw through Verdikt, split by `verdikt_said` |
| What did they misunderstand? | `q1_answer` (double negative), `q6_followed_small_print`, slow `q…_time_s` | Where people were misled without realising it |
| What surprised them? | `s2_expected_result` vs `verdikt_said`, `s3_feelings` | Expected one verdict but got the other; how often "Surprised" or "Confused" was picked |
| Who did they blame? | `s1_first_reaction` ("answered wrong" = self, "something wrong with the AI" = AI), `s7_responsible`, split by `verdikt_said` and `fair_reading` | Do good-fit people who were rejected blame themselves first? Does their first reaction differ from who they later say is responsible? |
| Autonomy over self-representation (Aizenberg et al. 2025) | `s1_first_reaction` = "It did not match how I see myself", `q8_answer` vs `q8_keyword_found`, `clicked_human_review` | Aizenberg et al. describe it as people's ability to represent themselves and to *negotiate* how others see them. Look at who felt misrepresented, how their own creative sentence was reduced to a single keyword check, and who tried to ask for a human review (which Verdikt never offered) |
| What surprised you? | All of the above + your notes | Write this as a group after testing: results that didn't match your expectations |
| Did the intervention create the tension or reflection you expected? | `s4_fairness`, `s5_trust`, `s6_explanation`, `s7_responsible`, `s8_accept_real`, split by `fair_reading` | Did good-fit participants who were rejected still trust Verdikt or blame themselves? Did people find the explanation convincing even when it was nonsense? |

**Observation notes.** During each session, one group member writes down what the participant does and says out loud: hesitations, laughs, comments, re-reading a question. Note the time next to your notes so you can match them to the row's `submitted_at` later.

**Two debrief questions.** Before you explain the twists in person, ask: *"What do you think just happened?"* and *"Did anything seem odd to you?"* Write the answers in your notes. That covers "say" and "notice" without making the survey longer.

**Before real testing starts,** delete everything in the Google Sheet, including the header row. The questions have changed, so the script will rebuild clean headers on the first real response.

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
