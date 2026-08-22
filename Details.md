Let's set you up properly. You're literally on the right screen right now — I'll tell you exactly what to type into it in a second. First, the stack and structure so the whole team knows what they're building.

## Tech Stack (optimized for "team of 4, 3 days, need it deployed and working")

| Layer | Choice | Why |
|---|---|---|
| Frontend + Backend | **Next.js (React)** | One repo, one deploy (Vercel), API routes live right next to your UI — no separate backend service to manage under time pressure |
| Styling | **Tailwind CSS** | Fast to style without fighting CSS files, works natively in Next.js |
| Database | **Supabase (Postgres)** | Free, 5-minute setup, gives you a real "wired into a database" story for judges. *(If setup eats too much time on Day 1, fall back to a single `data/hackathons.json` file — still works, just less impressive)* |
| Scraper orchestration | **Node.js scripts** calling the Bright Data CLI/API | This is your core "Use of Scraper Studio" score — keep it front and center |
| Deployment | **Vercel** | You've used it before, it's free, and it's a one-command deploy |
| Scheduling (stretch) | **GitHub Actions cron** | Optional Day-3 bonus — matches idea #5 from Bright Data's own list, makes your project look "production" |

## Folder structure

```
buildradar/
├── README.md
├── .env.example              # BRIGHTDATA_API_KEY, SUPABASE_URL, etc — never commit real keys
├── .gitignore
├── package.json
│
├── scripts/                          # scraper orchestration (Pritam owns this)
│   ├── brightdata.js                 # wrapper: trigger/run/heal via bdata API
│   ├── run-all-scrapers.js           # calls all 4 collectors, saves raw results
│   ├── heal-check.js                 # if result is empty -> call bdata heal automatically
│   └── seed-dummy-scraper.js         # your dummy site, for the self-heal demo
│
├── lib/
│   ├── normalize.js                  # per-platform data -> one common schema
│   ├── db.js                         # supabase client (or json read/write fallback)
│   └── rank.js                       # sort by deadline, filter by track/city
│
├── data/
│   └── hackathons.json               # local cache / fallback if DB setup is skipped
│
├── pages/
│   ├── index.jsx                     # the dashboard itself
│   └── api/
│       ├── hackathons.js             # GET — dashboard reads from here
│       └── refresh.js                # POST — triggers a fresh scrape run
│
├── components/
│   ├── HackathonCard.jsx
│   ├── FilterBar.jsx                 # filter by tech track / virtual-vs-inperson
│   ├── CountdownTimer.jsx
│   └── SelfHealBadge.jsx             # small "last healed: 2 min ago" UI touch — good for demo
│
└── .github/workflows/
    └── scrape-cron.yml               # optional Day-3 bonus
```

## Step-by-step, from absolute zero

**Step 1 — Repo & team split (do this first, 15 min)**
```
git init buildradar
```
Push to GitHub as **public** (rule requires this). Suggested role split:
- **You**: `scripts/` + `lib/` — scraper orchestration, normalization, heal logic (your strongest area)
- **1 teammate**: frontend (`pages/`, `components/`) — dashboard UI
- **1 teammate**: scraper creation for the 4 real sites using the **web UI you're already on** (no-code, perfect if they're less comfortable with terminal)
- **1 teammate**: dummy site (simple static hackathon-listing page) + self-heal testing + README/demo video prep

**Step 2 — Right now, on your current screen**, test feasibility before anything else. Type this in:
- **Website URL**: `https://devfolio.co/hackathons`
- **Additional instructions**: `Extract for each hackathon listed: name, hosting platform, prize pool amount, virtual or in-person, city, tech tracks/themes, registration deadline, application URL`

Click "Start scraping with AI" and see what comes back. This single test tells you if the whole idea is feasible — do it before building anything else.

**Step 3 — Repeat Step 2 for the other 3 sites** (DoraHacks, Devpost, Unstop), get 4 Collector IDs (`c_...`), write them down somewhere shared (a pinned Discord message or a `.env.example` entry).

**Step 4 — `npm init` the Next.js app**, set up Tailwind, get a blank dashboard deploying to Vercel immediately (even empty) — so deployment is never a Day-3 surprise.

**Step 5 — Build `lib/normalize.js`** — this is your real technical-excellence work. Each platform will format deadlines/prizes differently; write one function per platform that maps to your common schema.

**Step 6 — Wire `scripts/run-all-scrapers.js`** to call all 4 collectors, normalize, and save to Supabase (or the JSON fallback).

**Step 7 — Build the dashboard** — cards, countdown timers, filters, reading from `/api/hackathons`.

**Step 8 — Self-heal demo** — build your dummy site, get a scraper running against it successfully, then deliberately break its HTML and confirm `bdata scraper heal` recovers it. **Run this cycle 3-4 times** before demo day so it's reliable, not lucky.

**Step 9 — Polish**: README, example JSON output saved in repo, demo video, disclose AI-tool use as the rules require.

Want me to write the actual `lib/normalize.js` starter code and the `scripts/brightdata.js` wrapper once your team has real JSON back from Step 2-3? That'll be much easier to get exactly right once we see what the real scraped output looks like.