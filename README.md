# notes.kiranbusi.com

Personal thought leadership site. Built with [Astro](https://astro.build), deployed to GitHub Pages at [notes.kiranbusi.com](https://notes.kiranbusi.com).

---

## 🚀 Go-live checklist

Two manual steps required — everything else is already done.

### 1. Enable GitHub Pages (2 minutes)

1. Go to **https://github.com/busilogic/notes.busilogic.com/settings/pages**
2. Under **Source**, select **GitHub Actions** (not "Deploy from a branch")
3. Save

The deploy workflow has already run — once Pages is enabled it will serve the build immediately.

### 2. Add DNS record (propagates in ~10–30 minutes)

At your domain registrar (wherever `kiranbusi.com` is registered):

| Type | Host | Value |
|------|------|-------|
| CNAME | `notes` | `busilogic.github.io` |

Once DNS propagates, go back to **Settings → Pages** and tick **Enforce HTTPS**.

### Verify

- [ ] https://notes.kiranbusi.com loads the site
- [ ] https redirects correctly (no certificate warning)
- [ ] The first post is visible at `/writing`

---

## Writing a new post

1. Create a file in `src/content/posts/` named `your-post-slug.md`
2. Add frontmatter at the top:

```markdown
---
title: "Your Post Title"
date: 2026-04-14
description: "One sentence that appears in the post list."
---

Your content here.
```

3. `git add`, `git commit`, `git push` to `master` — the site deploys automatically within ~90 seconds.

To mark a post as a draft (hidden from the list until ready):

```markdown
---
title: "Draft Post"
date: 2026-04-14
description: "..."
draft: true
---
```

---

## Pages

| Page | File | Notes |
|------|------|-------|
| Home | `src/pages/index.astro` | Worldview statement, links to Writing and System |
| Writing list | `src/pages/writing/index.astro` | Auto-populated from `src/content/posts/` |
| Individual post | `src/pages/writing/[...slug].astro` | Rendered from markdown |
| System | `src/pages/system.astro` | Agentic workspace overview — update manually |

---

## Local development

```bash
npm install
npm run dev        # http://localhost:4321
npm run build      # builds to dist/
npm run preview    # preview the build locally
```

---

## Deployment

Automatic. Push to `master` → GitHub Actions builds and deploys to GitHub Pages.

The workflow is at `.github/workflows/deploy.yml`. GitHub Pages must be configured to use **GitHub Actions** as the source (not a branch) in the repo settings.

**DNS**: A CNAME record at the domain registrar must point `notes.kiranbusi.com → busilogic.github.io`.

---

## Design

- **Stack**: Astro, static output, no JS framework
- **Fonts**: Lora (headings), Inter (body), JetBrains Mono (code) via Google Fonts
- **Colours**: `#FAFAF8` background, `#1A1A1A` ink, `#C17D11` amber accent
- **Styles**: `src/styles/global.css` — all custom properties defined there
- **Layout**: `src/layouts/Base.astro` — nav, footer, meta tags

---

## Archive

The original site (Org-mode HTML, 2015–2018) is preserved in `archive/`. It is not served by the new build.
