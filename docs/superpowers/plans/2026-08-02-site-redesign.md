# Site Redesign Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Rebuild sazid.github.io as a high-contrast technical datasheet — bordered tables, small calm section labels, system sans-serif, automatic dark mode — with one shared visual system across every page.

**Architecture:** All colour, size, and spacing values live in CSS custom properties in one tokens file. Dark mode is a single `prefers-color-scheme` block that reassigns those properties. A small set of reusable component classes (nav, sheet, section label, entry, button, footer) is defined once and consumed by every page. Page-specific CSS is reduced to near zero. No JavaScript is added.

**Tech Stack:** Jekyll 3.10 (via the `github-pages` gem), SCSS, Liquid templates. No new gems, no new webfonts, no JavaScript.

**Source spec:** `docs/superpowers/specs/2026-08-02-site-redesign-design.md`

---

## Toolchain

The system Ruby on this machine (2.6.10) is too old for this project's
dependencies, and the Homebrew Ruby (4.0.6) is too new for the pinned
`github-pages` gem. **Use Docker.** Both commands below were verified working
against this repo before this plan was written.

Build once:

```bash
docker run --rm -v "$PWD":/site -w /site -e BUNDLE_PATH=/bundle -v jekyll-bundle:/bundle ruby:3.3 bash -c "bundle install --quiet && bundle exec jekyll build"
```

Run a live-reloading dev server on http://localhost:4000:

```bash
docker run -d --name jekyll-dev -v "$PWD":/site -w /site -e BUNDLE_PATH=/bundle -v jekyll-bundle:/bundle -p 4000:4000 -p 35729:35729 ruby:3.3 bash -c "bundle install --quiet && bundle exec jekyll serve --host 0.0.0.0 --livereload --force_polling"
```

Stop it:

```bash
docker rm -f jekyll-dev
```

The first run pulls the `ruby:3.3` image and installs gems into a named Docker
volume, which takes a few minutes. Later runs start in seconds. Generated files
are owned by your user, not root.

## Global Constraints

These apply to every task. They are not repeated in each task's steps.

- **No raw hex colours outside `_sass/_tokens.scss`.** Every other stylesheet
  file must reference `var(--token-name)`. This is what makes dark mode work.
- **No font size below 16px for body text.** All sizes in `rem` or `em`, never `px`.
- **Nothing may set `outline: none`.** Ever, on any selector.
- **No JavaScript.** No new gems. No new webfont downloads.
- **The maroon `#7a1f3d` must not appear anywhere** in the final stylesheets.
- **Every `<th>` carries a `scope` attribute.** Every `<table>` has a `<caption>`.
- **No page may scroll horizontally at 375px width.** Wide tables scroll inside
  their own container instead.
- **Content is not rewritten.** The 22 files in `_posts/` are never edited.
- **Commit after every task.** No AI attribution lines in commit messages.

## Two deliberate deviations from the spec

Both are recorded here so a reviewer does not treat them as mistakes.

1. **The spec lists `_data/projects.yml` as unchanged, but Task 6 edits two
   lines of it.** Its `summary.title` is currently "Project Timeline" and its
   `summary.eyebrow` is "Projects". The redesign removes the timeline, so that
   title becomes wrong. Task 6 changes the title to "Projects" and deletes the
   now-redundant eyebrow. No project entries are touched.

2. **The homepage intro paragraph is removed.** The approved design for the home
   page is: name, About sheets, Writing table. The current paragraph ("Random
   thoughts and stuffs that piqued my interest…") has no place in that layout.
   It is not moved elsewhere. If it should be kept, say so before Task 2.

## File Structure

| File | Responsibility |
|---|---|
| `_sass/_tokens.scss` | **New.** Every colour (both modes), type size, spacing step, and layout width. The only file containing hex values. |
| `_sass/_base.scss` | **New.** Element defaults: body, headings, paragraphs, lists, links, code, images, focus ring, skip link, the `.shell` wrapper, the `.visually-hidden` helper, and the JetBrains Mono `@font-face`. |
| `_sass/_components.scss` | **New.** The reusable pieces: `.nav`, `.sheet`, `.section-label`, `.entry`, `.button`, `.footer`, plus the single 700px responsive block. |
| `_sass/_pages.scss` | **New.** Genuine one-offs only. Starts as a holding pen for legacy CV/Projects rules and is emptied by Tasks 5 and 6. |
| `_sass/main.scss` | **Rewritten.** Imports only. No rules of its own. |
| `_layouts/default.html` | **Rewritten.** Adds skip link, `.shell` wrapper, `<main>` landmark, footer include. |
| `_layouts/post.html` | **Rewritten.** Title, monospace date, article at reading measure. |
| `_includes/navigation.html` | **Rewritten.** The nav bar component. |
| `_includes/footer.html` | **New.** Hairline rule and copyright line. |
| `_data/profile.yml` | **New.** The facts shown in the homepage identity and contact sheets, in one editable place. |
| `index.html` | **Rewritten.** Name, two sheets, post table. |
| `about.md` | **Rewritten.** Prose bio plus a link sheet. |
| `resume.md` | **Rewritten.** Same content, entry components, no `.cv-*` classes. |
| `projects.md` | **Rewritten.** Same content, entry components, no timeline. |
| `.gitignore` | **Modified.** Add build output. |

Unchanged: `_posts/*`, `_data/resume.yml`, `_data/navigation.yml`, `_config.yml`, `Gemfile`, `assets/fonts/*`, `assets/images/*`, `assets/pdfs/*`, `assets/css/styles.scss`.

---

### Task 1: Ignore build output and confirm the toolchain

Nothing in this repo currently ignores Jekyll's output, so the first build
leaves untracked files that will pollute every later commit. Fix that before
touching anything else, and prove the build works.

**Files:**
- Modify: `.gitignore`

- [ ] **Step 1: Confirm the build currently works**

```bash
docker run --rm -v "$PWD":/site -w /site -e BUNDLE_PATH=/bundle -v jekyll-bundle:/bundle ruby:3.3 bash -c "bundle install --quiet && bundle exec jekyll build"
```

Expected: ends with `done in N seconds.` and no error lines. A warning about
`faraday-retry` and one about GitHub API authentication are both normal and can
be ignored.

- [ ] **Step 2: Confirm the build leaves untracked files**

```bash
git status --short
```

Expected: shows `?? Gemfile.lock` and `?? _site/`.

- [ ] **Step 3: Ignore them**

Replace the entire contents of `.gitignore` with:

```
.jekyll-cache
_site/
Gemfile.lock
```

`Gemfile.lock` is ignored because GitHub Pages resolves gem versions from the
`github-pages` gem itself and ignores any lockfile you commit. Keeping it out
avoids a file that changes every time someone builds on a different Ruby.

- [ ] **Step 4: Confirm the working tree is clean apart from .gitignore**

```bash
git status --short
```

Expected: exactly one line, `M .gitignore`.

- [ ] **Step 5: Commit**

```bash
git add .gitignore
git commit -m "Ignore Jekyll build output and Gemfile.lock"
```

---

### Task 2: Design tokens, base styles, and the page shell

This task installs the whole foundation: the colour system, dark mode,
typography, the skip link, the `<main>` landmark, the nav bar, and the footer.

The legacy CV and Projects rules are moved into `_pages.scss` with their
hardcoded colours swapped for tokens, so those two pages stay correct in dark
mode until Tasks 5 and 6 delete the rules entirely.

**Files:**
- Create: `_sass/_tokens.scss`
- Create: `_sass/_base.scss`
- Create: `_sass/_components.scss`
- Create: `_sass/_pages.scss`
- Create: `_includes/footer.html`
- Modify: `_sass/main.scss` (replace entirely)
- Modify: `_layouts/default.html` (replace entirely)
- Modify: `_includes/navigation.html` (replace entirely)

**Interfaces:**
- Produces, for every later task: the CSS custom properties listed in
  `_tokens.scss`; the classes `.shell`, `.prose`, `.visually-hidden`,
  `.skip-link`, `.nav`, `.footer`; and a `<main id="content">` element that page
  content is rendered into.

- [ ] **Step 1: Create the tokens file**

Create `_sass/_tokens.scss`:

```scss
// The only file in this project that may contain a raw colour value.
// Everything else references these custom properties, which is what makes the
// dark mode block below work without duplicating any rules.

:root {
  color-scheme: light dark;

  // Colour — light
  --bg:            #ffffff;
  --text:          #000000;
  --muted:         #555555;
  --border:        #cfcfcf;
  --border-strong: #000000;
  --fill:          #efefef;
  --link:          #0045e4;
  --code-bg:       #f0f0f0;

  // Type
  --font-sans: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto,
               "Helvetica Neue", Arial, sans-serif;
  --font-mono: "JetBrains Mono", ui-monospace, SFMono-Regular, Menlo, monospace;

  --size-body:  1rem;        // 16px — never go below this
  --size-small: 0.875rem;    // 14px — meta, captions, dates only
  --size-h1:    1.75rem;     // 28px
  --size-h3:    1.0625rem;   // 17px

  --leading-body:    1.55;
  --leading-heading: 1.3;

  // Spacing scale
  --space-1: 4px;
  --space-2: 8px;
  --space-3: 12px;
  --space-4: 16px;
  --space-5: 24px;
  --space-6: 40px;

  // Layout
  --shell:   900px;   // full page width, used by tables
  --measure: 68ch;    // reading width, used by prose only

  --rule: 1px;
}

// Dark mode reassigns colours only. No layout or type changes.
// Background is #111111 rather than pure black: light text on pure black
// causes visible glare for many readers.
@media (prefers-color-scheme: dark) {
  :root {
    --bg:            #111111;
    --text:          #e8e8e8;
    --muted:         #a0a0a0;
    --border:        #333333;
    --border-strong: #e8e8e8;
    --fill:          #1c1c1c;
    --link:          #7aa7ff;
    --code-bg:       #1a1a1a;
  }
}
```

- [ ] **Step 2: Create the base stylesheet**

Create `_sass/_base.scss`:

```scss
@font-face {
  font-family: 'JetBrains Mono';
  src: url('/assets/fonts/JetBrainsMono-Regular.woff2') format('woff2');
  font-display: swap;
}

*, *::before, *::after { box-sizing: border-box; }

body {
  margin: 0;
  background: var(--bg);
  color: var(--text);
  font-family: var(--font-sans);
  font-size: var(--size-body);
  line-height: var(--leading-body);
  text-rendering: optimizeLegibility;
  -webkit-text-size-adjust: 100%;
}

// Full page width. Tables use all of it.
.shell {
  width: 100%;
  max-width: var(--shell);
  margin: 0 auto;
  padding: 0 var(--space-4);
}

// Comfortable reading width. Prose uses this, tables never do.
.prose { max-width: var(--measure); }

h1, h2, h3 {
  line-height: var(--leading-heading);
  font-weight: 700;
}

h1 { font-size: var(--size-h1); margin: var(--space-6) 0 var(--space-4); }
h2 { font-size: var(--size-body); margin: var(--space-6) 0 var(--space-3); }
h3 { font-size: var(--size-h3); margin: 0; }

p { margin: 0 0 var(--space-4); }

ul, ol { margin: 0 0 var(--space-4); padding-left: var(--space-5); }
li + li { margin-top: var(--space-2); }

a { color: var(--link); text-decoration: none; }
a:hover, a:focus-visible { text-decoration: underline; }

// Visible focus for keyboard users on every focusable element.
// Nothing in this project may override this with `outline: none`.
:focus-visible {
  outline: 2px solid var(--text);
  outline-offset: 2px;
}

code {
  font-family: var(--font-mono);
  font-size: 0.9375em;
  background: var(--code-bg);
  padding: 0 var(--space-1);
}

pre {
  margin: 0 0 var(--space-4);
  padding: var(--space-3);
  overflow-x: auto;
  border: var(--rule) solid var(--border);
  background: var(--code-bg);
  font-size: var(--size-small);
  line-height: 1.45;
}

pre code { padding: 0; background: transparent; font-size: inherit; }

img { max-width: 100%; height: auto; }

hr {
  border: 0;
  border-top: var(--rule) solid var(--border);
  margin: var(--space-6) 0;
}

// Present for screen readers, invisible on screen. Used for table captions
// where an adjacent heading already names the table.
.visually-hidden {
  position: absolute;
  width: 1px;
  height: 1px;
  margin: -1px;
  padding: 0;
  overflow: hidden;
  clip-path: inset(50%);
  white-space: nowrap;
}

.skip-link {
  position: absolute;
  left: var(--space-4);
  top: -100px;
  z-index: 10;
  padding: var(--space-2) var(--space-3);
  border: var(--rule) solid var(--border-strong);
  background: var(--bg);
  color: var(--text);
}

.skip-link:focus { top: var(--space-2); text-decoration: none; }

// The skip link targets <main>, which needs tabindex="-1" to accept focus.
// That would otherwise draw an outline on mouse click, which :focus-visible
// already prevents, so nothing further is needed here.
main:focus { outline: none; }
```

Note on the last rule: this is the one permitted `outline: none`, and only
because `:focus-visible` above still draws a ring for keyboard users. Do not
copy this pattern anywhere else.

- [ ] **Step 3: Create the components stylesheet with nav and footer**

Create `_sass/_components.scss`. The sheet, entry, and button components are
added in later tasks; this task adds only what the shell needs.

```scss
// ── Navigation bar ──────────────────────────────────────────────────────────

.nav {
  display: flex;
  flex-wrap: wrap;
  gap: var(--space-3);
  justify-content: space-between;
  align-items: baseline;
  padding: var(--space-3) 0;
  border-bottom: 2px solid var(--border-strong);
}

.nav-label {
  margin: 0;
  font-weight: 700;
}

.nav-links {
  display: flex;
  flex-wrap: wrap;
  gap: var(--space-4);
  margin: 0;
  padding: 0;
  list-style: none;
}

.nav-links li + li { margin-top: 0; }

// Current page is bold and not a link. Never signalled by colour alone.
.nav-current { font-weight: 700; }

// ── Footer ──────────────────────────────────────────────────────────────────

.footer {
  margin-top: var(--space-6);
  padding: var(--space-4) 0;
  border-top: var(--rule) solid var(--border);
  color: var(--muted);
  font-size: var(--size-small);
}

.footer p { margin: 0; }

// ── Responsive ──────────────────────────────────────────────────────────────
// One breakpoint for the whole site. Later tasks add rules inside this block.

@media (max-width: 700px) {
  .nav { display: block; }
  .nav-label { margin-bottom: var(--space-2); }
}
```

- [ ] **Step 4: Create the holding pen for legacy page rules**

Create `_sass/_pages.scss`. This is the existing CV and Projects CSS with every
hardcoded colour replaced by a token, so those pages remain readable in dark
mode. Tasks 5 and 6 delete these blocks entirely.

The replacements applied are:

| Old value | Token |
|---|---|
| `#171717` | `var(--text)` |
| `#7a1f3d` | `var(--text)` |
| `#303030`, `#3d3d3d`, `#4d4d4d`, `#555555`, `#666666`, `#6a6a6a` | `var(--muted)` |
| `#c9c9c9`, `#d8d8d8`, `#d9d9d9`, `#e4e4e4`, `#e6e6e6`, `#ececec`, `#eeeeee` | `var(--border)` |
| `#f4f4f4`, `#f7f7f7`, `#fafafa` | `var(--fill)` |
| `#ffffff` | `var(--bg)` |
| `#0045e4` | `var(--link)` |

```scss
// TEMPORARY. Legacy CV and Projects styling, retokenised so dark mode is not
// broken between commits. Task 5 deletes every .cv-* rule and Task 6 deletes
// every .resume-*, .projects-*, and .project-* rule. This file should be empty
// by the end of the plan.

.resume-page {
  width: min(960px, calc(100vw - 32px));
  margin-left: 50%;
  transform: translateX(-50%);
}

.resume-embed {
  width: 100%;
  min-height: 720px;
  height: calc(100vh - 180px);
  border: var(--rule) solid var(--border);
}

.cv-page {
  width: min(980px, calc(100vw - 32px));
  margin-left: 50%;
  transform: translateX(-50%);
  padding-bottom: 56px;
  color: var(--text);
  line-height: 1.55;
}

.cv-header {
  padding: 28px 0 24px;
  margin-bottom: 34px;
  border-bottom: 2px solid var(--text);
}

.cv-label {
  margin: 0 0 10px;
  color: var(--text);
  font-size: 13px;
  font-weight: 700;
  text-transform: uppercase;
}

.cv-header h1 { margin: 0 0 14px; font-size: 42px; line-height: 1.08; }

.cv-meta {
  display: flex;
  flex-wrap: wrap;
  gap: 8px 18px;
  padding: 0;
  margin: 0;
  list-style: none;
  color: var(--muted);
}

.cv-meta li { white-space: nowrap; }
.cv-download { margin: 22px 0 0; }

.cv-download a {
  display: inline-block;
  padding: 8px 12px;
  border: var(--rule) solid var(--text);
  color: var(--text);
  background: var(--fill);
}

.cv-download a:hover {
  background: var(--text);
  color: var(--bg);
  text-decoration: none;
}

.cv-section { margin-top: 36px; }

.cv-section h2 {
  padding-bottom: 8px;
  margin: 0 0 16px;
  border-bottom: var(--rule) solid var(--border);
  color: var(--text);
  font-size: 20px;
}

.cv-skill-list { margin: 0; }

.cv-skill-list div {
  display: grid;
  grid-template-columns: 220px minmax(0, 1fr);
  gap: 16px;
  padding: 12px 0;
  border-top: var(--rule) solid var(--border);
}

.cv-skill-list div:first-child { border-top: 0; }
.cv-skill-list dt { font-weight: 700; }
.cv-skill-list dd { margin: 0; }

.cv-entry { padding: 18px 0 20px; border-top: var(--rule) solid var(--border); }
.cv-entry:first-of-type { border-top: 0; padding-top: 0; }

.cv-entry-heading {
  display: grid;
  grid-template-columns: minmax(0, 1fr) minmax(220px, 0.72fr);
  gap: 16px;
  align-items: baseline;
}

.cv-entry-heading h3 { margin: 0; font-size: 18px; }
.cv-entry-heading p { margin: 0; color: var(--muted); text-align: right; }
.cv-period { margin: 4px 0 10px; color: var(--muted); }
.cv-entry ul { padding-left: 22px; margin: 0; }
.cv-entry li + li { margin-top: 8px; }

.projects-page {
  width: min(920px, calc(100vw - 32px));
  margin-left: 50%;
  transform: translateX(-50%);
  padding-bottom: 56px;
  color: var(--text);
  line-height: 1.58;
}

.projects-header {
  padding: 24px 0 20px;
  margin-bottom: 24px;
  border-bottom: var(--rule) solid var(--border);
}

.projects-label {
  margin: 0 0 8px;
  color: var(--text);
  font-size: 13px;
  font-weight: 700;
  text-transform: uppercase;
}

.projects-header h1 { margin: 0 0 12px; font-size: 36px; line-height: 1.12; }
.projects-intro { max-width: 72ch; margin: 0; color: var(--muted); }

.project-timeline { position: relative; padding: 4px 0 0 0; margin: 0; list-style: none; }

.project-timeline::before {
  content: "";
  position: absolute;
  top: 12px;
  bottom: 0;
  left: 116px;
  width: 1px;
  background: var(--border);
}

.project-event {
  position: relative;
  display: grid;
  grid-template-columns: 96px minmax(0, 1fr);
  gap: 40px;
  padding: 0 0 26px;
}

.project-event::before {
  content: "";
  position: absolute;
  top: 7px;
  left: 113px;
  width: 7px;
  height: 7px;
  background: var(--text);
}

.project-event time {
  color: var(--muted);
  font-size: 13px;
  font-weight: 700;
  line-height: 1.4;
  text-align: right;
}

.project-item { padding-bottom: 24px; border-bottom: var(--rule) solid var(--border); }

.project-item-heading {
  display: grid;
  grid-template-columns: minmax(0, 0.92fr) minmax(220px, 1fr);
  gap: 16px;
  align-items: baseline;
}

.project-item-heading h2 { margin: 0; font-size: 17px; }
.project-item-heading p { margin: 0; color: var(--muted); font-size: 14px; text-align: right; }
.project-status { margin: 6px 0 0; color: var(--muted); font-size: 14px; }
.project-summary { margin: 9px 0 0; }
.project-details { padding-left: 18px; margin: 10px 0 0; }
.project-details li + li { margin-top: 5px; }

.project-showcase {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(220px, 1fr));
  gap: 12px;
  margin-top: 14px;
}

.project-showcase-item { min-width: 0; margin: 0; }

.project-showcase img {
  display: block;
  width: 100%;
  max-width: 100%;
  max-height: 240px;
  margin: 0;
  border: var(--rule) solid var(--border);
  background: var(--fill);
  object-fit: contain;
}

.project-showcase figcaption { margin-top: 5px; color: var(--muted); font-size: 13px; }
.project-showcase-output { grid-column: 1 / -1; }

.project-showcase-output pre {
  margin: 0;
  padding: 10px 12px;
  overflow-x: auto;
  border: var(--rule) solid var(--border);
  background: var(--fill);
  font-size: 13px;
  line-height: 1.45;
}

.project-links { display: flex; flex-wrap: wrap; gap: 12px; margin-top: 10px; }
.project-links a { color: var(--link); font-size: 13px; font-weight: 700; }

@media screen and (max-width: 1024px) {
  .cv-header h1 { font-size: 30px; }
  .cv-meta li { white-space: normal; }
  .cv-skill-list div, .cv-entry-heading { display: block; }
  .cv-entry-heading p { margin-top: 4px; text-align: left; }
  .projects-header h1 { font-size: 30px; }
  .project-timeline::before { left: 3px; }
  .project-event { display: block; padding-left: 22px; }
  .project-event::before { left: 0; }
  .project-event time { display: block; margin-bottom: 8px; text-align: left; }
  .project-item-heading { display: block; }
  .project-item-heading p { margin-top: 4px; text-align: left; }
}
```

- [ ] **Step 5: Rewrite main.scss as imports only**

Replace the entire contents of `_sass/main.scss` with:

```scss
@import "tokens";
@import "base";
@import "components";
@import "pages";
```

The Google Fonts `@import` for Roboto Mono is deliberately dropped. It was a
render-blocking request to a third-party server, and the site no longer uses
that font.

- [ ] **Step 6: Create the footer include**

Create `_includes/footer.html`:

```html
<footer class="footer">
  <p>&copy; {{ site.time | date: "%Y" }} Mohammed Sazid Al Rashid</p>
</footer>
```

- [ ] **Step 7: Rewrite the navigation include**

Replace the entire contents of `_includes/navigation.html` with:

```html
<nav class="nav" aria-label="Primary">
  <p class="nav-label">Navigation</p>
  <ul class="nav-links">
    {% for item in site.data.navigation %}
    <li>
      {% if page.url == item.link %}
      <span class="nav-current" aria-current="page">{{ item.name }}</span>
      {% else %}
      <a href="{{ item.link }}">{{ item.name }}</a>
      {% endif %}
    </li>
    {% endfor %}
  </ul>
</nav>
```

- [ ] **Step 8: Rewrite the default layout**

Replace the entire contents of `_layouts/default.html` with:

```html
<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  {% seo %}
  <title>{{ page.title }} | sazid</title>
  <link rel="stylesheet" href="/assets/css/styles.css">
</head>

<body>
  <a class="skip-link" href="#content">Skip to content</a>
  <div class="shell">
    {% include navigation.html %}
    <main id="content" tabindex="-1">
      {{ content }}
    </main>
    {% include footer.html %}
  </div>
</body>

</html>
```

- [ ] **Step 9: Build and verify the generated HTML**

```bash
docker run --rm -v "$PWD":/site -w /site -e BUNDLE_PATH=/bundle -v jekyll-bundle:/bundle ruby:3.3 bash -c "bundle install --quiet && bundle exec jekyll build"
```

Expected: `done in N seconds.`, no errors.

Then check the shell rendered:

```bash
grep -c 'class="skip-link"' _site/index.html
grep -c '<main id="content"' _site/index.html
grep -c 'class="footer"' _site/index.html
grep -c 'aria-current="page"' _site/index.html
```

Expected: `1` from each of the four commands.

And check no hex colours leaked outside the tokens file:

```bash
grep -nE '#[0-9a-fA-F]{3,6}\b' _sass/_base.scss _sass/_components.scss _sass/_pages.scss
```

Expected: no output at all.

- [ ] **Step 10: Look at it**

Start the dev server:

```bash
docker rm -f jekyll-dev 2>/dev/null; docker run -d --name jekyll-dev -v "$PWD":/site -w /site -e BUNDLE_PATH=/bundle -v jekyll-bundle:/bundle -p 4000:4000 -p 35729:35729 ruby:3.3 bash -c "bundle install --quiet && bundle exec jekyll serve --host 0.0.0.0 --livereload --force_polling"
```

Open http://localhost:4000 and confirm:
- Body text is sans-serif, not monospace.
- The nav bar shows "Navigation" on the left, the four links on the right, with
  a thick rule underneath. The current page is bold and not clickable.
- Pressing Tab once reveals the "Skip to content" link at the top left.
- Every link shows a clear outline when tabbed to.
- The footer appears at the bottom of every page.
- Switching the operating system to dark mode flips the whole site, including
  the CV and Projects pages, with no unreadable dark-on-dark text anywhere.

- [ ] **Step 11: Commit**

```bash
git add _sass _includes/footer.html _includes/navigation.html _layouts/default.html
git commit -m "Add design tokens, base styles, dark mode, and page shell"
```

---

### Task 3: The sheet component and the new homepage

**Files:**
- Create: `_data/profile.yml`
- Modify: `_sass/_components.scss` (append the sheet component and its responsive rules)
- Modify: `index.html` (replace entirely)

**Interfaces:**
- Consumes from Task 2: `.shell`, `.section-label` sizing from the `h2` rule,
  `.visually-hidden`, the `--fill` and `--border-strong` tokens.
- Produces for Tasks 4, 5, 6: the classes `.sheet`, `.sheet--rows`,
  `.sheet-wrap`, `.sheet-title`, `.sheet-grid`, `.section-label`, and
  `.post-table`.

**Markup rule for every sheet, used by this task and Tasks 4, 5, and 6.** The
wrapper always carries these three attributes, and the table always has exactly
one caption:

```html
<div class="sheet-wrap" tabindex="0" role="region" aria-label="NAME">
  <table class="sheet">
    <caption class="sheet-title">NAME</caption>   <!-- or class="visually-hidden" -->
    …
  </table>
</div>
```

Use `class="sheet-title"` when the table needs its own visible grey title bar.
Use `class="visually-hidden"` when a `.section-label` heading directly above
already names it. The `aria-label` on the wrapper matches the caption text
either way.

- [ ] **Step 1: Append the sheet component to `_sass/_components.scss`**

Insert these blocks **before** the existing `// ── Responsive` comment:

```scss
// ── Section label ───────────────────────────────────────────────────────────
// Body-sized bold text with a hairline rule under it. Deliberately not large
// and not uppercase: capitals reduce word-shape cues and slow reading.

.section-label {
  padding-bottom: var(--space-2);
  border-bottom: var(--rule) solid var(--border);
}

// ── Sheet ───────────────────────────────────────────────────────────────────
// The bordered data table. Two variants:
//   .sheet          column headers in <thead>, e.g. the post list
//   .sheet--rows    first cell of each row is a <th scope="row">, for
//                   name/value pairs like the identity and contact sheets

// The wrapper is the scroll container. It carries tabindex="0" and
// role="region" in the markup so that when a table is too wide to fit, a
// keyboard user can focus it and scroll it with the arrow keys. Without that,
// wide table content is unreachable without a mouse.
.sheet-wrap {
  overflow-x: auto;
  margin: 0 0 var(--space-4);
}

.sheet {
  width: 100%;
  border-collapse: collapse;
  border: var(--rule) solid var(--border-strong);
}

// A caption styled to look like the grey title bar at the top of the table.
// Use this when the table has no visible heading of its own. When a
// .section-label already names the table, use <caption class="visually-hidden">
// instead so the name reaches screen readers without being shown twice.
.sheet-title {
  padding: var(--space-2) var(--space-3);
  border: var(--rule) solid var(--border-strong);
  border-bottom: 0;
  background: var(--fill);
  text-align: left;
  font-weight: 700;
}

.sheet th,
.sheet td {
  padding: var(--space-2) var(--space-3);
  border: var(--rule) solid var(--border);
  text-align: left;
  vertical-align: top;
}

.sheet thead th {
  background: var(--fill);
  font-weight: 700;
}

// Row headers are labels, not emphasis, so they are not bold.
.sheet--rows tbody th {
  width: 34%;
  font-weight: 400;
}

.sheet li + li { margin-top: var(--space-1); }
.sheet ul { margin: 0; padding-left: var(--space-4); }

// Two sheets side by side on the homepage.
.sheet-grid {
  display: grid;
  grid-template-columns: minmax(0, 1fr) minmax(0, 0.85fr);
  gap: var(--space-4);
  align-items: start;
}

.sheet-grid .sheet-wrap { margin-bottom: 0; }

// ── Post list ───────────────────────────────────────────────────────────────

.post-table td:first-child {
  width: 9rem;
  font-family: var(--font-mono);
  font-size: var(--size-small);
  white-space: nowrap;
}
```

Then add these lines **inside** the existing `@media (max-width: 700px)` block:

```scss
  .sheet-grid { grid-template-columns: 1fr; }
  .sheet-grid .sheet-wrap { margin-bottom: var(--space-4); }
  .sheet--rows tbody th { width: auto; }
```

- [ ] **Step 2: Create the profile data file**

Create `_data/profile.yml`. The values come from `_data/resume.yml` and
`about.md`, which stay as they are — this file exists so the homepage facts are
editable in one place without touching the CV.

```yaml
name: Mohammed Sazid Al Rashid

identity:
  - label: Name
    value: Mohammed Sazid Al Rashid
  - label: Occupation
    value: Technical Lead at Automation Solutionz
  - label: Location
    value: Dhaka, Bangladesh
  - label: Focus
    value: Systems engineering, agentic AI, developer tooling
  - label: Education
    value: BSc in Computer Science and Software Engineering, American International University Bangladesh

contact:
  - label: Email
    text: sazidozon@gmail.com
    url: mailto:sazidozon@gmail.com
  - label: GitHub
    text: sazid
    url: https://github.com/sazid
  - label: LinkedIn
    text: sazidz
    url: https://www.linkedin.com/in/sazidz/
  - label: Stack Overflow
    text: sazid
    url: https://stackoverflow.com/users/1941132/sazid
```

- [ ] **Step 3: Rewrite the homepage**

Replace the entire contents of `index.html` with:

```html
---
layout: default
title: Home
---

{% assign profile = site.data.profile %}

<h1>{{ profile.name }}</h1>

<h2 class="section-label">About</h2>

<div class="sheet-grid">
  <div class="sheet-wrap" tabindex="0" role="region" aria-label="Personal information">
    <table class="sheet sheet--rows">
      <caption class="sheet-title">Personal information</caption>
      <tbody>
        {% for row in profile.identity %}
        <tr>
          <th scope="row">{{ row.label }}</th>
          <td>{{ row.value }}</td>
        </tr>
        {% endfor %}
      </tbody>
    </table>
  </div>

  <div class="sheet-wrap" tabindex="0" role="region" aria-label="Contact">
    <table class="sheet sheet--rows">
      <caption class="sheet-title">Contact</caption>
      <tbody>
        {% for row in profile.contact %}
        <tr>
          <th scope="row">{{ row.label }}</th>
          <td><a href="{{ row.url }}">{{ row.text }}</a></td>
        </tr>
        {% endfor %}
      </tbody>
    </table>
  </div>
</div>

<h2 class="section-label">Writing</h2>

<div class="sheet-wrap" tabindex="0" role="region" aria-label="Posts">
  <table class="sheet post-table">
    <caption class="visually-hidden">Posts, newest first</caption>
    <thead>
      <tr>
        <th scope="col">Date</th>
        <th scope="col">Title</th>
      </tr>
    </thead>
    <tbody>
      {% for post in site.posts %}
      <tr>
        <td>{{ post.date | date: "%Y-%m-%d" }}</td>
        <td><a href="{{ post.url }}">{{ post.title }}</a></td>
      </tr>
      {% endfor %}
    </tbody>
  </table>
</div>
```

Dates change from Jekyll's `date_to_string` ("18 Jan 2019") to ISO format
("2019-01-18"). ISO dates sort visually, align in a monospace column, and are
unambiguous internationally.

- [ ] **Step 4: Build and verify**

```bash
docker run --rm -v "$PWD":/site -w /site -e BUNDLE_PATH=/bundle -v jekyll-bundle:/bundle ruby:3.3 bash -c "bundle install --quiet && bundle exec jekyll build"
```

Then confirm the tables rendered with the right number of rows:

```bash
grep -c 'scope="row"' _site/index.html
grep -c '<table class="sheet post-table"' _site/index.html
grep -c 'role="region"' _site/index.html
grep -o 'href="/[0-9]\{4\}/' _site/index.html | wc -l
```

Expected: `9` (five identity rows plus four contact rows), `1`, `3` (one per
sheet), and `22` (one link per post).

- [ ] **Step 5: Look at it**

Open http://localhost:4000 and confirm:
- Two bordered tables sit side by side under an "About" label, each with a grey
  header row.
- The post list below is a single bordered table, newest post first, dates in
  monospace.
- At a narrow window the two tables stack into one column and nothing scrolls
  sideways.

- [ ] **Step 6: Commit**

```bash
git add _data/profile.yml _sass/_components.scss index.html
git commit -m "Add sheet component and rebuild homepage as a datasheet"
```

---

### Task 4: Prose pages — post layout and About

Both pages in this task are prose-led, and both introduce the reading-measure
rule that keeps paragraphs narrower than tables.

**Files:**
- Modify: `_layouts/post.html` (replace entirely)
- Modify: `_sass/_components.scss` (append the post meta rule)
- Modify: `about.md` (replace entirely)

**Interfaces:**
- Consumes from Task 3: `.sheet`, `.sheet-wrap`, `.section-label`,
  `.visually-hidden`.
- Produces: the `.post-meta` class.

- [ ] **Step 1: Append the post meta rule to `_sass/_components.scss`**

Insert before the `// ── Responsive` comment:

```scss
// ── Post ────────────────────────────────────────────────────────────────────

.post-meta {
  margin: 0 0 var(--space-6);
  color: var(--muted);
  font-family: var(--font-mono);
  font-size: var(--size-small);
}
```

- [ ] **Step 2: Rewrite the post layout**

Replace the entire contents of `_layouts/post.html` with:

```html
---
layout: default
---
<article>
  <h1>{{ page.title }}</h1>
  <p class="post-meta">
    <time datetime="{{ page.date | date: '%Y-%m-%d' }}">{{ page.date | date: "%Y-%m-%d" }}</time>
  </p>
  <div class="prose">
    {{ content }}
  </div>
</article>
```

The `<time datetime="…">` element gives assistive technology and search engines
a machine-readable date, which the current template does not.

- [ ] **Step 3: Rewrite the About page**

Replace the entire contents of `about.md` with:

```markdown
---
layout: default
title: About
---

<h1>About</h1>

<div class="prose">
  <p>
    I'm Mohammed Sazid Al Rashid, a human. Love cats, coding and playing
    football!
  </p>
  <p>
    Reach me at <a href="mailto:sazidozon@gmail.com">sazidozon@gmail.com</a>.
  </p>
</div>

<h2 class="section-label">Elsewhere</h2>

<div class="sheet-wrap" tabindex="0" role="region" aria-label="Profiles on other sites">
  <table class="sheet">
    <caption class="visually-hidden">Profiles on other sites</caption>
    <thead>
      <tr>
        <th scope="col">Platform</th>
        <th scope="col">Link</th>
      </tr>
    </thead>
    <tbody>
      <tr>
        <th scope="row">GitHub</th>
        <td><a href="https://github.com/sazid">github.com/sazid</a></td>
      </tr>
      <tr>
        <th scope="row">Stack Overflow</th>
        <td><a href="https://stackoverflow.com/users/1941132/sazid">stackoverflow.com/users/1941132/sazid</a></td>
      </tr>
      <tr>
        <th scope="row">LinkedIn</th>
        <td><a href="https://www.linkedin.com/in/sazidz/">linkedin.com/in/sazidz</a></td>
      </tr>
      <tr>
        <th scope="row">Play Store</th>
        <td><a href="https://play.google.com/store/search?q=pub%3ASazid&amp;utm_source=opensearch">Apps published by Sazid</a></td>
      </tr>
    </tbody>
  </table>
</div>
```

The email address changes from the obfuscated "sazidozon at gmail com" to a real
`mailto:` link. Obfuscation of that kind does not deter scrapers and does make
the address harder for people to use.

- [ ] **Step 4: Build and verify**

```bash
docker run --rm -v "$PWD":/site -w /site -e BUNDLE_PATH=/bundle -v jekyll-bundle:/bundle ruby:3.3 bash -c "bundle install --quiet && bundle exec jekyll build"
```

Then:

```bash
grep -c 'class="prose"' _site/2020/05/11/cat.html
grep -c '<time datetime="2020-05-11"' _site/2020/05/11/cat.html
grep -c 'mailto:sazidozon@gmail.com' _site/about.html
grep -c 'scope="row"' _site/about.html
```

Expected: `1`, `1`, `1`, `4`.

If the post path differs, list what was generated with
`ls _site/2020/05/11/` and use the actual filename.

- [ ] **Step 5: Look at it**

Open http://localhost:4000/about.html and any post. Confirm:
- Post paragraphs are noticeably narrower than the nav bar above them.
- The post date sits under the title in monospace.
- Code blocks in a post such as
  http://localhost:4000/2020/09/02/rust-env-logger-variable.html are bordered
  and monospace, and scroll rather than overflow.

- [ ] **Step 6: Commit**

```bash
git add _sass/_components.scss _layouts/post.html about.md
git commit -m "Rebuild post layout and About page with reading measure and link sheet"
```

---

### Task 5: The entry component and the CV page

**Files:**
- Modify: `_sass/_components.scss` (append the entry and button components)
- Modify: `_sass/_pages.scss` (delete all `.cv-*` and `.resume-*` rules)
- Modify: `resume.md` (replace entirely)

**Interfaces:**
- Consumes from Task 3: `.sheet`, `.sheet--rows`, `.sheet-wrap`,
  `.section-label`, `.visually-hidden`.
- Produces for Task 6: the classes `.entry`, `.entry-head`, `.entry-meta`,
  `.entry-period`, `.entry-list`, `.button`, `.meta-list`.

- [ ] **Step 1: Append the entry and button components to `_sass/_components.scss`**

Insert before the `// ── Responsive` comment:

```scss
// ── Entry ───────────────────────────────────────────────────────────────────
// For content that looks like a table row but is not tabular: a job, a project,
// a degree. Uses a real heading and a real list so screen readers announce it
// correctly, while matching the sheets visually.

.entry {
  padding: var(--space-5) 0;
  border-top: var(--rule) solid var(--border);
}

.entry:first-of-type {
  padding-top: var(--space-2);
  border-top: 0;
}

.entry-head {
  display: grid;
  grid-template-columns: minmax(0, 1fr) minmax(0, 0.7fr);
  gap: var(--space-4);
  align-items: baseline;
}

.entry-meta {
  margin: 0;
  color: var(--muted);
  text-align: right;
}

.entry-period {
  margin: var(--space-1) 0 var(--space-3);
  color: var(--muted);
  font-family: var(--font-mono);
  font-size: var(--size-small);
}

.entry-list { margin: 0; }

// ── Button ──────────────────────────────────────────────────────────────────

.button {
  display: inline-block;
  padding: var(--space-2) var(--space-3);
  border: var(--rule) solid var(--border-strong);
  background: var(--bg);
  color: var(--text);
  font-size: var(--size-small);
}

.button:hover,
.button:focus-visible {
  background: var(--text);
  color: var(--bg);
  text-decoration: none;
}

// ── Inline meta list ────────────────────────────────────────────────────────
// The location / website / profile line under a page title.

.meta-list {
  display: flex;
  flex-wrap: wrap;
  gap: var(--space-2) var(--space-4);
  margin: 0 0 var(--space-4);
  padding: 0;
  list-style: none;
  color: var(--muted);
}

.meta-list li + li { margin-top: 0; }
```

Then add these lines **inside** the existing `@media (max-width: 700px)` block:

```scss
  .entry-head { display: block; }
  .entry-meta { margin-top: var(--space-1); text-align: left; }
```

- [ ] **Step 2: Delete the legacy CV rules**

In `_sass/_pages.scss`, delete every rule whose selector starts with `.cv-` or
`.resume-`. That is the block running from `.resume-page` through
`.cv-entry li + li`, plus these three lines inside the
`@media screen and (max-width: 1024px)` block at the bottom:

```scss
  .cv-header h1 { font-size: 30px; }
  .cv-meta li { white-space: normal; }
  .cv-skill-list div, .cv-entry-heading { display: block; }
  .cv-entry-heading p { margin-top: 4px; text-align: left; }
```

Leave every `.projects-*` and `.project-*` rule in place — Task 6 removes those.

- [ ] **Step 3: Rewrite the CV page**

Replace the entire contents of `resume.md` with:

```html
---
layout: default
title: CV
description: Public CV for Mohammed Sazid Al Rashid.
---

{% assign resume = site.data.resume %}

<article>
  <h1>{{ resume.name }}</h1>

  <ul class="meta-list">
    <li>{{ resume.location }}</li>
    <li><a href="{{ resume.website.url }}">{{ resume.website.label }}</a></li>
    {% for profile in resume.profiles %}
    <li><a href="{{ profile.url }}">{{ profile.label }}</a></li>
    {% endfor %}
  </ul>

  <p><a class="button" href="{{ resume.pdf }}">Download PDF</a></p>

  <h2 class="section-label">Summary</h2>
  <div class="prose">
    <p>{{ resume.summary }}</p>
  </div>

  <h2 class="section-label">Technical proficiencies</h2>
  <div class="sheet-wrap" tabindex="0" role="region" aria-label="Technical proficiencies">
    <table class="sheet sheet--rows">
      <caption class="visually-hidden">Technical proficiencies by category</caption>
      <tbody>
        {% for skill in resume.skills %}
        <tr>
          <th scope="row">{{ skill.label }}</th>
          <td>{{ skill.details }}</td>
        </tr>
        {% endfor %}
      </tbody>
    </table>
  </div>

  <h2 class="section-label">Experience</h2>
  {% for job in resume.experience %}
  <section class="entry">
    <div class="entry-head">
      <h3>{{ job.role }}</h3>
      <p class="entry-meta">{{ job.company }} / {{ job.location }}</p>
    </div>
    <p class="entry-period">{{ job.period }}</p>
    <ul class="entry-list">
      {% for highlight in job.highlights %}
      <li>{{ highlight }}</li>
      {% endfor %}
    </ul>
  </section>
  {% endfor %}

  <h2 class="section-label">Projects</h2>
  {% for project in resume.projects %}
  <section class="entry">
    <div class="entry-head">
      <h3>{{ project.name }}</h3>
      <p class="entry-meta">{{ project.stack }}</p>
    </div>
    <p class="entry-period"><a href="{{ project.link.url }}">{{ project.link.label }}</a></p>
    <ul class="entry-list">
      {% for highlight in project.highlights %}
      <li>{{ highlight }}</li>
      {% endfor %}
    </ul>
  </section>
  {% endfor %}

  <h2 class="section-label">Open source contributions</h2>
  <div class="sheet-wrap" tabindex="0" role="region" aria-label="Open source contributions">
    <table class="sheet">
      <caption class="visually-hidden">Open source contributions</caption>
      <thead>
        <tr>
          <th scope="col">Project</th>
          <th scope="col">Contribution</th>
        </tr>
      </thead>
      <tbody>
        {% for item in resume.open_source %}
        <tr>
          <th scope="row">{{ item.project }}</th>
          <td><a href="{{ item.url }}">{{ item.label }}</a></td>
        </tr>
        {% endfor %}
      </tbody>
    </table>
  </div>

  <h2 class="section-label">Education</h2>
  {% for item in resume.education %}
  <section class="entry">
    <div class="entry-head">
      <h3>{{ item.institution }}</h3>
      <p class="entry-meta">{{ item.degree }}</p>
    </div>
    <p class="entry-period">{{ item.period }}</p>
    <ul class="entry-list">
      {% for highlight in item.highlights %}
      <li>{{ highlight }}</li>
      {% endfor %}
    </ul>
  </section>
  {% endfor %}
</article>
```

Two content notes. The open source list becomes a real table because it is
genuinely two columns of data. Section headings are shortened to sentence case
("Summary", not "Professional Summary") to match the calm label style; the
underlying data is untouched.

- [ ] **Step 4: Build and verify**

```bash
docker run --rm -v "$PWD":/site -w /site -e BUNDLE_PATH=/bundle -v jekyll-bundle:/bundle ruby:3.3 bash -c "bundle install --quiet && bundle exec jekyll build"
```

Then confirm the legacy classes are gone and the new ones are used:

```bash
grep -c 'cv-' _site/resume.html
grep -c 'class="entry"' _site/resume.html
grep -c 'class="button"' _site/resume.html
grep -rn 'cv-' _sass/
```

Expected: `0`, a number greater than `5`, `1`, and no output from the last
command.

Note: `grep -c` prints `0` and exits with status 1 when there are no matches.
That is the expected result here, not an error.

- [ ] **Step 5: Look at it**

Open http://localhost:4000/resume.html and confirm:
- No maroon anywhere.
- The skills table is bordered with the category in the left column.
- Job entries are separated by hairline rules, with the company right-aligned
  and the period in monospace underneath.
- "Download PDF" is a square-cornered bordered button that inverts on hover and
  when tabbed to.
- At a narrow window the company text moves below the role and aligns left.

- [ ] **Step 6: Commit**

```bash
git add _sass/_components.scss _sass/_pages.scss resume.md
git commit -m "Add entry and button components and rebuild CV page"
```

---

### Task 6: The Projects page

**Files:**
- Modify: `_sass/_components.scss` (append the showcase rules)
- Modify: `_sass/_pages.scss` (delete every remaining rule; the file ends empty)
- Modify: `_data/projects.yml` (two lines in the `summary` block)
- Modify: `projects.md` (replace entirely)

**Interfaces:**
- Consumes from Task 5: `.entry`, `.entry-head`, `.entry-meta`,
  `.entry-period`, `.entry-list`.
- Produces: the classes `.showcase`, `.showcase-item`, `.showcase-output`,
  `.link-row`.

- [ ] **Step 1: Append the showcase rules to `_sass/_components.scss`**

Insert before the `// ── Responsive` comment:

```scss
// ── Project showcase ────────────────────────────────────────────────────────

.showcase {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(220px, 1fr));
  gap: var(--space-3);
  margin-top: var(--space-3);
}

.showcase-item { min-width: 0; margin: 0; }

.showcase img {
  display: block;
  width: 100%;
  max-height: 240px;
  border: var(--rule) solid var(--border);
  background: var(--fill);
  object-fit: contain;
}

.showcase figcaption {
  margin-top: var(--space-1);
  color: var(--muted);
  font-size: var(--size-small);
}

.showcase-output { grid-column: 1 / -1; }
.showcase-output pre { margin: 0; }

.link-row {
  display: flex;
  flex-wrap: wrap;
  gap: var(--space-4);
  margin-top: var(--space-3);
}
```

- [ ] **Step 2: Empty the legacy stylesheet**

Delete every remaining rule in `_sass/_pages.scss`. After Task 5 removed the CV
rules, what is left is `.projects-page` through `.project-links a`, plus the
whole trailing `@media screen and (max-width: 1024px)` block. Remove all of it.

Replace the entire contents of the file with just:

```scss
// No page-specific styling is needed. Every page is built from the shared
// components in _components.scss. Add rules here only when a genuine one-off
// appears, and prefer extending a component instead.
```

- [ ] **Step 3: Fix the stale title in the projects data**

In `_data/projects.yml`, the `summary` block currently reads:

```yaml
summary:
  eyebrow: Projects
  title: Project Timeline
  intro: A timeline of notable systems projects, developer tools, Android apps, university work, and experiments.
```

Replace those four lines with:

```yaml
summary:
  title: Projects
  intro: Notable systems projects, developer tools, Android apps, university work, and experiments, newest first.
```

The timeline is gone, so "Project Timeline" and "A timeline of…" no longer
describe the page. The `eyebrow` field is deleted because nothing renders it any
more. No project entries are touched.

- [ ] **Step 4: Rewrite the Projects page**

Replace the entire contents of `projects.md` with:

```html
---
layout: default
title: Projects
description: Notable systems, tools, apps, and experiments by Mohammed Sazid Al Rashid.
---

{% assign projects = site.data.projects %}

<article>
  <h1>{{ projects.summary.title }}</h1>

  <div class="prose">
    <p>{{ projects.summary.intro }}</p>
  </div>

  {% for project in projects.projects %}
  <section class="entry">
    <div class="entry-head">
      <h2>{{ project.name }}</h2>
      <p class="entry-meta">{{ project.stack }}</p>
    </div>
    <p class="entry-period">{{ project.period }} &middot; {{ project.status }}</p>
    <p>{{ project.summary }}</p>
    <ul class="entry-list">
      {% for detail in project.details %}
      <li>{{ detail }}</li>
      {% endfor %}
    </ul>

    {% if project.showcase %}
    <div class="showcase">
      {% for item in project.showcase %}
      <figure class="showcase-item showcase-{{ item.type }}">
        {% if item.type == "output" %}
        <figcaption>{{ item.label }}</figcaption>
        <pre><code>{{ item.code | escape }}</code></pre>
        {% else %}
        {% assign media_href = item.href | default: item.src %}
        <a href="{{ media_href }}">
          <img src="{{ item.src }}" alt="{{ item.alt | default: item.label }}" loading="lazy">
        </a>
        <figcaption>{{ item.label }}</figcaption>
        {% endif %}
      </figure>
      {% endfor %}
    </div>
    {% endif %}

    <div class="link-row">
      {% for link in project.links %}
      <a href="{{ link.url }}">{{ link.label }}</a>
      {% endfor %}
    </div>
  </section>
  {% endfor %}
</article>
```

On alt text: the spec says decorative images take `alt=""`. Every image in
`_data/projects.yml` has a `label` or `alt` describing what it shows, so all of
them are treated as meaningful and keep a description. If, while doing this
task, you find an image whose label adds nothing beyond what the surrounding
text already says, set its `alt` to `""` in the data file and note it in the
commit message.

Two changes beyond the styling. Project names become `<h2>` rather than the
current `<h2>` inside an `<li>` inside an `<ol>`, so the page now has a flat,
correct heading outline. And `target="_blank" rel="noopener noreferrer"` is
dropped from the screenshot links: opening a new window without warning is
disorienting for screen reader and keyboard users, and the images are on this
same site.

- [ ] **Step 5: Build and verify**

```bash
docker run --rm -v "$PWD":/site -w /site -e BUNDLE_PATH=/bundle -v jekyll-bundle:/bundle ruby:3.3 bash -c "bundle install --quiet && bundle exec jekyll build"
```

Then confirm every legacy selector is gone from both the stylesheets and the
generated HTML:

```bash
grep -rn 'cv-\|project-timeline\|project-event\|project-item\|projects-page\|projects-header\|project-showcase\|project-links\|project-status\|project-summary\|project-details\|resume-page\|resume-embed' _sass/ _site/*.html
```

Expected: no output at all.

And confirm the page rendered:

```bash
grep -c 'class="entry"' _site/projects.html
grep -c '7a1f3d' _site/assets/css/styles.css
```

Expected: a number greater than `3`, and `0`.

- [ ] **Step 6: Look at it**

Open http://localhost:4000/projects.html and confirm:
- No vertical line and no square markers down the left side.
- Each project reads like a CV job entry: name left, stack right, period and
  status in monospace beneath.
- Screenshots are bordered with captions underneath and never overflow the page.

- [ ] **Step 7: Commit**

```bash
git add _sass/_components.scss _sass/_pages.scss _data/projects.yml projects.md
git commit -m "Rebuild Projects page with entry component and remove timeline"
```

---

### Task 7: Verification pass

Every requirement in the spec's section 8 is checked here, on the real site, at
real sizes. Anything that fails is fixed in this task.

**Files:**
- Modify: whichever files a failed check points at.

- [ ] **Step 1: Clean build from scratch**

```bash
rm -rf _site .jekyll-cache
docker run --rm -v "$PWD":/site -w /site -e BUNDLE_PATH=/bundle -v jekyll-bundle:/bundle ruby:3.3 bash -c "bundle install --quiet && bundle exec jekyll build"
```

Expected: `done in N seconds.` with no error or warning lines other than the
known `faraday-retry` and GitHub API authentication notices.

- [ ] **Step 2: Check every page rendered**

```bash
ls _site/*.html && ls _site/2*/ | head
```

Expected: `about.html`, `index.html`, `projects.html`, `resume.html`, plus the
year directories holding the 22 posts.

- [ ] **Step 3: Confirm the global constraints hold**

```bash
grep -rnE '#[0-9a-fA-F]{3,6}\b' _sass/_base.scss _sass/_components.scss _sass/_pages.scss
grep -rn 'outline: *none' _sass/ | grep -v 'main:focus'
grep -rn '7a1f3d' _sass/ _site/
grep -c '<th ' _site/index.html
grep -o '<th [^>]*>' _site/index.html | grep -vc 'scope='
for f in _site/index.html _site/about.html _site/resume.html; do
  echo "$f: $(grep -c 'class="sheet-wrap"' $f) wrappers, $(grep -c 'role="region"' $f) regions"
done
```

Expected, in order: no output, no output, no output, a number greater than `9`,
`0` (every `th` has a scope), and then three lines where the wrapper count and
the region count are equal on each file. Any wrapper without a region is a
table a keyboard user cannot scroll.

- [ ] **Step 4: Check each page at three widths**

With the dev server running, view each of these five pages at 375px, 700px, and
1280px viewport width:

- http://localhost:4000/
- http://localhost:4000/about.html
- http://localhost:4000/projects.html
- http://localhost:4000/resume.html
- http://localhost:4000/2023/09/18/zeuz-test-deploy-architecture.html

At **375px**, for each page confirm the document itself does not scroll
sideways. Check it precisely by running this in the browser console:

```js
document.documentElement.scrollWidth <= document.documentElement.clientWidth
```

Expected: `true` on every page. If it returns `false`, find the offending
element with:

```js
[...document.querySelectorAll('*')].filter(e => e.getBoundingClientRect().right > document.documentElement.clientWidth)
```

and wrap it in `.sheet-wrap` or give it `max-width: 100%`.

- [ ] **Step 5: Check both colour schemes**

View all five pages with the operating system set to light, then to dark.
Confirm on each: no dark text on a dark background, no light text on a light
background, table header rows are distinguishable from body rows, and every
link is clearly visible against the page.

- [ ] **Step 6: Check keyboard access**

On the homepage, with the mouse untouched:

1. Press Tab once. The "Skip to content" link appears at the top left.
2. Press Enter. Focus moves into the main content.
3. Keep pressing Tab. Every link shows a clear outline. Nothing is skipped and
   nothing is invisible while focused.
4. Each table receives focus as a single stop and shows an outline around the
   whole table. With it focused, the left and right arrow keys scroll it if it
   is wider than the screen. Check this at 375px width, where the tables do
   overflow.
5. Tab to "Download PDF" on the CV page. It inverts, exactly as on hover.

- [ ] **Step 7: Confirm the contrast figures**

Using any contrast checker, confirm each pair reaches at least 4.5:1:

| Pair | Light | Dark |
|---|---|---|
| Body text on background | `#000000` on `#ffffff` | `#e8e8e8` on `#111111` |
| Muted text on background | `#555555` on `#ffffff` | `#a0a0a0` on `#111111` |
| Link on background | `#0045e4` on `#ffffff` | `#7aa7ff` on `#111111` |
| Body text on table header fill | `#000000` on `#efefef` | `#e8e8e8` on `#1c1c1c` |

If any pair falls short, adjust only the value in `_sass/_tokens.scss`. Because
nothing else in the project holds a colour, that is the only edit needed.

- [ ] **Step 8: Confirm content was not lost**

```bash
git diff --stat HEAD~6 -- _posts/ _data/resume.yml _data/navigation.yml _config.yml Gemfile
```

Expected: no output. These files must be byte-identical to where the plan
started.

- [ ] **Step 9: Commit any fixes**

If steps 4 to 7 required changes:

```bash
git add -A
git commit -m "Fix responsive and contrast issues found in verification"
```

If nothing needed fixing, skip this step — there is nothing to commit.

- [ ] **Step 10: Stop the dev server**

```bash
docker rm -f jekyll-dev
```

---

## Done when

- All seven tasks are committed.
- `_sass/_pages.scss` contains only a comment.
- No `.cv-*`, `.project-*`, `.projects-*`, or `.resume-*` selector exists
  anywhere in the project.
- The maroon `#7a1f3d` appears nowhere.
- All five page types render correctly in light and dark mode at 375px, 700px,
  and 1280px.
- `_posts/`, `_data/resume.yml`, `_data/navigation.yml`, `_config.yml`, and
  `Gemfile` are unchanged.
