# Live Up Collective — Implementation Plan
> Prompt this document to Claude Code to scaffold the full project.

---

## Project Overview

Build a static website for **The Live Up Collective**, a non-profit focused on adolescent mental health. The site is a single-page scrolling landing page built with Hugo + Decap CMS, hosted on GitHub Pages, with automated builds via GitHub Actions.

**Goals:**
- Match the Canva mockup as closely as possible (see Design System below)
- Zero hosting cost beyond domain registration
- Non-developer maintainable: all content editable via Decap CMS at `/admin`
- Domain: `liveupcollective.org`

---

## Tech Stack

| Layer | Choice | Reason |
|---|---|---|
| Static site generator | Hugo | Fast, GitHub Pages native, no runtime deps |
| CMS | Decap CMS | Git-backed, form UI for non-devs, free |
| Hosting | GitHub Pages | Free, HTTPS included |
| CI/CD | GitHub Actions | Free tier, Hugo build in ~20s |
| Donation platform | Authorize.net | TBD — wire up later, use placeholder link for now |
| Signup Forms | Jotform.com | TBD — wire up later, use placeholder link for now |

---

## Repository Structure

```
liveup-collective/
├── hugo.toml                        # Site config
├── content/
│   └── _index.md                    # All editable page content (front matter)
├── layouts/
│   ├── _default/
│   │   └── baseof.html              # Base HTML shell
│   └── index.html                   # Single-page layout template
├── layouts/partials/
│   ├── head.html                    # <head>, fonts, meta
│   ├── nav.html                     # Sticky top nav
│   ├── hero.html                    # Section 1
│   ├── who-we-live-up-for.html      # Section 2
│   ├── the-problem.html             # Section 3
│   ├── our-mission.html             # Section 4
│   ├── your-impact.html             # Section 5
│   ├── what-can-you-do.html         # Section 6
│   └── footer.html                  # Footer
├── static/
│   ├── admin/
│   │   ├── index.html               # Decap CMS entry point
│   │   └── config.yml               # Decap field definitions
│   ├── css/
│   │   └── main.css                 # All styles
│   ├── js/
│   │   └── main.js                  # Smooth scroll, nav behavior
│   └── images/
│       └── placeholder/             # Placeholder images (teens, community)
└── .github/
    └── workflows/
        └── hugo.yml                 # Build & deploy on push to main
```

---

## Design System

Implement these exactly. Do not use a Hugo theme — write the CSS from scratch to match the mockup.

### Colors
```css
:root {
  --green-dark:   #1C3829;   /* Hero bg, Problem section bg, Footer bg */
  --green-mid:    #2D5440;   /* Hover states, accents */
  --cream:        #F5F0E8;   /* Main body background */
  --cream-dark:   #EDE7D9;   /* Alternate section bg (subtle) */
  --gold:         #C8922A;   /* CTA buttons, accent highlights */
  --gold-light:   #E5A93A;   /* Button hover */
  --white:        #FFFFFF;
  --text-dark:    #1A1A1A;
  --text-muted:   #4A4A4A;
  --text-on-green:#FFFFFF;
}
```

### Typography
Use Google Fonts — add to `<head>`:
- **Display/Logo:** `Playfair Display` (700, 900) — used for "The Live Up Collective" and section headings like "The Problem", "Our Mission"
- **Accent uppercase:** `Oswald` (700) — used for the "BECAUSE THE MENTAL HEALTH..." stamp line and "LIVE UP" inline emphasis
- **Body:** `Lato` (400, 600) — all body copy, descriptions, bullets

```css
/* Scale */
--font-display: 'Playfair Display', Georgia, serif;
--font-accent:  'Oswald', Impact, sans-serif;
--font-body:    'Lato', Helvetica, sans-serif;

--text-hero:    clamp(2.8rem, 6vw, 5rem);
--text-h2:      clamp(2rem, 4vw, 3rem);
--text-h3:      1.25rem;
--text-body:    1rem;
--text-small:   0.875rem;
```

### Spacing
```css
--section-pad:  clamp(4rem, 8vw, 7rem) clamp(1.5rem, 5vw, 6rem);
--max-width:    1100px;
```

### Logo Treatment
The logo wordmark "The Live Up Collective" uses Playfair Display bold. The arrow motif on the "I" in "Live Up" should be implemented as a CSS pseudo-element `↑` or a small inline SVG — a subtle upward arrow overlaid on or replacing the dot/stem of the letter I. Keep it simple.

---

## Page Sections

Implement each section as a Hugo partial. The layout template (`layouts/index.html`) calls each partial in order.

### 1. Nav (sticky)
- Logo left: "The Live Up Collective" in Playfair Display, white on dark green bar
- Right: "DONATE NOW" button in gold
- Becomes visible / changes style on scroll (JS)
- Smooth scroll anchors to each section

### 2. Hero
- **Background:** `--green-dark`
- **Logo:** Large "The Live Up Collective" wordmark, white, Playfair Display 900, top-left area
- **Tagline:** Below logo — "Enduring hard things to raise support for those who don't think they can."
- **CTA:** "DONATE NOW" button, gold, top-right
- **Editable fields:** `hero_tagline`, `hero_donate_url`

### 3. Who We Live Up For
- **Background:** `--cream`
- **Heading:** "Who we LIVE UP for" — "LIVE UP" rendered in `--font-accent` in green, rest in Playfair Display
- **Subheading:** "Our mission is laser-focused on teens and adolescents within our local communities. Why?"
- **Stamp line:** "BECAUSE THE MENTAL HEALTH OF OUR YOUTH IS THE HEARTBEAT OF OUR FUTURE" — full width, Oswald uppercase, green, large, centered
- **Three-column middle row:** [photo left] | [center text: "the overwhelmed / the underserved / the unseen" stacked in Playfair Display italic] | [photo right]
- **Closing quote:** Centered, italic body text
- **Editable fields:** `who_subheading`, `who_stamp_text`, `who_tags` (list: overwhelmed/underserved/unseen), `who_closing_quote`

### 4. The Problem
- **Background:** `--green-dark`
- **Heading:** "The Problem" in Playfair Display, white, centered
- **Three-column layout:**
  - Col 1: placeholder image + caption "Today's adolescents are facing overwhelming mental health challenges. Rates of anxiety, depression, and isolation are rising."
  - Col 2: stat callout — "1 in 5 adolescents experiences a mental health challenge each year." — styled as a prominent card/badge
  - Col 3: placeholder image + caption "Behind every statistic is a real person—a teenager navigating anxiety, depression, loneliness, or trauma without the support they deserve."
- All text white on dark green
- **Editable fields:** `problem_col1_text`, `problem_stat`, `problem_stat_label`, `problem_col3_text`

### 5. Our Mission
- **Background:** `--cream`
- **Heading:** "Our Mission" in Playfair Display, centered
- **Intro paragraph:** "We are committed to equipping, empowering, and encouraging young people to rise above mental health challenges and step into hope, strength, and purpose."
- **Subheading:** "What we stand for:"
- **Three pillars** (rendered as cards or styled list items):
  - **Equipping** — "We provide young people with the tools, resources, and support they need to navigate life's challenges."
  - **Empowering** — "We help adolescents believe in their strength, their voice, and their ability to overcome."
  - **Encouraging** — "We walk alongside youth with compassion, reminding them they are not alone and their story matters."
- Green left-border accent on each pillar label
- **Editable fields:** `mission_intro`, `mission_pillars` (list of: title, description)

### 6. Your Impact
- **Background:** `--cream` (or subtle `--cream-dark`)
- **Heading:** "Your Impact" — very large, Playfair Display, left-aligned, display size
- **Intro:** "Your generosity and support directly funds real, life-changing support for adolescents who might otherwise go without help."
- **Three impact areas** (styled as a breakdown list with green accent headers):
  - **Mental Health Access** — "Providing counseling and mental health services for youth who cannot afford care."
  - **School Support** — "Funding Social Workers and Guidance Counselors in underfunded schools so students have someone to turn to."
  - **Youth Outreach Programs** — "Supporting mental health education and staffing in youth organizations, and creating safe and supportive environments."
- **Closing line:** "This isn't just funding programs—it's creating access, support, and hope where it's needed most."
- **Editable fields:** `impact_intro`, `impact_items` (list of: title, description), `impact_closing`

### 7. What Can You Do
- **Background:** `--cream`
- **Heading:** "What can you do" in Playfair Display
- **Intro:** "There are young people right now who don't think they can make it through."
- **Pick your project block:** Centered, italic — "suffer for something greater than yourself and choose your challenge to raise money for adolescent mental health."
- **Examples line:** "run your first marathon, do an ironman, 7 mile swim, writing a book, etc"
- **Note:** "fitness challenges have coaching provided by live up staff!"
- **Three CTA items** (styled with gold arrow/emoji):
  - Donate Now → links to donate URL
  - Be Part of the Mission → links to (TBD)
  - Sponsor someone else → links to (TBD)
- **Editable fields:** `cta_intro`, `cta_project_description`, `cta_examples`, `cta_note`, `cta_items` (list of: label, url)

### 8. Footer
- **Background:** `--green-dark`
- **Center block:** "The Live Up Collective is committed to helping young people rise above challenges and step into hope, strength, and purpose."
- **Tagline below:** "Pushing through hard things to empower those who don't think they can."
- White text throughout
- **Editable fields:** `footer_statement`, `footer_tagline`

---

## Content File: `content/_index.md`

All editable content lives here as YAML front matter. Hugo templates read from `.Params`. Example structure:

```yaml
---
hero_tagline: "Enduring hard things to raise support for those who don't think they can."
hero_donate_url: "https://donate.liveupcollective.org"

who_subheading: "Our mission is laser-focused on teens and adolescents within our local communities. Why?"
who_stamp_text: "Because the mental health of our youth is the heartbeat of our future"
who_tags:
  - the overwhelmed
  - the underserved
  - the unseen
who_closing_quote: "We've seen firsthand what happens when young people don't have access to mental health support—and what happens when they do."

problem_col1_text: "Today's adolescents are facing overwhelming mental health challenges. Rates of anxiety, depression, and isolation are rising."
problem_stat: "1 in 5"
problem_stat_label: "adolescents experiences a mental health challenge each year."
problem_col3_text: "Behind every statistic is a real person—a teenager navigating anxiety, depression, loneliness, or trauma without the support they deserve."

mission_intro: "We are committed to equipping, empowering, and encouraging young people to rise above mental health challenges and step into hope, strength, and purpose."
mission_pillars:
  - title: Equipping
    description: "We provide young people with the tools, resources, and support they need to navigate life's challenges."
  - title: Empowering
    description: "We help adolescents believe in their strength, their voice, and their ability to overcome."
  - title: Encouraging
    description: "We walk alongside youth with compassion, reminding them they are not alone and their story matters."

impact_intro: "Your generosity and support directly funds real, life-changing support for adolescents who might otherwise go without help."
impact_items:
  - title: Mental Health Access
    description: "Providing counseling and mental health services for youth who cannot afford care."
  - title: School Support
    description: "Funding Social Workers and Guidance Counselors in underfunded schools so students have someone to turn to."
  - title: Youth Outreach Programs
    description: "Supporting mental health education and staffing in youth organizations, and creating safe and supportive environments."
impact_closing: "This isn't just funding programs—it's creating access, support, and hope where it's needed most."

cta_intro: "There are young people right now who don't think they can make it through."
cta_project_description: "Suffer for something greater than yourself and choose your challenge to raise money for adolescent mental health."
cta_examples: "Run your first marathon, do an ironman, 7 mile swim, writing a book, etc."
cta_note: "Fitness challenges have coaching provided by Live Up staff!"
cta_items:
  - label: Donate Now
    url: "https://donate.liveupcollective.org"
  - label: Be Part of the Mission
    url: "#"
  - label: Sponsor someone else
    url: "#"

footer_statement: "The Live Up Collective is committed to helping young people rise above challenges and step into hope, strength, and purpose."
footer_tagline: "Pushing through hard things to empower those who don't think they can."
---
```

---

## Decap CMS Config: `static/admin/config.yml`

```yaml
backend:
  name: github
  repo: YOUR_GITHUB_ORG/liveup-collective
  branch: main

media_folder: static/images/uploads
public_folder: /images/uploads

collections:
  - name: "pages"
    label: "Pages"
    files:
      - name: "home"
        label: "Home Page"
        file: "content/_index.md"
        fields:
          - { label: "Hero Tagline", name: "hero_tagline", widget: "string" }
          - { label: "Donate Button URL", name: "hero_donate_url", widget: "string" }

          - { label: "Who We Are — Subheading", name: "who_subheading", widget: "string" }
          - { label: "Who We Are — Stamp Text", name: "who_stamp_text", widget: "string" }
          - label: "Who We Are — Tags"
            name: "who_tags"
            widget: "list"
            field: { label: "Tag", name: "tag", widget: "string" }
          - { label: "Who We Are — Closing Quote", name: "who_closing_quote", widget: "text" }

          - { label: "Problem — Column 1 Text", name: "problem_col1_text", widget: "text" }
          - { label: "Problem — Stat Number", name: "problem_stat", widget: "string" }
          - { label: "Problem — Stat Label", name: "problem_stat_label", widget: "string" }
          - { label: "Problem — Column 3 Text", name: "problem_col3_text", widget: "text" }

          - { label: "Mission — Intro", name: "mission_intro", widget: "text" }
          - label: "Mission — Pillars"
            name: "mission_pillars"
            widget: "list"
            fields:
              - { label: "Title", name: "title", widget: "string" }
              - { label: "Description", name: "description", widget: "text" }

          - { label: "Impact — Intro", name: "impact_intro", widget: "text" }
          - label: "Impact — Items"
            name: "impact_items"
            widget: "list"
            fields:
              - { label: "Title", name: "title", widget: "string" }
              - { label: "Description", name: "description", widget: "text" }
          - { label: "Impact — Closing Line", name: "impact_closing", widget: "text" }

          - { label: "CTA — Intro", name: "cta_intro", widget: "text" }
          - { label: "CTA — Project Description", name: "cta_project_description", widget: "text" }
          - { label: "CTA — Examples", name: "cta_examples", widget: "string" }
          - { label: "CTA — Note", name: "cta_note", widget: "string" }
          - label: "CTA — Action Items"
            name: "cta_items"
            widget: "list"
            fields:
              - { label: "Label", name: "label", widget: "string" }
              - { label: "URL", name: "url", widget: "string" }

          - { label: "Footer — Statement", name: "footer_statement", widget: "text" }
          - { label: "Footer — Tagline", name: "footer_tagline", widget: "string" }
```

---

## Decap CMS Entry Point: `static/admin/index.html`

```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Content Manager — Live Up Collective</title>
  </head>
  <body>
    <script src="https://unpkg.com/decap-cms@^3.0.0/dist/decap-cms.js"></script>
  </body>
</html>
```

---

## GitHub Actions Workflow: `.github/workflows/hugo.yml`

```yaml
name: Deploy Hugo site to GitHub Pages

on:
  push:
    branches: [main]
  workflow_dispatch:

permissions:
  contents: read
  pages: write
  id-token: write

concurrency:
  group: "pages"
  cancel-in-progress: false

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v4

      - name: Setup Hugo
        uses: peaceiris/actions-hugo@v3
        with:
          hugo-version: "latest"
          extended: true

      - name: Build
        run: hugo --minify

      - name: Upload Pages artifact
        uses: actions/upload-pages-artifact@v3
        with:
          path: ./public

  deploy:
    needs: build
    runs-on: ubuntu-latest
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    steps:
      - name: Deploy to GitHub Pages
        id: deployment
        uses: actions/deploy-pages@v4
```

---

## Hugo Base Template: `layouts/_default/baseof.html`

```html
<!DOCTYPE html>
<html lang="en">
  <head>{{ partial "head.html" . }}</head>
  <body>
    {{ partial "nav.html" . }}
    {{ block "main" . }}{{ end }}
    {{ partial "footer.html" . }}
    <script src="/js/main.js"></script>
  </body>
</html>
```

## Hugo Index Template: `layouts/index.html`

```html
{{ define "main" }}
  {{ partial "hero.html" . }}
  {{ partial "who-we-live-up-for.html" . }}
  {{ partial "the-problem.html" . }}
  {{ partial "our-mission.html" . }}
  {{ partial "your-impact.html" . }}
  {{ partial "what-can-you-do.html" . }}
{{ end }}
```

---

## CSS Architecture: `static/css/main.css`

Write a single flat CSS file (no preprocessor needed). Structure it in this order:

1. CSS custom properties (design tokens — colors, fonts, spacing as listed above)
2. Google Fonts `@import`
3. Reset / base styles
4. Utility classes (`.container`, `.btn`, `.btn-gold`, `.section-heading`)
5. Nav styles
6. Section styles in page order (hero → who → problem → mission → impact → cta → footer)
7. Responsive breakpoints at the bottom (`@media (max-width: 768px)`)

**Important CSS notes:**
- Use CSS Grid for the three-column layouts in sections 3, 4, 5
- On mobile (<768px), all three-column grids collapse to single column
- The "BECAUSE THE MENTAL HEALTH..." stamp line should use `letter-spacing: 0.1em` and be large enough to feel impactful (approx `clamp(1.1rem, 3vw, 2rem)`)
- Buttons use `text-transform: uppercase`, `letter-spacing: 0.12em`, gold background, no border-radius or very slight (2px)
- Section transitions between cream and dark green create the visual rhythm — maintain generous top/bottom padding

---

## JS: `static/js/main.js`

Keep it minimal:

```js
// Smooth scroll for anchor links
document.querySelectorAll('a[href^="#"]').forEach(anchor => {
  anchor.addEventListener('click', function (e) {
    e.preventDefault();
    document.querySelector(this.getAttribute('href'))?.scrollIntoView({ behavior: 'smooth' });
  });
});

// Sticky nav shadow on scroll
const nav = document.querySelector('.site-nav');
window.addEventListener('scroll', () => {
  nav.classList.toggle('scrolled', window.scrollY > 40);
});
```

---

## Placeholder Images

Use `https://picsum.photos` for placeholder images during development. The mockup uses 4 images:

| Location | Suggested query | Dimensions |
|---|---|---|
| Who section — left photo | teens community group | 400×500 |
| Who section — right photo | youth support group | 400×500 |
| Problem — col 1 | teen contemplative | 360×280 |
| Problem — col 3 | teen struggle | 360×280 |

Use `<img src="https://picsum.photos/seed/liveup1/400/500" alt="...">` etc. with unique seeds so they don't all show the same image.

---

## GitHub Repository Setup (one-time, manual steps)

After Claude Code scaffolds the project, Jessie or you will need to:

1. Create a new GitHub repo: `YOUR_ORG/liveup-collective` (can be public or private)
2. Push the scaffolded code to `main`
3. In repo **Settings → Pages**: set source to **GitHub Actions**
4. In repo **Settings → Environments**: confirm `github-pages` environment exists
5. For Decap CMS OAuth (so the client can log in):
   - Go to [GitHub Developer Settings → OAuth Apps](https://github.com/settings/developers)
   - Create new OAuth App:
     - Homepage URL: `https://liveupcollective.org`
     - Callback URL: `https://liveupcollective.org/admin/`
   - Copy the Client ID and Secret
   - Deploy [netlify/netlify-cms-proxy-server](https://github.com/netlify/netlify-cms-proxy-server) OR use [Decap's hosted auth](https://decapcms.org/docs/github-backend/) (simplest: add `site_id` from Netlify even if not hosting there — Netlify's OAuth gateway works standalone)
6. Add custom domain in **Settings → Pages → Custom domain**: `liveupcollective.org`
7. Update DNS at registrar: add CNAME `www` → `YOUR_ORG.github.io`

---

## Implementation Order for Claude Code

Execute in this order to avoid dependency issues:

1. `hugo.toml` — site config
2. `content/_index.md` — all content as front matter
3. `static/css/main.css` — full design system and all section styles
4. `layouts/_default/baseof.html` — base shell
5. `layouts/partials/head.html` — fonts, meta, CSS link
6. `layouts/partials/nav.html`
7. `layouts/partials/hero.html`
8. `layouts/partials/who-we-live-up-for.html`
9. `layouts/partials/the-problem.html`
10. `layouts/partials/our-mission.html`
11. `layouts/partials/your-impact.html`
12. `layouts/partials/what-can-you-do.html`
13. `layouts/partials/footer.html`
14. `layouts/index.html` — assembles all partials
15. `static/js/main.js`
16. `static/admin/config.yml`
17. `static/admin/index.html`
18. `.github/workflows/hugo.yml`

After scaffolding, run `hugo server` locally to verify before pushing.

---

## Definition of Done

- [ ] `hugo server` runs without errors
- [ ] All 7 sections render and match the mockup layout
- [ ] All text content is pulled from `content/_index.md` front matter (no hardcoded copy in templates)
- [ ] `/admin` loads Decap CMS login screen
- [ ] Decap CMS form exposes all editable fields defined above
- [ ] Saving in Decap triggers a git commit to `main`
- [ ] GitHub Actions workflow runs on push and deploys to GitHub Pages
- [ ] Site is mobile-responsive (single column below 768px)
- [ ] All buttons link to `hero_donate_url` or CTA URLs from front matter
- [ ] No console errors
