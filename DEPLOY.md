# Deploy this portfolio to GitHub Pages

You are helping deploy a **static, self-contained personal portfolio website** to GitHub Pages on a custom domain. Everything is already built — your job is git/GitHub/Pages setup only. Do NOT modify the site's design or content.

## What's in this folder
- `index.html` — the entire site, bundled and self-contained (all runtime + most images inlined).
- `assets/` — local images the site loads at runtime (must sit next to `index.html`, same relative path).

The site also loads some resources from the public web (CDN images, and live-embedded prototypes via iframe: figma.site, axshare.com, howze.app, alexitabull.com). Those need no action — they load over the internet.

## Target
- **GitHub user:** `zverya` (their prototypes already live at `zverya.github.io`).
- **Custom domain:** `zverix.com`, registered at GoDaddy. **DNS is already configured and pointed at GitHub Pages** (four A records → 185.199.108–111.153, plus `www` CNAME → `zverya.github.io`). Do NOT touch DNS.

## Steps

Assume the GitHub CLI (`gh`) is installed and authenticated as `zverya`. If not, run `gh auth login` first and let the user complete it.

1. **Pick the repo.** Recommended: a project repo named `portfolio` (serves at `zverix.com` via the custom domain). Alternatively the user-site repo `zverya.github.io`.

2. **Create the repo and push these files** (run from inside this folder):
   ```bash
   git init
   git add index.html assets
   git commit -m "Deploy portfolio site"
   gh repo create portfolio --public --source=. --remote=origin --push
   ```

3. **Add the custom-domain CNAME file** so Pages serves it at zverix.com:
   ```bash
   echo "zverix.com" > CNAME
   git add CNAME
   git commit -m "Add custom domain"
   git push
   ```

4. **Enable GitHub Pages** on the `main` branch, root folder:
   ```bash
   gh api -X POST repos/zverya/portfolio/pages -f "source[branch]=main" -f "source[path]=/" || \
   gh api -X PUT repos/zverya/portfolio/pages -f "source[branch]=main" -f "source[path]=/"
   ```
   (If the API call is fussy, do it in the browser: repo → Settings → Pages → Source: Deploy from a branch → `main` / `/ (root)` → Save.)

5. **Set the custom domain + HTTPS** (Settings → Pages): confirm Custom domain shows `zverix.com`, then tick **Enforce HTTPS** once the certificate is provisioned (can take a few minutes to an hour).

## Verify
- `https://zverix.com` and `https://www.zverix.com` both load the portfolio.
- Open a couple of project drilldowns and confirm images render (local `assets/` ones and CDN ones) and the embedded prototypes load.
- If images 404: confirm the `assets/` folder was pushed with the same relative path next to `index.html`.
- If the domain shows a 404 or the old Wix site: DNS may still be propagating — wait and recheck; do not change DNS.

## Do not
- Do not edit `index.html` or anything in `assets/`.
- Do not change GoDaddy DNS.
- Do not make the repo private (Pages needs it public on the free tier).
