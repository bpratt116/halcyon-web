# Hosting halcyon.app — Step-by-Step

This folder contain the full set of pages App Review (and Apple/AdMob) need before Halcyon can ship:

- `index.html` — landing page at `halcyon.app/`
- `privacy.html` → served at `halcyon.app/privacy`
- `terms.html` → served at `halcyon.app/terms`
- `support.html` → served at `halcyon.app/support`
- `app-ads.txt` → served at `halcyon.app/app-ads.txt` (AdMob inventory verification)
- `CNAME` → tells GitHub Pages which domain to map

The recommended host is **GitHub Pages** — free, supports custom domains, and the entire workflow is two commits + a DNS change.

---

## Option A — GitHub Pages (recommended)

### Step 1: Create a GitHub repo

1. Go to https://github.com/new
2. Name: `halcyon-web` (or anything; Brendan's call).
3. Set to **Public** (Pages requires Pro for private repos).
4. Do NOT initialize with a README (we have files to push).
5. Click **Create repository**.

### Step 2: Push these files

Open Terminal and run:

```bash
cd ~/Documents/halcyon-web
git init
git add .
git commit -m "Initial halcyon.app site"
git branch -M main
git remote add origin https://github.com/<your-username>/halcyon-web.git
git push -u origin main
```

Replace `<your-username>` with your GitHub username.

### Step 3: Enable Pages

1. Open the repo on github.com.
2. Click **Settings** → **Pages** (left sidebar).
3. Under **Source**, choose **Deploy from a branch**.
4. Branch: `main`, folder: `/ (root)`. Click **Save**.
5. Wait ~30 seconds. The page show a green check and a temporary URL like `https://<your-username>.github.io/halcyon-web/`.

### Step 4: Point halcyon.app at GitHub

GitHub Pages need DNS records on your domain registrar (where you bought halcyon.app — Namecheap, GoDaddy, Cloudflare, etc.).

Add these records at your registrar:

| Type | Name | Value |
|------|------|-------|
| `A` | `@` | `185.199.108.153` |
| `A` | `@` | `185.199.109.153` |
| `A` | `@` | `185.199.110.153` |
| `A` | `@` | `185.199.111.153` |
| `AAAA` | `@` | `2606:50c0:8000::153` |
| `AAAA` | `@` | `2606:50c0:8001::153` |
| `AAAA` | `@` | `2606:50c0:8002::153` |
| `AAAA` | `@` | `2606:50c0:8003::153` |
| `CNAME` | `www` | `<your-username>.github.io` |

DNS propagation take 5 minutes to a few hours.

### Step 5: Set custom domain in GitHub

1. Repo → Settings → Pages.
2. Under **Custom domain**, enter `halcyon.app`. Click **Save**.
3. GitHub verify DNS automatically. Once verified, tick **Enforce HTTPS**.

### Step 6: Verify

```bash
curl -I https://halcyon.app/
curl -I https://halcyon.app/privacy
curl -I https://halcyon.app/terms
curl -I https://halcyon.app/support
curl -I https://halcyon.app/app-ads.txt
```

All five should return `HTTP/2 200`. If any 404, the file path is off — check filename casing matches exactly.

---

## Option B — Cloudflare Pages

If you're already on Cloudflare:

1. Log into Cloudflare Dashboard → **Pages** → **Create a project**.
2. Connect to your GitHub `halcyon-web` repo.
3. Build settings: **None** (these are static files).
4. Output directory: `/`.
5. Once deployed, Cloudflare give you a `*.pages.dev` URL.
6. Pages → Custom domains → add `halcyon.app`. Cloudflare auto-configure DNS if halcyon.app is on Cloudflare.

---

## Option C — Existing host (Squarespace, Wix, Webflow)

If halcyon.app already point at a site builder, just paste the body content of each `.html` file into pages with the matching slug:
- Page slug `privacy` → paste contents of `privacy.html`
- Page slug `terms` → paste contents of `terms.html`
- Page slug `support` → paste contents of `support.html`

For `app-ads.txt`, most builders let you upload a custom file at the root URL. If yours doesn't, you must move hosting to one of A or B; AdMob require this exact path.

---

## Update workflow

When the policy or terms change:

```bash
cd ~/Documents/halcyon-web
# edit privacy.html (or whichever file)
git add .
git commit -m "Update privacy policy"
git push
```

GitHub Pages redeploy in ~30 seconds.

---

## What you may want to customize before pushing

- **`mailto:bpratt116@gmail.com`** appears in privacy.html, terms.html, and support.html. If you want a `support@halcyon.app` address instead, set up email forwarding on your domain registrar and search/replace the email everywhere.
- **Governing-law jurisdiction** in `terms.html` § 9 currently say Texas. Change if you live elsewhere.
- **`app-ads.txt`** — current value `pub-3855102906014982` come from `AdConfiguration.swift`. Don't change unless your AdMob publisher ID change.
- **Subscription price** in `terms.html` § 2 currently say `$6.99 USD per year`. Update if pricing change.
