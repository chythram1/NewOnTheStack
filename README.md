# New on the Stacks

**A curated feed of the newest research in behavioral psychology, education, tech, AI and finance, with each paper summarized by what it found and why it matters.**

![Hosted on Cloudflare Pages](https://img.shields.io/badge/hosted%20on-Cloudflare%20Pages-F38020?logo=cloudflare&logoColor=white)
![No build step](https://img.shields.io/badge/build-none-2ea44f)
![Zero dependencies](https://img.shields.io/badge/dependencies-0-blue)
![Content updated daily](https://img.shields.io/badge/content-updated%20daily-8a4b9e)

[![Deploy to Cloudflare](https://deploy.workers.cloudflare.com/button)](https://deploy.workers.cloudflare.com/?url=https://github.com/chythram1/NewOnTheStack)

---

## Overview

New on the Stacks turns a stream of new papers into short, readable briefs. Every entry answers three questions:

1. **What did it find?** The headline result, with the key number.
2. **Why does it matter?** The practical takeaway, plus the main caveat.
3. **How strong is the evidence?** Study design and sample size.

Each field also gets a **trend brief**: a short pattern that ties together several of the latest papers, so you can see where a field is heading, not just what came out today.

| Field | Key |
| --- | --- |
| Behavioral psychology | `psych` |
| Education | `edu` |
| Technology | `tech` |
| Artificial intelligence | `ai` |
| Finance | `fin` |

## Features

- **Filter by field**, with a live count of papers in each.
- **Full-text search** across titles, authors and summaries.
- **Sort** by publication date or by when a paper was added.
- **Reading list**: save papers for later. It's stored in your browser, so no account is needed.
- **Light and dark themes** that follow your system setting.
- **Responsive layout** for phones, tablets and desktops.
- **Always current**: the content file is never cached, so each daily update shows up right away.

## Architecture

The site has no framework, no bundler and no backend. One HTML file renders everything from one JSON file.

```
 ┌──────────────┐   commit feed.json   ┌──────────────┐   auto-deploy   ┌──────────────────┐
 │  Daily job   │ ───────────────────▶ │ GitHub: main │ ──────────────▶ │ Cloudflare Pages │
 └──────────────┘                      └──────────────┘                 └────────┬─────────┘
                                                                                 │
                                                       index.html fetches feed.json on load
                                                                                 ▼
                                                                          ┌────────────┐
                                                                          │   Reader   │
                                                                          └────────────┘
```

| Layer | Choice |
| --- | --- |
| Markup, styles, logic | One `index.html` with plain HTML, CSS and JavaScript |
| Content | `feed.json`, loaded at runtime |
| Typography | Newsreader, IBM Plex Sans and IBM Plex Mono (Google Fonts) |
| Hosting | Cloudflare Pages, global CDN |
| Caching | `_headers` sets `Cache-Control: no-cache` on `feed.json` |
| CI/CD | Cloudflare Pages Git integration: every push to `main` redeploys |

## Project structure

```
.
├── index.html       # The entire site: layout, styles and rendering logic
├── feed.json        # All content: papers and trend briefs
├── _headers         # Header rules (no caching for feed.json)
├── wrangler.jsonc   # Config for the "Deploy to Cloudflare" button
├── .assetsignore    # Files the button's deploy won't serve
└── README.md
```

## Running locally

The page loads `feed.json` with `fetch`, so serve the folder over HTTP rather than opening the file directly:

```bash
git clone https://github.com/chythram1/NewOnTheStack.git
cd NewOnTheStack
python3 -m http.server 8000
```

Then open <http://localhost:8000>.

## Deployment

Hosted on **Cloudflare Pages** through its GitHub integration:

| Setting | Value |
| --- | --- |
| Framework preset | None |
| Build command | *(empty)* |
| Build output directory | `/` |
| Production branch | `main` |

Every push to `main` triggers a new production deployment. No API tokens or build tooling are involved.

### Deploy your own copy

[![Deploy to Cloudflare](https://deploy.workers.cloudflare.com/button)](https://deploy.workers.cloudflare.com/?url=https://github.com/chythram1/NewOnTheStack)

The button copies this repo into your GitHub account and deploys it to your Cloudflare account as a static site. `wrangler.jsonc` holds the config it uses, and `.assetsignore` keeps repo-only files from being served. Both files are ignored by the Cloudflare Pages setup above.

## Updating the content

Only `feed.json` changes. A daily job replaces it and commits to `main`, which redeploys the site. Keep about 5 papers per field.

### `feed.json` schema

```jsonc
{
  "updatedAt": "2026-10-20T16:30:00Z",          // ISO time of the last refresh, shown as "Last pulled"
  "trends": {                                   // one entry per field: psych, edu, tech, ai, fin
    "ai": { "headline": "Short pattern name", "points": ["2-3 sentences, each tied to a paper result"] }
  },
  "papers": [
    {
      "id": "arxiv.org_abs_2610.24052",          // URL without https://, non [A-Za-z0-9_-.] chars → "_"
      "field": "ai",                             // psych | edu | tech | ai | fin
      "kind": "Preprint",                        // Preprint | Journal article | Working paper
      "date": "2026-10-20", "dateLabel": "Oct 2026",   // dateLabel optional
      "title": "…", "authors": "First three, comma-separated", "venue": "arXiv cs.CR",
      "url": "https://arxiv.org/abs/2610.24052", "ref": "arXiv:2610.24052",  // ref optional
      "summary": "Headline finding in 1-2 sentences, with the key number.",
      "findings": ["2-3 concrete results with numbers as reported"],
      "takeaway": "Why it matters, plus the main caveat.",
      "evidence": "Study design · sample",
      "addedAt": "2026-10-20T10:56:28Z"
    }
  ]
}
```

### Content guidelines

- **Summaries** lead with the result and include the key number.
- **Findings** quote numbers as the paper reports them.
- **Takeaways** always name the main limitation.
- **Trend points** each tie back to a specific paper in the feed.
- **IDs** must stay stable across updates, since saved reading lists depend on them.
