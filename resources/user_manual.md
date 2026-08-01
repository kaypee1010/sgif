# SGIF User Manual
**K.P. Singh · SGT University**
*How to actually run this system — plain English, step by step*

---

## What you have and what each thing does

| File | What it is | When you use it |
|---|---|---|
| **Code.gs** | The brain — one Google Apps Script file running on your Sheet | Deployed once, never touched again |
| **student_registration.html** | Students sign up for competitions | A few days before each event |
| **student.html** | Students check their own quiz history and scores | Any time, self-service |
| **faculty_dashboard.html** | You filter and analyse results | After each quiz or event |
| **Quiz HTML files** | The actual quiz — one file per quiz | Generated fresh each time using the Master Prompt |

Everything talks to **one Google Sheet** and **one API URL**. You never need to touch Code.gs again after the first setup.

---

## First-time setup (do this once, ever)

**1. Create the Google Sheet**
- Go to sheets.google.com → New spreadsheet
- Name it: `SGIF — KP Singh`

**2. Add the Apps Script backend**
- Inside the sheet: Extensions → Apps Script
- Delete any existing code
- Paste the entire contents of `Code_gs_v1_6.js`
- Click Save

**3. Run setup**
- In Apps Script: click the function dropdown → select `setup` → click Run
- This creates all the tabs automatically (Config, Audit, Responses, Events, Students, Activities, Registrations)

**4. Deploy as a Web App**
- Click Deploy → New deployment
- Type: Web App
- Execute as: Me
- Who has access: Anyone
- Click Deploy → copy the `/exec` URL — **save this somewhere safe**

**5. Set your api_key**
- Go back to your Google Sheet → Config tab
- Find the row where Key = `api_key`
- Type a password of your choice in the Value column (e.g. `SGT2025KP`)
- This is your private key for the faculty dashboard — students never need it

**6. Upload your class roster to the Students tab**
- In the Students tab, fill in one row per student:
  - Roll Number · Name · Section · Branch · Semester · Cohort_ID · Status
  - Cohort_ID format: `BTECH_SEM7_2025` (Branch_Semester_Year, no spaces)
  - Status: `Active`
- Do this for each class. All classes go in the same tab, just different Cohort_IDs.

**7. Deploy your permanent HTML files to GitHub Pages**
- Go to your GitHub repo → upload these files:
  - `student_registration.html`
  - `student.html`
  - `faculty_dashboard.html`
- Open `student_registration.html` and `student.html` — find the line that says `PASTE_YOUR_FOUNDATION_EXEC_URL_HERE` and replace it with your `/exec` URL
- Do the same for `faculty_dashboard.html`
- Commit and push

---

## Running a lecture or lab quiz

**Step 1 — Generate the quiz (10–15 minutes)**
- Open a new Claude conversation
- Paste the entire contents of `Master_Prompt_v4.md` as your first message
- Claude will ask you which mode. Say **Lecture Mode** or **Lab Mode**
- Answer the questions Claude asks:
  - Branch, semester, time limit, number of questions
  - Quiz_ID (e.g. `MATLAB-L1` for MATLAB Lecture 1)
  - Cohort_ID (e.g. `BTECH_SEM7_2025` — must match what's in your Students tab)
  - Your API URL (paste the /exec URL)
  - Topics you want covered
- Claude generates a complete HTML file

**Step 2 — Set the three constants**
- Open the generated HTML file in any text editor
- Near the top of the `<script>` block, find and fill in:
```
API_URL   = 'your /exec URL'
QUIZ_ID   = 'MATLAB-L1'
COHORT_ID = 'BTECH_SEM7_2025'
```

**Step 3 — Upload to GitHub Pages**
- Upload the file as `matlab-l1.html` to your GitHub repo
- Your quiz is now live at `kpsingh.in/matlab-l1.html`

**Step 4 — Share with students**
- Show the URL on the projector in class
- Students open it on their phone or laptop
- They type their roll number → their name pre-fills from the roster
- They confirm and begin the quiz
- Results save automatically to your Google Sheet

**Step 5 — Check results**
- Open `faculty_dashboard.html`
- Enter your api_key in the API Key field
- Set Cohort_ID filter to `BTECH_SEM7_2025`
- Set Quiz filter to `MATLAB-L1`
- Click Load data
- See: who scored what, which topics the class struggled with

---

## Running a competition event (CodeChamps etc.)

**Week 1 — Set up**

1. Get HOD approval. Fix date, venue, duration.

2. Add one row to the Activities tab in your Google Sheet:

| Column | Example value |
|---|---|
| Activity_ID | `CODECHAMPS-01` |
| Title | `CodeChamps — Round 1` |
| Type | `CodeChamps` |
| Description | One sentence about the event |
| Venue | Lab 3, Block A |
| Activity Date | 15/08/2025 |
| Reg Start | 10/08/2025 |
| Reg End | 14/08/2025 |
| Capacity | 60 |
| Status | `DRAFT` |
| Eligibility | `BTech / BCA / MCA` |
| Quiz_IDs | `CC-01` |
| Cohort_ID | `SGT_LEAGUE_2025` |

3. Generate the quiz HTML (same as lecture, but choose **Event Mode** and use Quiz_ID `CC-01`, Cohort_ID `SGT_LEAGUE_2025`).

4. Upload `codechamps-01.html` to GitHub Pages. Keep the URL secret.

**Week 2 — Announce and run**

1. Change Status in Activities tab from `DRAFT` to `OPEN`.

2. Share `student_registration.html` URL with students — they register and get a Registration ID.

3. Two days before: check registration count in the Registrations tab.

4. Event day: share `codechamps-01.html` URL at the start time (not before).

5. After everyone submits: leaderboard is live. Announce top 3.

6. Change Status to `DONE`.

7. Open faculty dashboard → filter by `SGT_LEAGUE_2025` → check which topics the cohort struggled with → post one insight to class WhatsApp.

---

## What students do

**Before a competition:**
- Go to `student_registration.html`
- Find the event → click Register → fill name, roll, email etc.
- Get a Registration ID — save it (they need it to cancel)

**On quiz day (lecture, lab, or event):**
- Open the quiz URL you shared
- Type their roll number
- Their name pre-fills automatically (from roster or registration)
- They confirm and begin
- Answer questions, submit
- Get their score, grade, PDF automatically

**Any time — checking their own record:**
- Go to `student.html`
- Type their roll number
- See all their quiz attempts across all subjects and events
- Three tabs: All / Classroom / Events

---

## What you do after each quiz

1. Open `faculty_dashboard.html` in your browser
2. Enter api_key → set your filters → click Load
3. Switch between tabs:
   - **Overview** — total attempts, pass rate, average score
   - **Quiz Summary** — one row per quiz, hardest topic shown
   - **Topic Mastery** — bar chart of topics, sorted weakest first — this is your lesson plan
   - **Student List** — sorted weakest-first, top 3 weak topics per student

That weakest-topic list from the dashboard directly tells you what to cover in the next lecture. No manual analysis needed.

---

## Quiz naming — quick reference

Always follow this pattern so the dashboard filters work cleanly:

```
Lecture quizzes:   SUBJECT-LN        matlab-l1.html     MATLAB-L1
Lab quizzes:       SUBJECT-LABN      matlab-lab1.html   MATLAB-LAB1
Event quizzes:     BRAND-NN          codechamps-01.html CC-01

Cohort_ID (class): BRANCH_SEMN_YEAR  BTECH_SEM7_2025
Cohort_ID (event): SGT_LEAGUE_YEAR   SGT_LEAGUE_2025
```

---

## Common problems and fixes

**Students type their roll number but nothing pre-fills**
Their roll in the Students tab doesn't match exactly what they typed. Check for spaces, leading zeros, or capital/lowercase differences. Roll numbers in the sheet must be uppercase with no spaces.

**Faculty dashboard shows no data**
Two causes: (a) api_key is wrong — check the Config tab for the exact value, or (b) the Cohort_ID filter doesn't match what's stored in Responses — try leaving Cohort_ID blank to see everything, then narrow down.

**Leaderboard is empty**
The Responses tab is missing its header row. Go to the sheet, click on cell A1 of the Responses tab, and check if the first row says "Timestamp". If it's blank or has data instead of headers, insert a row above and type the headers — the exact list is in the Code.gs file under `RESPONSE_HEADERS_()`.

**PDF doesn't download automatically**
The student's network may be blocking the html2pdf.js library. The "Save PDF" button on the result screen lets them retry manually.

**A student submitted but no row appears**
The quiz uses fire-and-forget POST — no confirmation comes back. If the Events tab has a `QUIZ_START` row for that session but the Responses tab has no row, the student closed the browser before the submission completed. Their Events data is still there and usable.

---

## File checklist — everything you need

```
Permanent (deploy once):
☐ Code_gs_v1_6.js          → paste into Apps Script, deploy
☐ student_registration.html → paste /exec URL, push to GitHub Pages
☐ student.html             → paste /exec URL, push to GitHub Pages
☐ faculty_dashboard.html   → paste /exec URL, push to GitHub Pages

Generated per quiz (using Master Prompt v4.0):
☐ matlab-l1.html           → set 3 constants, push to GitHub Pages
☐ matlab-lab1.html         → set 3 constants, push to GitHub Pages
☐ aiml-l1.html             → set 3 constants, push to GitHub Pages
☐ codechamps-01.html       → set 3 constants, push to GitHub Pages
  (and so on for every quiz)
```

---

## Coming next

| Feature | When |
|---|---|
| `passport.html` — rich growth passport with trends | After 3+ quizzes with real data |
| Roster upload tool — web UI instead of manual paste | When manual CSV becomes inconvenient |

---

*SGIF Platform v1.6 · K.P. Singh · SGT University*
*One sheet. One backend. Every class. Every event. Every student.*
