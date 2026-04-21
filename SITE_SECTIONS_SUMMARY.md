# Site Sections Summary

This document summarizes every section, page, and major component of the portfolio site.

---

## Homepage (`index.html` → `home.js`)

The homepage is a single-page layout with four main sections, a sticky navigation bar, and a footer.

### 1. Top Bar / Header

- **File**: `index.html:21-41`
- Sticky header with logo (`Liu_WP_Logo_4x.png`), site name "Yujia Max Liu", and a mobile hamburger menu toggle.
- On desktop it sits at the top with backdrop blur; on mobile it triggers the sidebar drawer.

### 2. Sidebar Navigation

- **File**: `index.html:43-80`
- Fixed left panel that collapses to icon-only (78 px) by default, expands on hover/focus to show labels (230 px).
- Links: Tags (`#tags`), About (`#about`), Recommendation (`#featured-recommendations`), Works (`#works`).
- Bottom share block: LinkedIn, X/Twitter, Facebook, YouTube icons. Collapsed state shows a "↗" icon; expanded state shows the full icon row.
- On mobile (≤980 px) the sidebar becomes a slide-in drawer with a dark overlay.

### 3. Tag System (`#tags`)

- **File**: `index.html:84-86`, `home.js:1-131`
- Sticky pill-button row that filters the works gallery by tag.
- Definitions loaded from `data/taxonomy.json` (currently: All, Farcry6, Division2, Short film, Tool, Template).
- Clicking a tag re-renders the gallery and smooth-scrolls to it.

### 4. About Section (`#about`)

- **File**: `index.html:89-97`
- Portrait image (`YujiaMaxLiu_Web.jpg`) and one-paragraph bio:
  > "I am Yujia Max Liu, a visual creator focused on stylized and cinematic 3D production. My work spans environment art, lighting, shaders, and production scripting for real-time pipelines."

### 5. Featured Recommendations (`#featured-recommendations`)

- **File**: `index.html:100-120`, `home.js:194-489`
- Carousel-style panel showcasing four featured projects with prev/next navigation and a dot indicator.
- Each card has a main image, four thumbnails (hover to preview), a title, and a description. Clicking navigates to the project page.
- Image paths resolved from `Resources/Featured Recommendations/pages/fr-<slug>/` with extension probing (png → jpg → svg).
- Runtime validation ensures folder naming matches the `fr-<slug>` convention.
- **Featured entries** (defined in `home.js:209-251`):
  1. **Division 2** (`fr-division-2`, → `division-2.html`)
  2. **Farcry 6** (`fr-farcry-6`, → `farcry-6.html`)
  3. **D-Walker VS Sahelanthropus** (`fr-d-walker-vs-sahelanthropus`, → `d-walker-vs-sahelanthropus.html`)
  4. **Raiden VS Gekko** (`fr-raiden-vs-gekko`, → `raiden-vs-gekko.html`)

### 6. Selected Works Gallery (`#works`)

- **File**: `index.html:122-127`, `home.js:78-131`
- Two-column card grid (single column ≤640 px) populated from `data/taxonomy.json`.
- Each card shows a project image, title, and dot-separated tag labels.
- **Current projects** (from taxonomy):
  1. **Farcry 6** — Procedural generation (tag: Farcry6)
  2. **Division 2** — Tools (tags: Division2, Tool)
  3. **D-Walker VS Sahelanthropus** — Short film
  4. **Raiden VS Gekko** — Short film
  5. **Project Instance Test** — Template placeholder

### 7. Footer

- **File**: `index.html:130-168`
- Repeats the four social icon links (LinkedIn, X, Facebook, YouTube).
- Dynamic copyright year via JS (`new Date().getFullYear()`).

---

## Project Instance Pages (4 pages)

Each project is a minimal HTML shell (`<slug>.html`) that loads `project-instance-loader.js`. The loader fetches `template-content.html` and the project's JSON config at runtime, then hydrates the DOM.

### Page Shells

| Slug | File | Config JSON |
|---|---|---|
| `d-walker-vs-sahelanthropus` | `d-walker-vs-sahelanthropus.html` | `Resources/Project Instances/config/d-walker-vs-sahelanthropus.json` |
| `division2-tools` | `division-2.html` | `Resources/Project Instances/config/division2-tools.json` |
| `farcry6-procedural-generation` | `farcry-6.html` | `Resources/Project Instances/config/farcry6-procedural-generation.json` |
| `raiden-vs-gekko` | `raiden-vs-gekko.html` | `Resources/Project Instances/config/raiden-vs-gekko.json` |

### Shared Template (`template-content.html`)

- Loaded via `fetch` + `DOMParser` at runtime.
- Contains: top-bar, sidebar (Overview, Project Details, Related Projects, Back to Home), and project layout.
- **autohydrate disabled** on project instance pages (`data-project-template-autohydrate="false"`) so JSON-driven paths take precedence.

### Loader (`project-instance-loader.js`)

- Reads `data-project-slug` from `<body>`.
- Fetches config from `Resources/Project Instances/config/<slug>.json`.
- Populates: kicker, title, description, cover image, gallery (thumb_01–04, main), metadata (tools, languages, time, role), tags (resolved from taxonomy), project details blocks (`h1`/`h2`/`h3`/`p`/`image`/`video`), and related works.
- Mobile overview stacking: on ≤980 px the gallery moves between info columns.

---

## Key Files

| File | Purpose |
|---|---|
| `index.html` | Homepage entry point |
| `home.js` | Homepage JS: tag system, featured carousel, project gallery, sidebar, template hydration |
| `project-instance-loader.js` | Project page JS: config loading, template hydration, gallery, sidebar toggle, mobile layout |
| `template-content.html` | Shared project-page markup (fetched at runtime) |
| `style.css` | All styles — design tokens in `:root`, layout, responsive breakpoints (980/860/760/640 px) |
| `data/taxonomy.json` | Tag definitions + project index (used by both homepage and project pages) |

---

## Styling Architecture

- Single `style.css` (1152 lines) with CSS custom properties (`:root` lines 1-18).
- **Breakpoints**: 980 px (sidebar → drawer), 860 px (recommendation card → single column), 760 px (tag bar scroll, carousel reflow), 640 px (gallery → single column).
- Design tokens: dark theme (`--bg: #0f111a`, `--accent: #78a8ff`, `--accent-2: #a28bff`).

---

## Data Flow

```
Homepage:
  index.html → home.js → data/taxonomy.json (tags + projects)
                           Resources/Featured Recommendations/pages/fr-*/  (carousel images)

Project Instance:
  <slug>.html → project-instance-loader.js → data/taxonomy.json (tags + related projects)
                                              template-content.html (shared markup)
                                              Resources/Project Instances/config/<slug>.json (project content)
                                              Resources/Project Instances/pages/<slug>*/  (project images)
```