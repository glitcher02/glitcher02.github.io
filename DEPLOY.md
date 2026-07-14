# Deploying archiebutler.co.uk (GitHub Pages)

This is a plain static site — no build step. Everything in this folder gets
served exactly as-is. You can deploy entirely from the GitHub website (no
command line needed).

## What's in here
```
index.html              ← the landing page
CNAME                    ← tells GitHub the custom domain (do not delete)
assets/css/site.css      ← styling for the landing page
demos/itinerary/         ← demo 1: luxury itinerary viewer
demos/builder/           ← demo 2: quote & margin builder
demos/ai-brief/          ← demo 3: AI proposal drafter
```

## Preview locally
A tiny server is handy because the pages use root-absolute paths (e.g.
`/assets/...`). From this folder:
```
python3 -m http.server 8787
```
then open http://localhost:8787

## Publish to GitHub Pages (web UI)
1. On github.com, create a **new public repository** — call it e.g. `website`
   (it does **not** need to be named after the domain).
2. **Add file → Upload files**, then drag in the *contents* of this folder
   (so `index.html` sits at the repo root, alongside `CNAME`, `assets/`,
   `demos/`). Commit.
3. **Settings → Pages**:
   - Source: *Deploy from a branch* → `main` → `/ (root)` → Save.
   - Custom domain: `archiebutler.co.uk` (the `CNAME` file already sets this).
   - Tick **Enforce HTTPS** once it's available (can take a little while).

## DNS (the apex domain bit)
`cv.` works today because it's a *subdomain* (a single CNAME record). The apex
`archiebutler.co.uk` needs **A / AAAA records** instead. Wherever your DNS lives
(registrar or Cloudflare), add:

**A records** (host `@`) → GitHub Pages IPs:
```
185.199.108.153
185.199.109.153
185.199.110.153
185.199.111.153
```
**AAAA records** (host `@`, optional but recommended):
```
2606:50c0:8000::153
2606:50c0:8001::153
2606:50c0:8002::153
2606:50c0:8003::153
```
Optionally add a `www` **CNAME** → `<your-github-username>.github.io` so
`www.archiebutler.co.uk` redirects too.

> If your DNS is on **Cloudflare**, set those records to **DNS only** (grey
> cloud), not proxied, while GitHub issues the HTTPS certificate. You can turn
> the proxy back on afterwards if you want.

DNS changes can take anywhere from minutes to a few hours to propagate. Once
GitHub shows the domain as verified and HTTPS is enforced, you're live.

## Before you go live — quick checklist
- [ ] Confirm the contact email in `index.html` (currently
      `hello@archiebutler.co.uk`) is a mailbox you actually receive.
- [ ] (Optional) Swap the gradient "film-still" panels in the itinerary demo
      for real licensed photography.
