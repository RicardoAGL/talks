# Host Your Own Talks Site on GitHub Pages

A step-by-step guide to set up a public URL where your conference and workshop slides live permanently — shareable from any venue, on any machine.

**End result:** `https://<your-github-username>.github.io/talks/`

---

## What you need

- A GitHub account
- Node.js 18+ and `pnpm` (or `npm`)
- A Slidev deck (see [sli.dev](https://sli.dev) if you don't have one yet)
- ~15 minutes

---

## Step 1 — Create the public repo

1. Go to github.com → **New repository**
2. Name it `talks`
3. Set visibility to **Public** (required for free GitHub Pages)
4. Do **not** initialise with a README — you'll push files yourself

---

## Step 2 — Enable GitHub Pages

In your new repo: **Settings → Pages → Source → Deploy from branch**

- Branch: `main`
- Folder: `/ (root)`
- Click **Save**

Your site will be live at `https://<username>.github.io/talks/` within a minute of your first push.

---

## Step 3 — Clone this repo as your starting point

```bash
git clone https://github.com/<your-username>/talks.git
cd talks
```

Copy the three files that power the landing page from [ricardoagl.github.io/talks](https://ricardoagl.github.io/talks):

| File | Purpose |
|---|---|
| `index.html` | Landing page — renders cards from `_meta.json` |
| `_meta.json` | Registry of all your talks (edit this per talk) |
| `404.html` | SPA fallback — catches hard-refreshes on slide URLs |

You can grab them directly from [github.com/RicardoAGL/talks](https://github.com/RicardoAGL/talks). Replace the LinkedIn/GitHub links in `index.html` with your own.

---

## Step 4 — Build your Slidev deck

In your Slidev project, build with the `--base` flag pointing to your talk's subdirectory:

```bash
# Replace <your-username> and <talk-slug> with your own values
npx slidev build slides.md --base /talks/<talk-slug>/
```

> **Why `--base`?** GitHub Pages serves your deck from `/talks/<talk-slug>/`, not from `/`. Without the flag, asset paths resolve to the site root and 404.

The output lands in `dist/`.

---

## Step 5 — Copy the build into your talks repo

```bash
# From your Slidev project directory
cp -r dist/. /path/to/talks/<talk-slug>/
```

The talk slug must match what you used in `--base` (e.g. `writing-coach`, `dbt-intro`, `ai-agents`).

---

## Step 6 — Register the talk in `_meta.json`

Add an entry to `_meta.json`:

```json
[
  {
    "slug": "your-talk-slug",
    "title": "Your Talk Title",
    "subtitle": "Conference · Month Year",
    "description": "One sentence that describes what this talk is about.",
    "date": "2026-09-15",
    "forum": "CONFERENCE",
    "status": "live",
    "tags": ["ai", "data", "whatever-fits"]
  }
]
```

`status` controls how the landing page renders the card:
- `"live"` — clickable card, links to the deck
- `"archived"` — greyed-out card with "message me for a copy" CTA

---

## Step 7 — Push and verify

```bash
git add .
git commit -m "feat: add <talk-slug> talk"
git push
```

GitHub Pages deploys automatically. After ~60 seconds visit:
- **Landing page:** `https://<username>.github.io/talks/`
- **Your deck:** `https://<username>.github.io/talks/<talk-slug>/`

Navigate slides with arrow keys or the on-screen controls — all client-side, no server requests.

---

## Adding more talks later

Repeat Steps 4–7 for each new talk. The landing page reads `_meta.json` on load, so adding an entry there is enough to make a new card appear.

---

## Archiving a talk

When you want to retire a deck from public access:

1. Delete the talk's folder from the repo (`git rm -r <talk-slug>/`)
2. Flip `"status": "archived"` in `_meta.json`
3. Push — the card stays visible on the landing page with a "message me for a copy" note, but the link is gone

You keep the history in git, so you can always restore or share a specific commit.

---

## Tips

**No Slidev?** Any static site generator works — the pattern is the same: build with the correct base path, drop the output folder into the repo, register in `_meta.json`.

**Custom domain:** Add a `CNAME` file at the repo root with your domain, then configure DNS. GitHub Pages handles the TLS cert automatically.

**iframes / live demos:** Avoid embedding `localhost:*` iframes — they won't work for audiences. Export your deck to PPTX or PDF first, extract the screenshot from the exported image slide, and embed that as a static `<img>` instead.

**Large repos:** The built Slidev assets for a single deck are typically 2–5 MB. For dozens of talks, consider GitHub LFS or simply deleting the folder when archiving.
