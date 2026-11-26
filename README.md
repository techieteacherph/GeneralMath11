# Explain Explore – Math @11

**Grade 11 General Mathematics Lab** · Molave Vocational Technical School (MVTS)
Teacher: Neljohn C. Parojinog · *Explain Explore with Mr. John*

A free, installable web app for Grade 11 General Mathematics. Learners practise unlimited, freshly generated problems for every week of the DepEd Budget of Work, take a once-per-lesson check-up, earn XP and join a nickname-only leaderboard. Teachers manage rosters, lessons, videos and results from a simple Admin panel.

It runs in any modern browser, installs like an app on **iPhone, iPad and Android**, and works **offline** after the first visit.

---

## Features

| For learners | For teachers |
|---|---|
| Sign in with a **Student Code** (e.g. `01ASSH4ADLAON`) | Upload **one roster CSV per section** |
| Unlimited practice problems, generated fresh each time | Sync lessons, questions and videos from a **Google Sheet** |
| Quick-reference formulas and worked solutions | Results are sent automatically to a **Google Drive** folder |
| Weekly 5-item check-up (one attempt per lesson) | Download **results** and **class progress** as CSV |
| XP, levels and a nickname-only leaderboard | Attach slides, PDFs and videos to each lesson |
| Slides, handouts and videos under *View Notes* | Everything stays under your control – no accounts or servers to maintain |
| Works offline; installs to the Home Screen | |

## Quick start

**Learners:** open the school link → type your Student Code → *Continue* → pick a lesson. See the [User Guide](docs/USER_GUIDE.md).

**Teachers:** open the app → **Admin ▾** → enter the access code → upload rosters → *Sync lessons*. See the [User Guide](docs/USER_GUIDE.md#part-2--teachers).

## Repository contents

```
index.html                  The whole app (HTML + CSS + JavaScript)
manifest.webmanifest        Install settings (name, icons, colours)
sw.js                       Offline support (service worker)
logo.png                    Logo shown on the learner screen
icons/                      App icons for iOS, iPad and Android
privacy.html  terms.html    In-app Privacy Notice and Terms (from the .md files)
roster.json                 (optional) exported roster so learners' phones can check codes
templates/                  CSV templates: lessons sheet and class roster
docs/USER_GUIDE.md          Step-by-step guide for learners and teachers
docs/DOCUMENTATION.md       Technical documentation and maintenance
PRIVACY.md  TERMS.md        Data Privacy Notice and Terms and Conditions
```

## Publishing in short

1. Put the files above in a GitHub repository (files at the top level, not inside a folder).
2. In Cloudflare: **Workers & Pages → Create → Pages → Connect to Git**, choose the repo, leave *Build command* empty, output directory `/`.
3. Add your domain under **Custom domains**.
4. Every commit to GitHub re-publishes the site automatically.

Full steps, configuration and troubleshooting are in [docs/DOCUMENTATION.md](docs/DOCUMENTATION.md).

## Documentation

- [User Guide](docs/USER_GUIDE.md) – learners and teachers
- [Technical Documentation](docs/DOCUMENTATION.md) – architecture, setup, data formats, deployment, maintenance
- [Data Privacy Notice](PRIVACY.md)
- [Terms and Conditions](TERMS.md)

## Important notes

- The teacher access code is a **convenience lock, not real security** (see *Known limitations* in the technical documentation).
- Learner data is minimal and kept mostly on the learner's own device. Read the [Privacy Notice](PRIVACY.md) before using the app with learners.
- Replace every `[bracketed placeholder]` in `PRIVACY.md` and `TERMS.md` (contacts, retention period), then regenerate `privacy.html` and `terms.html` or edit them to match.

## Credits and rights

Competencies follow the DepEd Grade 11 General Mathematics Budget of Work. DepEd and school marks belong to their owners.
© 2026 Neljohn C. Parojinog (Explain Explore with Mr. John). All rights reserved unless stated otherwise.
