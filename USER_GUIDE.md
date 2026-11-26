# User Guide – Explain Explore · Math @11

This guide has two parts: **Part 1 – Learners** and **Part 2 – Teachers**.

---

## Part 1 – Learners

### 1. Install the app (recommended)

You can use the app in a browser, but installing it gives you an icon on your phone and lets it open offline.

**iPhone or iPad (use Safari)**
1. Open the school link.
2. Tap the **Share** button.
3. Tap **Add to Home Screen**, then **Add**.

**Android (Chrome)**
1. Open the school link.
2. Tap **Install** on the banner (or the browser menu ⋮ → **Install app**).

Open the app once while online so it can save itself for offline use.

### 2. Sign in with your Student Code

Your Student Code is made of three parts, written together with no spaces:

| Part | What it is | Example |
|---|---|---|
| Class number | Your number in the class list, 2 digits | `01` |
| Section | Your section code | `ASSH4` |
| Last name | Your last name, letters only | `ADLAON` |

Example: **`01ASSH4ADLAON`**

- Capital or small letters both work. Spaces and dashes are ignored.
- Last names with spaces or accents are written plainly: *Dela Cruz* → `DELACRUZ`, *Peña* → `PENA`.
- If the app says **"Code not found"**, check your class number, section and spelling, then ask your teacher.

Type the code and tap **Continue**. To let another learner use the same device, tap **Switch learner**.

### 3. Pick a lesson

Tap **Lessons**, choose the **term** (First, Second or Third), then tap a week. Each lesson page has:

- **Practice** – a new problem every time. Type your answer and tap **Check**. Tap the hint button to see the worked solution.
- **Quick reference** – the key formulas for the topic.
- **View Notes** – slides, PDF handouts and videos your teacher uploaded (needs internet).
- **Check-up** – a 5-question quiz for the lesson.

### 4. Practice

- Practice is **unlimited** and nothing is sent to your teacher.
- Numbers are checked with a small tolerance, so rounding slightly differently is fine. For True/False items, pick from the list.
- Each correct answer earns **10 XP**, plus a bonus when you get 3 or more in a row.

### 5. The check-up

- Answer **all 5 items**, then tap **Submit**.
- You can submit **only once** per lesson on your device, so review first.
- Each correct item earns **20 XP**.
- Your score is saved on your device and sent to your teacher when you are online. If you are offline, it waits and sends later.

### 6. My progress, XP and the leaderboard

- **My progress** shows your percentage for each lesson and your check-up scores.
- Your **level** goes up every 100 XP: Rookie → Solver → Analyst → Strategist → Master.
- On **Home**, set a **nickname** (3–14 letters, numbers or spaces). Do **not** use your real name. The **Leaderboard** shows nicknames and XP only.

### 7. Learner FAQ

| Question | Answer |
|---|---|
| Does it need internet? | Practice and check-ups work offline. Videos, slides and syncing need internet. |
| I cleared my browser data. Is my progress gone? | Progress is stored on your device, so yes, clearing site data removes it. Ask your teacher about check-up scores that were already sent. |
| Can I submit the check-up again? | No. One submission per lesson. |
| Can I use a different phone? | Yes, sign in with your code, but progress does not move between devices. |
| Nickname is taken | Try another. Nicknames must be unique on the device. |

---

## Part 2 – Teachers

### 1. Open the Admin panel

1. Tap **Admin ▾** at the top right.
2. Enter the **access code** and tap **Open**. (Change it under **Settings**.)
3. Tap **Lock** when you finish, especially on a shared device.

After unlocking you will see three tiles (**Students**, **Check-ups on this device**, **Waiting to send**) and four buttons:

| Button | What it does |
|---|---|
| **Sync lessons** | Downloads your questions, videos, slides and handouts from the Google Sheet (and Drive, if configured). |
| **Send results** | Uploads queued check-up results to your Drive folder. This also happens automatically when a learner submits online. |
| **Results CSV** | Downloads all check-up results found on this device. |
| **Class progress CSV** | Downloads every student with Student Code, section, mastery % and check-ups done. |

### 2. Upload class rosters (one per section)

**Prepare a CSV** with these columns (a header row is fine):

```
No.,Gender,Last Name,First Name
1,M,Adlaon,Juan
2,F,Dela Cruz,Maria Luz
```

A template is in `templates/roster_template.csv`.

- *No.* is the class number. If a file has no number column, rows are numbered in order.
- The old format `Gender, Last Name, First Name` also works.

**Upload**
1. Admin → **Class rosters**.
2. Type the **Section** (for example `ASSH4`).
3. Choose the **Roster CSV**. The app confirms how many students were added.
4. Repeat for each section.

Uploading a section again **replaces only that section**. The **Remove** button next to a section deletes it from this device. The table lists every Student Code so you can give learners their codes.

**Make codes work on learners' phones**
The roster you upload lives in *your* browser. To let learners' own phones check their codes:
1. Click **Export roster.json**.
2. Upload `roster.json` to the main folder of your GitHub repository.
3. Learners' devices pick it up the next time they open the app online.

`roster.json` is public on your website. It contains only section, class number and last name (no first names or gender). Read the [Privacy Notice](../PRIVACY.md) before publishing it.

### 3. Add lessons and questions (Google Sheet)

1. Create a Google Sheet. Share it as **Anyone with the link – Viewer**.
2. **First tab – videos and files:** column A = lesson code (`T2W5` = Term 2, Week 5), columns B onward = YouTube, slide or PDF links.
3. **Tab named `Lessons` – questions:** one row per question, using the columns below (template: `templates/MVTS_Lessons_Sheet_Template.csv`).

| Column | Content |
|---|---|
| Code | `T2W5` |
| Lesson title | Only on the first row of a lesson |
| Intro | Short note, first row of a lesson |
| Question | The question text |
| Answer | A number or `True` / `False` / short text |
| Unit (optional) | `m²`, `₱`, … |
| Worked solution (optional) | Shown after the learner checks |

4. Admin → **Settings**, paste the **Sheet ID** (the long text between `/d/` and `/edit` in the sheet's address).
5. Tap **Sync lessons**. Learners receive the update automatically the next time they are online.

Tip: a code like `T2W4` is matched to the nearest lesson at or before that week (for example Weeks 3–4 share one lesson).

### 4. Attach slides, PDFs and videos

- Easiest: put the links in the first tab of the Google Sheet, then **Sync lessons**.
- Or use Admin → **Videos, slides and PDFs**: pick the lesson, the type, paste the link and tap **Add link**. Links added here stay on this device; use **Export links.json** and upload it to your Drive folder to share them.

### 5. Collect results

Check-up results go to the Google Drive folder you set up, one file per submission. See the technical documentation for the one-time Apps Script setup. In the app:

- **Check-up results** shows submissions on *this* device and whether each was sent.
- Results from learners' own phones arrive in the Drive folder.

### 6. A simple weekly routine

1. Sync lessons on Monday.
2. Announce the week's lesson and check-up.
3. Download **Class progress CSV** on Friday and record it in your class record.
4. Keep the Drive folder tidy (move old result files into a subfolder each term).

### 7. Teacher FAQ

| Question | Answer |
|---|---|
| A learner forgot their code | Open **Class rosters** and read it from the table. |
| A learner is not in the roster | Add them to the CSV and re-upload that section, then re-export `roster.json`. |
| A learner needs to retake a check-up | The lock is on their device. Ask them to clear the site's data (they will also lose practice progress), then remove the old result file in Drive if needed. |
| "Sync lessons" says not reachable | Make sure the Sheet is shared as *Anyone with the link – Viewer* and the Sheet ID is correct. |
| I changed settings but another device did not update | Settings are saved per device. Change the defaults in `index.html` to change them for everyone. |
