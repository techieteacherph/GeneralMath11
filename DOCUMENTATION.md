# Technical Documentation – Explain Explore · Math @11

Version: 2026 (Student Code + multi-section rosters + installable app)
Audience: the teacher/administrator and anyone maintaining the site.

---

## 1. Overview

A single-page web app written in plain HTML, CSS and JavaScript. There is **no build step, no database and no server code of its own**. Everything is stored in the learner's browser, and optional teacher data comes from Google Sheets/Drive.

```
 Learner phone / tablet (browser or installed app)
   │  loads files
   ▼
 Cloudflare Pages  ◄── auto-deploys ──  GitHub repository
   (hosts index.html, icons, sw.js, roster.json …)

 Learner device ──read──►  Google Sheet   (lessons, questions, video links)
 Learner device ──read──►  Google Drive   (optional: lesson packs, links.json, leaderboard.json)
 Learner device ──POST──►  Google Apps Script web app ──writes──► Google Drive folder (results)
 Learner device ──embed─►  YouTube (youtube-nocookie.com), only when "Watch here" is tapped
```

## 2. Files

| File | Purpose |
|---|---|
| `index.html` | The entire app, including styles, scripts and the two header logos. |
| `manifest.webmanifest` | Makes the site installable: name, icons, colours, standalone display. |
| `sw.js` | Service worker: caches the app for offline use (network first, cache as fallback). |
| `logo.png` | Logo on the learner sign-in screen (falls back to `icons/logo.png`). |
| `icons/` | `icon-192.png`, `icon-512.png`, `icon-maskable-512.png` (Android), `apple-touch-icon.png` (iOS/iPad), `favicon-32.png`, `logo.png`. |
| `roster.json` | Optional public roster export (section, number, last name, code). |
| `privacy.html`, `terms.html` | In-app versions of `PRIVACY.md` and `TERMS.md`. |
| `templates/` | CSV templates for the lessons sheet and class roster. |

Keep `index.html`, `manifest.webmanifest`, `sw.js` and `logo.png` in the **same folder** (the repository root).

## 3. Deployment (GitHub → Cloudflare Pages → your domain)

Cloudflare's menu names change occasionally; the steps below show the usual path.

1. **GitHub:** create a repository and upload all files from section 2 at the top level.
2. **Cloudflare:** *Workers & Pages → Create → Pages → Connect to Git*. Authorize GitHub and select the repository. Production branch `main`, **Build command: empty**, **Build output directory: `/`**. Save and deploy. A test address such as `yourproject.pages.dev` is created.
3. **Domain:** open the project → *Custom domains → Set up a custom domain*. A subdomain such as `math.<your-domain>` is recommended. Because the domain is on Cloudflare, DNS and the HTTPS certificate are set up for you. Wait until the status shows **Active**.
4. **Update:** edit or upload files on GitHub and commit. Cloudflare redeploys within about a minute.
5. **Roll back:** in Cloudflare open the project's *Deployments*, choose an earlier deployment and roll back. Or revert the commit on GitHub.

HTTPS is required for installing the app and for offline mode. It does not work when `index.html` is opened from a file or from Drive.

## 4. Configuration

Settings live in the `cfg` object near the top of the script in `index.html` and can be overridden per device in **Admin → Settings** (saved in that browser only).

| Key | Meaning | Default |
|---|---|---|
| `sheet` | Google Sheet ID for video links (first tab) and lessons | MVTS sheet |
| `lsheet` | Separate lessons sheet ID (blank = same as `sheet`) | blank |
| `ltab` | Name of the questions tab | `Lessons` |
| `sec` | Section label shown on the sign-in screen | `Grade 11 – ASSH4` |
| `folder` | Drive folder where results are written | set in file |
| `lib` | Drive folder with course files | set in file |
| `key` | Google API key (only needed to list Drive files) | blank |
| `hook` | Apps Script web-app URL that receives results | deployed `/exec` URL |
| `pin` | Teacher access code | `MVTS2026` |

To change a default for **everyone**, edit the line starting `const cfg=Object.assign({…` in `index.html`, then commit. Change `pin` before going live.

## 5. Student Codes and rosters

**Format:** `NN` + `SECTION` + `LASTNAME`, for example `01ASSH4ADLAON`.

- `NN` is the class number from the roster (zero-padded to 2 digits).
- `SECTION` is typed by the teacher when uploading (uppercase letters/digits only).
- `LASTNAME` is uppercased with accents removed and everything except letters and digits dropped (*Peña* → `PENA`, *Dela Cruz* → `DELACRUZ`).
- When a learner types a code, the app uppercases it and removes spaces and symbols. A one-digit number (`1ASSH4…`) is also accepted.

**CSV import** accepts:
- `No., Gender, Last Name, First Name` with or without a header (the first header may also read `Column1`),
- `No., Last Name, First Name`,
- the older `Gender, Last Name, First Name` (numbered by row order).

**Multiple sections:** each upload is tagged with the section typed on the screen and **replaces only that section**. Rosters are stored in the browser (`mvts11_roster`).

**Public roster (`roster.json`):** *Admin → Class rosters → Export roster.json*. Each entry is `{"no":1,"sec":"ASSH4","ln":"Adlaon","code":"01ASSH4ADLAON"}`. First names and gender are never exported. The app loads this file when it starts and adds codes it does not already have. If no roster is available on a device at all, the typed code is accepted without checking.

## 6. Lessons, questions and files

### 6.1 Lesson list
The 26 lessons of the Budget of Work (three terms) are built into `index.html` in the array `L`. Each entry holds term, week, domain, title, a topic key for the practice generator and the learning competencies. Lessons are identified as `term-week` (for example `2-5`).

### 6.2 Google Sheet
- **First tab** (video links): column A starts with a code matching `T[1-3]W<number>`; any `http(s)` links in the other columns are classified as video (YouTube or other), slides (`.pptx` / Google Slides) or PDF.
- **`Lessons` tab** (questions): `Code, Lesson title, Intro, Question, Answer, Unit (optional), Worked solution (optional)`. One row per question. Title and intro are read from the first row of each lesson. A number-looking answer (including `₱`, commas, `%`) is treated as numeric; other answers are compared as text (case-insensitive). `True`/`False` answers show a dropdown.
- A code matches the **latest lesson whose week is less than or equal** to the code's week.
- The sheet must be shared **Anyone with the link – Viewer**. It is read through Google's public CSV endpoint; no sign-in is used.

### 6.3 Drive files (optional)
Requires a Google API key with the Drive API enabled and the folders shared as *Anyone with the link – Viewer*.
- `T2W5.json` – extra lesson pack: `{"title":"…","intro":"…","items":[{"q":"…","a":42,"u":"m²","s":"worked solution"}],"video":"…","slides":"…","pdf":"…"}`
- `T2W5-slides.pptx`, `T2W5-handout.pdf` – appear under **View Notes**.
- `links.json` – exported from *Videos, slides and PDFs*.
- `leaderboard.json` – exported from *Settings → Export leaderboard.json* (nicknames and XP only).

### 6.4 Answer checking
Numeric answers are accepted within ±0.2 % (minimum tolerance 0.02). Text answers must match ignoring case.

## 7. Scoring and progress

| Item | Rule |
|---|---|
| Practice problems | Random each time. +10 XP per correct answer, +5 bonus at a streak of 3 or more. |
| Lesson progress | `min(100, correct practice answers × 20) %`. |
| Mastery % | Average lesson progress across all lessons. |
| Check-up | 5 items, one submission per lesson per device, +20 XP per correct item. |
| Level | `floor(XP / 100) + 1`, titles Rookie, Solver, Analyst, Strategist, Master. |
| Nickname | 3–14 letters, numbers, spaces, `.` `-` `_`; unique on the device. |
| Leaderboard | Top 10 nicknames by XP, from this device plus `leaderboard.json`. |

## 8. Receiving results in Google Drive (Apps Script)

One-time setup:

1. Open [script.google.com](https://script.google.com), create a project and paste:

```javascript
function doPost(e){
  var f = DriveApp.getFolderById('YOUR_RESULTS_FOLDER_ID'), d = JSON.parse(e.postData.contents);
  f.createFile('result_'+d.week+'_'+d.name.replace(/\W+/g,'_')+'_'+Date.now()+'.json', JSON.stringify(d));
  return ContentService.createTextOutput('ok');
}
```

2. **Deploy → New deployment → Web app.** *Execute as:* **Me**. *Who has access:* **Anyone**.
3. Authorize when asked, then copy the **Web app URL** (ends in `/exec`).
4. Paste it in *Admin → Settings → Results web-app URL* (or in `cfg.hook`).
5. **After editing the script:** *Deploy → Manage deployments → Edit (pencil) → Version: New version → Deploy.* This keeps the same URL. Creating a brand-new deployment gives a new URL that you must update in the app.

**Record sent for each check-up**

```json
{"nick":"MathWiz","xp":340,"key":"01ASSH4ADLAON","name":"Juan Adlaon","sec":"ASSH4",
 "week":"2-5","title":"Second Term Week 5: Length, area and perimeter of residential spaces",
 "score":4,"total":5,"at":"2026-11-26T08:15:00.000Z","sent":0}
```

The learner's individual answers are **not** sent. Results wait in a queue on the device when offline and are sent later.

## 9. Installable app and offline use

- `manifest.webmanifest` defines the name *Explain Explore – Math @11* (short name *Math @11*), standalone display, any orientation and the icons. The maskable icon has extra margin so Android can crop it into a circle or rounded square.
- `index.html` contains the iOS tags (`apple-mobile-web-app-*`, `apple-touch-icon`) and `viewport-fit=cover` with safe-area padding for notches and landscape.
- `sw.js` caches the core files when installed. For every same-site request it **tries the network first and falls back to the cache**, so new deployments reach users on their next online visit. It never touches requests to Google or YouTube.
- Android shows an **Install** banner; iOS/iPad shows an **Add to Home Screen** hint. Both can be dismissed ("Not now").
- To force every device to refresh the cache after a big change, change the `V` value at the top of `sw.js` (for example `mvts-g11-v3`) and commit.

## 10. Data stored on the device

All keys are in the browser's `localStorage` and start with `mvts11_`:

| Key | Content |
|---|---|
| `prof` | Signed-in learner: name, section, Student Code |
| `prog` | Practice results per lesson |
| `locks` | Submitted check-ups (answers, score, time) |
| `recs` | Check-up result records and whether each was sent |
| `xp`, `nicks`, `board` | XP, nicknames, imported leaderboard |
| `roster` | Rosters uploaded or loaded on this device |
| `cfg` | Settings changed on this device |
| `sp`, `sv`, `packs`, `files`, `vids`, `rvids`, `assign`, `chk`, `sync` | Synced lessons, links and lists |

Clearing the site's data in the browser removes all of this.

## 11. Known limitations

1. **The access code is not secure.** It is stored in the page's code, so anyone who views the source can read it. It only keeps casual users out of the Admin panel. Do not rely on it to protect personal data.
2. **Anyone can post to the Apps Script URL.** It is deployed with access "Anyone" and the URL is in the page, so a person could send fake results. Treat result files as records to review, not as tamper-proof grades.
3. **"Sent" is not confirmation.** Results are posted without reading a reply, so the app marks them sent when the request goes out. Check the Drive folder for the actual files.
4. **Check-up lock is per device.** Clearing site data or using another device allows another attempt.
5. **Progress and rosters are per device.** They do not follow a learner to another phone. Only check-up results (sent to Drive) are shared with the teacher.
6. **The leaderboard is local** plus whatever `leaderboard.json` you export.
7. **A public `roster.json`** reveals section, class numbers and last names to anyone who knows the address.
8. **Browsers may delete stored data** on devices that are low on storage or unused for a long time (especially Safari).

Possible improvements: hash the codes in `roster.json`, protect the Apps Script with a shared secret, move results to a Google Sheet with duplicate detection, and add a server-side teacher login.

## 12. Troubleshooting

| Problem | Likely cause and fix |
|---|---|
| Logo shows only the alt text | `logo.png` is not in the repository root. Upload it there (or in `icons/`). Reload twice to refresh the cache. |
| Old version still appears | Reload twice, or change `V` in `sw.js` and commit. |
| No Install banner on Android | The page must be on https and not already installed. Use the browser menu → Install app. |
| iPhone has no Install button | Use Safari → Share → Add to Home Screen (Chrome on iOS cannot install). |
| "Code not found" | Roster not loaded on that device. Export `roster.json` to GitHub, or check the spelling and section. |
| Roster upload says "Type the section first" | Fill in the Section box before choosing the file. |
| "No students found" | Check the CSV columns (No., Gender, Last Name, First Name) and that it is saved as CSV. |
| Sync lessons: "not reachable" | Sheet not shared as *Anyone with the link – Viewer*, wrong Sheet ID, or the `Lessons` tab name differs. |
| Results stay "queued" | Device offline, or the Results web-app URL is missing or wrong. |
| No files appear in Drive | Apps Script not deployed as *Web app / Anyone*, wrong folder ID in the script, or the script was edited without publishing a **New version**. |
| Videos do not play | Needs internet. The video must allow embedding. |

## 12.1 Before you go live (checklist)

- [ ] Change the access code (`pin`).
- [ ] Set your real section label and the Sheet/Drive IDs.
- [ ] Deploy the Apps Script and test one check-up end to end.
- [ ] Replace placeholders in `PRIVACY.md` and `TERMS.md`; have the school's Data Protection Officer review them; update `privacy.html` and `terms.html` to match.
- [ ] Get parent/guardian consent as required by your school.
- [ ] Decide whether to publish `roster.json`.
- [ ] Test installing on one iPhone/iPad and one Android device.

## 13. Maintenance

- **Each term:** review the Sheet, remove old result files from Drive, update the section label and rosters.
- **Each school year:** replace rosters, delete the previous year's results according to the retention period in the Privacy Notice, review the access code.
- **Backups:** keep the GitHub repository (it is your backup of the app) and the Drive results folder. Download *Class progress CSV* regularly.

## 14. Changelog

| Version | Notes |
|---|---|
| ver00 | Original single-file app with name selection and a long admin form. |
| 2026 update | Simplified Admin panel; Student Code sign-in; multiple section rosters and `roster.json`; Explain Explore logo; installable on iOS, iPad and Android with offline support; Privacy Notice and Terms. |
