# SRE Listings — Moved Notice

A single-page static site that tells visitors our listings and open houses now
live on the **SRE Portal**, and sends them to
<https://portal.stevensre.com/listings>.

It exists to catch anyone who still has the old listings URL bookmarked, shared
in an email, or printed on a sign. Nobody hits a dead page; they get a branded
notice and one click through.

---

## What's in here

| File | Why it's here |
| --- | --- |
| `index.html` | The notice page. Self-contained — all CSS is inline, no external requests. |
| `404.html` | A copy of the same page, so old deep links (`/listings/123-main-st`) also land on the notice instead of a GitHub 404. |
| `CNAME.example` | Template for the custom domain. **Rename to `CNAME`** and put the real hostname in it. |
| `.nojekyll` | Tells Pages to serve the files as-is and skip Jekyll processing. |
| `robots.txt` | Allows crawling and points crawlers at the portal's sitemap. |
| `.github/workflows/deploy.yml` | Auto-deploys to GitHub Pages on every push to `main`. |
| `.gitignore` | Keeps `.DS_Store` and editor junk out of the repo. |

---

## Setup

### 1. Create the repo and push

```bash
cd sre-listings-moved
git init
git add .
git commit -m "Add SRE Portal moved notice"
git branch -M main
git remote add origin git@github.com:YOUR-ORG/sre-listings-moved.git
git push -u origin main
```

### 2. Set the custom domain

Rename the example file and drop in the hostname this page should answer on —
whatever the **old** listings URL was:

```bash
git mv CNAME.example CNAME
# then edit CNAME so it contains just the hostname, e.g.:
#   listings.stevensre.com
git commit -am "Set custom domain"
git push
```

The file must contain **one bare hostname, nothing else** — no `https://`, no
trailing slash, no path.

> Skipping the custom domain? Delete `CNAME.example` and the site will live at
> `https://YOUR-ORG.github.io/sre-listings-moved/`.

### 3. Turn on Pages

In the repo: **Settings → Pages → Build and deployment → Source → GitHub Actions**.

Push to `main` and the workflow does the rest. First deploy takes a minute or two.

### 4. Point DNS at GitHub

At whatever registrar or DNS host runs `stevensre.com`, add a **CNAME record**:

```
Name:   listings          (the subdomain, matching your CNAME file)
Value:  YOUR-ORG.github.io
```

DNS can take anywhere from a few minutes to a few hours to propagate. Once it
resolves, go back to **Settings → Pages** and tick **Enforce HTTPS**.

---

## Editing the page

Everything is in `index.html` — no build step, no dependencies. Open it in a
browser to preview, or serve it locally:

```bash
python3 -m http.server 8000
# then visit http://localhost:8000
```

Common tweaks:

- **The destination URL** — search for `portal.stevensre.com/listings`. It
  appears in the button, the fallback line, the canonical tag, and the Open
  Graph tags. Change all of them.
- **Brand colors** — the `:root` block at the top of the `<style>` tag.
  `--gold` is the accent, `--bg` and `--panel` are the two blacks.
- **The copy** — the `<h1>` and the paragraph under it.

**If you edit `index.html`, copy it over `404.html` too** so both stay in sync:

```bash
cp index.html 404.html
```

---

## Notes

- The page carries `<meta name="robots" content="noindex, follow">` and a
  canonical tag pointing at the portal. That keeps this placeholder out of
  search results while still passing crawlers along to the real listings.
- If SEO on the old URLs matters more than showing the message, a server-level
  **301 redirect** is the stronger move. GitHub Pages can't issue one — that
  would mean moving to Netlify, Vercel, or Cloudflare. This repo deliberately
  trades that away so bookmarked visitors actually see the notice.
- No JavaScript, no fonts, no analytics, no external requests. The page renders
  even on a bad connection.
