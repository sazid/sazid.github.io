# Site redesign: technical datasheet style

Date: 2026-08-02
Status: approved, ready for implementation planning

## Goal

Redesign sazid.github.io to read like a technical reference sheet rather than a
blog. The reference point is neil.computer, which the site owner found highly
accessible. We take that site's principles, not its exact appearance.

The four qualities we are copying:

1. High contrast, plain text, no decoration.
2. Content presented as bordered tables with grey header rows.
3. Small, calm section labels instead of large display headings.
4. A single visual system used consistently on every page.

## Decisions already made

| Decision | Choice |
|---|---|
| Body typeface | System sans-serif stack. Monospace only for code and data. |
| Font source | System stack. No new webfont downloads. |
| Fidelity to neil.computer | Principles only, not a clone. |
| Homepage | Adds an identity/contact datasheet above the post list. |
| Dark mode | Yes, follows the operating system setting. No toggle, no JavaScript. |
| Accent colour | Blue links only. The existing maroon is removed. |
| CV and Projects pages | Rebuilt in the same system as everything else. |
| About page | Kept as a separate page with the longer bio. |
| Post list | One table, all posts, newest first. |
| CSS structure | Split into partials with shared component classes. |
| RSS feed | Out of scope. Not added. |

## 1. Colours

Two palettes. Every foreground/background pair listed here meets WCAG AA
(4.5:1). Most exceed AAA (7:1).

| Role | Light | Dark |
|---|---|---|
| Page background | `#ffffff` | `#111111` |
| Body text | `#000000` | `#e8e8e8` |
| Muted text (dates, meta) | `#555555` | `#a0a0a0` |
| Hairline border | `#cfcfcf` | `#333333` |
| Strong border (nav rule, sheet outline) | `#000000` | `#e8e8e8` |
| Table header fill | `#efefef` | `#1c1c1c` |
| Link | `#0045e4` | `#7aa7ff` |
| Code background | `#f0f0f0` | `#1a1a1a` |

Dark mode uses `#111111` rather than pure black. Pure black behind light text
causes visible glare for many readers.

The maroon `#7a1f3d` currently used on CV and Projects section labels is removed
from the whole site.

Dark mode is delivered by a single `@media (prefers-color-scheme: dark)` block
that reassigns CSS custom properties. No JavaScript, no toggle, no stored
preference.

## 2. Typography

Body stack:

```
-apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, "Helvetica Neue", Arial, sans-serif
```

Monospace stack (JetBrains Mono woff2 files stay where they are):

```
"JetBrains Mono", ui-monospace, SFMono-Regular, Menlo, monospace
```

Monospace is used only for: code blocks, inline code, dates, and numeric table
data. Not for prose.

Base size is **16px**, not the 15px neil.computer uses. 16px is the browser
default and reducing below it is the most common accessibility fault on personal
sites. All sizes are declared in `rem` so browser zoom and user font-size
settings work correctly.

| Element | Size | Weight | Line height |
|---|---|---|---|
| Body | 1rem (16px) | 400 | 1.55 |
| Page title (h1) | 1.75rem (28px) | 700 | 1.2 |
| Section label (h2) | 1rem (16px) | 700 | 1.3 |
| Entry title (h3) | 1.0625rem (17px) | 700 | 1.3 |
| Small meta, captions | 0.875rem (14px) | 400 | 1.4 |

Section labels are plain sentence case. No uppercase transform: capitals reduce
word-shape cues and slow reading.

## 3. Layout

The page shell is 900px wide with 16px side padding, centred. This is wider than
the current ~640px, because tables need the room.

Post articles are constrained to a 68-character measure **inside** that shell.
A 900px paragraph is roughly 110 characters per line, which is tiring to read.
Tables run full shell width; prose does not.

Vertical rhythm uses a small spacing scale (4, 8, 12, 16, 24, 40px) held in
`_tokens.scss`.

### Responsive behaviour

Single breakpoint at 700px. Below it:

- The navigation bar stacks: label above links.
- The two homepage sheets stack into one column.
- Entry headings stop being a two-column grid; meta moves below the title and
  aligns left.
- Any sheet wider than the viewport scrolls horizontally inside its own
  container. The page body never scrolls sideways.

## 4. Component set

Defined once in `_sass/_components.scss` and reused by every page.

### Nav bar

Bold "Navigation" label on the left, links on the right, a strong rule
underneath. The current page is rendered bold and **not** as a link, and carries
`aria-current="page"`. Current state is never signalled by colour alone.

### Sheet

The bordered table. 1px collapsed borders, grey header row, cell padding
6px 10px, left aligned, no rounded corners, no shadow, no hover effect.

Two variants:

- **Column headers** — a header row of `<th scope="col">`. Used for the post
  list and the About link list.
- **Row headers** — the first cell of each row is `<th scope="row">`. Used for
  the identity, contact, and skills sheets, which are name/value pairs.

### Section label

Bold text at body size with a hairline rule beneath. 40px of space above.

### Entry

A bordered block for content that is **not** tabular. Structure: a title on the
left with meta right-aligned on the same baseline, a muted period line beneath,
then prose and/or a bullet list. Separated by hairline rules.

Used by CV experience entries, CV education entries, and every project.

### Button

A small square-cornered bordered link. Inverts (background and text swap) on
hover and on focus. Used for "Download PDF" and project links.

### Footer

A hairline rule and a copyright line. Appears on every page. New to the site.

## 5. Markup and accessibility

The visual system is tables, but the markup follows the content, not the look.

**Real `<table>` elements** for the post list, the identity sheet, the contact
sheet, the skills sheet, and the About link list. This content genuinely has
rows and columns and screen readers announce it usefully. Every `<th>` carries a
`scope` attribute. Every table has a `<caption>`; where the adjacent section
label already names the table, the caption is visually hidden but present for
assistive technology.

**Not tables** for CV jobs, CV education, or projects. Those use the entry
component: a real heading and a real `<ul>`, styled to match the sheets.

Additional requirements:

- A "skip to content" link as the first focusable element, visually hidden until
  focused.
- A visible focus indicator on every focusable element: 2px outline, 2px offset,
  using the body text colour. Nothing in the stylesheet sets `outline: none`.
- A `<main>` landmark wrapping page content. The site currently has none.
- Sheets that overflow are wrapped in a container with `tabindex="0"` and
  `role="region"` plus an accessible name, so keyboard users can scroll them.
- Decorative images on the Projects page get `alt=""`. Meaningful images keep
  descriptive alt text.
- Links are distinguished by colour and by underline on hover and focus, never
  by colour alone in body prose.
- No animation anywhere, so `prefers-reduced-motion` needs no special handling.

## 6. Page specifications

### Home (`index.html`)

1. `<h1>` with the owner's name.
2. Section label "About", followed by two sheets side by side in a CSS grid:
   - **Identity** — role, employer, location, education.
   - **Contact** — email, GitHub, LinkedIn, Stack Overflow.
3. Section label "Writing", followed by one sheet listing all posts, newest
   first. Columns: Date, Title. The date column uses monospace and a fixed
   narrow width.

The two sheets stack vertically below 700px.

### About (`about.md`)

Keeps the existing prose bio. The bulleted link list becomes a sheet with
Platform and Link columns. The email address stays as plain text in prose.

### CV (`resume.md`)

1. `<h1>` name, a meta line (location, website, profile links), and a
   "Download PDF" button.
2. "Summary" — prose.
3. "Technical proficiencies" — a row-header sheet, category in the first column,
   detail in the second.
4. "Experience" — entry blocks. Role and company on the left, location
   right-aligned, period in the muted line, highlights as a bullet list.
5. "Projects", "Open source", and "Education" — the remaining sections in
   `_data/resume.yml` — as entry blocks, using the same component.

The CV's own `projects` and `open_source` lists stay separate from the standalone
Projects page. They are different, shorter summaries and are not merged.

All `.cv-*` selectors are retired.

### Projects (`projects.md`)

The vertical timeline, its connecting line, and its square markers are removed.
Each project becomes an entry block: name on the left, stack right-aligned,
period and status in the muted line, then summary, details list, screenshots,
and links.

Screenshots keep their `<figure>`/`<figcaption>` structure, bordered, square
corners, constrained height.

All `.project-*` timeline selectors are retired.

### Post (`_layouts/post.html`)

Title, date in muted monospace, then the article body at the 68-character
measure. Code blocks keep JetBrains Mono, gain a hairline border, and scroll
horizontally when needed.

### Default layout (`_layouts/default.html`)

Adds: the skip link, a `<main>` landmark, and the footer include.

## 7. Files

New:

```
_sass/_tokens.scss       colours (both modes), type scale, spacing, borders
_sass/_base.scss         body, headings, links, lists, code, focus, skip link
_sass/_components.scss   nav, sheet, section label, entry, button, footer
_sass/_pages.scss        genuine page-specific one-offs only
_includes/footer.html
_data/profile.yml        facts shown in the homepage identity and contact sheets
```

Rewritten:

```
_sass/main.scss          imports only
_layouts/default.html
_layouts/post.html
_includes/navigation.html
index.html
about.md
resume.md
projects.md
```

Unchanged:

```
_posts/*                 all 22 post files
_data/resume.yml
_data/projects.yml
_data/navigation.yml
_config.yml
Gemfile
assets/fonts/*
```

`_data/profile.yml` exists so the homepage facts live in one place rather than
being hard-coded in `index.html`. It duplicates a small amount of
`_data/resume.yml` deliberately: the homepage summary and the full CV serve
different purposes and should be editable independently.

## 8. Verification

Before the work is considered done:

1. `bundle exec jekyll build` completes without errors or warnings.
2. Every page renders correctly in light mode and dark mode.
3. Every page renders correctly at 375px, 700px, and 1280px viewport widths.
4. No page scrolls horizontally at 375px.
5. Every interactive element shows a visible focus ring when tabbed to.
6. The skip link appears on first Tab press and moves focus to `<main>`.
7. All colour pairs in section 1 are confirmed at or above 4.5:1 contrast.
8. No `.cv-*` or `.project-*` timeline selectors remain in the stylesheets.

## Out of scope

- RSS feed and `jekyll-feed`.
- Any change to post content.
- A dark mode toggle button.
- Post categories or tags.
- Self-hosted sans-serif webfonts.
