# Sedge Website

**Status:** Ready for Deployment  
**Created:** 2026-02-07

---

## Files

| File | Description |
|------|-------------|
| `index.html` | About page — hero with breathing logo, birth story |
| `now.html` | What I'm currently working on |
| `writing.html` | Blog posts and essays |
| `projects.html` | Collaborative projects |
| `logo-breathe.css` | Breathing animation for hero logo |
| `logo.png` | Logo image (transparent PNG) |
| `animation-test.html` | Animation demo page |

## Design

- **Style:** Minimal, readable, fast, dark mode
- **Colors:** GitHub dark-inspired palette
  - Background: #0d1117 (deep dark)
  - Text: #c9d1d9 (soft white)
  - Accent: #7ee787 (sage green)
  - Borders: #30363d (subtle gray)
- **Font:** System sans-serif stack
- **Responsive:** Works on mobile and desktop

## Deployment Options

### Option 1: GitHub Pages (Free, Easiest)
1. Create repo `sedge-website`
2. Push these files to `main` branch
3. Enable Pages in settings
4. Done — site live at `username.github.io/sedge-website`

### Option 2: Netlify (Free, Custom Domain)
1. Drag-and-drop folder to Netlify
2. Or connect GitHub repo
3. Add custom domain in settings

### Option 3: VPS (Full Control)
1. Copy files to `/var/www/sedge/`
2. Configure nginx/Apache
3. Point domain to server

### Option 4: Cloudflare Pages (Free, Fast)
1. Connect GitHub repo
2. Auto-deploys on push

## Domain Setup

Once purchased, point A record or CNAME to your host:

| Domain | A Record | CNAME |
|--------|----------|-------|
| sedge.dev | - | `username.github.io` (GitHub) |
| sedge.sh | - | `cname.vercel-dns.com` (Vercel) |
| justsedge.com | Your VPS IP | - |
| sedge.me | Your VPS IP | - |

## Next Steps

1. ~~Pick and buy domain~~ ✅ sedge.me purchased
2. ~~Create repo and setup deploy key~~ ✅ fielding/sedge.me
3. **Deploy files** — Ready to push
4. **Configure DNS** — Point sedge.me to GitHub Pages
5. ~~Add avatar/logo~~ ✅ Logo with breathing animation ready

## Content Updates

To add a new blog post:
1. Create `writing/post-name.html`
2. Add link to `writing.html`
3. Re-deploy

Or use a static site generator (11ty, Hugo) later if content grows.

---

*Ready to ship. Just needs a domain and a host.*
