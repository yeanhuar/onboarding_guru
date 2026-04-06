# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

**Onboarding Guru** — an interactive onboarding plan generator for new joiners across SPX, SLS, and WMS product lines (Shopee Express ecosystem).

**Stack:** Plain HTML/CSS/JS — no build step, no framework.
**State:** Firebase Firestore (joiner progress: checkbox ticks, quiz answers, active day).
**Hosting:** GitHub Pages.
**Plan generation logic:** Defined in `SKILL.md`.

## Development

No build or install step. To preview locally:

```bash
npx serve .          # Serves on http://localhost:3000
# or just open index.html directly in a browser
```

To deploy: push to the `main` branch (or `gh-pages` branch, depending on GitHub Pages config). GitHub Pages serves from the repo root or `/docs` folder — set this in repo Settings → Pages.

## Firebase Setup

Firebase config is stored in `js/firebase-config.js` and imported via `<script type="module">` in `index.html`. The config object (apiKey, projectId, etc.) **is intentionally public** — Firebase client keys are identifiers, not secrets. Security is enforced by Firestore Security Rules, not key secrecy.

**Required Firebase Console setup:**
1. Enable **Firestore** in the Firebase project
2. Enable **Anonymous Authentication** (Auth → Sign-in method → Anonymous)
3. Deploy the Security Rules below

**Firestore Security Rules** (deploy via Firebase Console → Firestore → Rules):
```
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /plans/{document=**} {
      allow read, write: if request.auth != null;
    }
  }
}
```

**Anonymous auth pattern** — call before any Firestore write:
```js
import { getAuth, signInAnonymously } from 'https://www.gstatic.com/firebasejs/10.x.x/firebase-auth.js';
const auth = getAuth(app);
await signInAnonymously(auth);
```

Joiner state is keyed by `{name}-{startDate}` and stored under `plans/{joinerId}/state`.

## Project Structure

```
onboarding_guru/
  index.html              — Entry point / plan generator UI
  js/
    firebase-config.js    — Firebase app init (public config object)
    app.js                — Main app logic, plan rendering, state sync
  css/
    styles.css            — Styles (Tailwind CDN or hand-written)
  references/
    universal.md          — Day 1 content for ALL plans
    spx/                  — SPX sub-domain reference files
    sls/                  — SLS sub-domain reference files
    wms/                  — WMS sub-domain reference files
  SKILL.md                — Onboarding plan generator skill specification
```

## Onboarding Plan Generation

All plan generation logic, product line taxonomy, quiz rules, and UI spec are in `SKILL.md`. When generating or modifying onboarding plans, always read `SKILL.md` first and follow its rules exactly — especially:

- Day 1 is always the universal foundation block (from `references/universal.md`)
- Days 2–5 come from the sub-domain reference file(s)
- Quiz: 7 MCQ + 3 open-ended per day
- Never fabricate Confluence or Drive links — use only hardcoded defaults from reference files

## State Shape (Firestore document)

```js
{
  activeDay: 1,                        // currently viewed day tab
  checkedTasks: { "day1-task0": true },// task checkbox states
  adminChecklist: { "item0": true },   // Day 1 admin checklist states
  quizAnswers: { "day1-q0": "B" },     // MCQ selections
  quizSubmitted: { "day1": true },     // whether day quiz was submitted
  openEnded: { "day1-q7": "..." }      // open-ended quiz text
}
```
