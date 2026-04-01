# DND Character Creator — Deployment Guide

## Recommended: Cloudflare Pages (Free, Unlimited Bandwidth)

### Prerequisites
- A GitHub account (https://github.com)
- A Cloudflare account (https://dash.cloudflare.com/sign-up — free)

### Step 1: Prepare the project structure

Your folder should look like this:

```
DND-Character/
├── race-selection.html          (rename to index.html for homepage)
├── dnd-landing-v5.html
├── Shimmersive_Epic_fantasy_... .png   (main BG)
├── 种族立绘/
│   ├── 高等精灵/  ...png
│   ├── 人类/      ...png
│   └── 矮人/      ...png
└── 主城概念图/
    ├── 高等精灵/  ...png
    ├── 人类/      ...png
    └── 矮人/      ...png
```

NOTE: Chinese folder names work fine — Cloudflare Pages serves them
with proper URL encoding automatically.

### Step 2: Push to GitHub

```bash
cd "path/to/DND Character"
git init
git add .
git commit -m "Initial commit: DND character creator with original art"
git remote add origin https://github.com/YOUR_USERNAME/dnd-character.git
git branch -M main
git push -u origin main
```

### Step 3: Connect to Cloudflare Pages

1. Go to https://dash.cloudflare.com → Workers & Pages → Create
2. Select "Pages" → "Connect to Git"
3. Authorize GitHub, select your repository
4. Build settings:
   - Framework preset: None
   - Build command: (leave empty)
   - Build output directory: /        (just a slash — serve the root)
5. Click "Save and Deploy"

Your site will be live at: https://dnd-character.pages.dev
(Cloudflare auto-assigns a subdomain, you can customize it)

### Step 4: Custom domain (optional)

In Cloudflare Pages dashboard → Custom domains → Add your domain.
Cloudflare handles SSL/HTTPS automatically.

---

## Alternative: GitHub Pages

```bash
# After pushing to GitHub:
# Go to repo Settings → Pages → Source: "Deploy from a branch"
# Select: main / root
# Site will be at: https://YOUR_USERNAME.github.io/dnd-character/
```

## Alternative: Vercel

```bash
npm i -g vercel
cd "path/to/DND Character"
vercel
# Follow prompts — done in 30 seconds
```

---

## Important Notes

- All image paths in the HTML are already relative — no code changes
  needed for deployment
- Images are served as-is (no compression) on all three platforms
- Cloudflare Pages automatically applies HTTP/2 and Brotli compression
  to TEXT files (HTML/CSS/JS) but leaves binary files (PNG/JPG) untouched
