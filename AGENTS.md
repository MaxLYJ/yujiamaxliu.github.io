# AGENTS.md

## Repository Type
Static HTML/CSS/JS portfolio site. **No build system, tests, or linting.** Edit files directly. Deployed on GitHub Pages.

## Dual JS Architecture (easy to miss)
- **Homepage** (`index.html` + `template-content.html`) loads **`home.js`**
- **Project instance pages** (`<slug>.html`) load **`project-instance-loader.js`** — NOT `home.js`
- `home.js` has template-gallery hydration code for `template-content.html`, but project instance pages set `data-project-template-autohydrate="false"` to prevent it from overriding JSON-driven image paths

## Entry Points
- **Homepage**: `index.html` → `home.js` (tag system, featured recommendations, gallery from `data/taxonomy.json`)
- **Project Instance Pages**: `<slug>.html` → `project-instance-loader.js` → fetches `template-content.html` at runtime → populates from `Resources/Project Instances/config/<slug>.json`
- **Template**: `template-content.html` (shared markup, loaded via `fetch` + `DOMParser` — must be same-origin)

## Data Sources
- **`data/taxonomy.json`**: tags + project index. Read by both `home.js` (homepage gallery) and `project-instance-loader.js` (tag labels, related projects)
- **`Resources/Project Instances/config/<slug>.json`**: per-project content (title, kicker, description, tools, images, `projectDetails.blocks`). **Filename must match the slug exactly** — loader resolves path by convention (`Resources/Project Instances/config/<slug>.json`)

## Adding a New Project Instance (3 files to touch)
1. Add entry to `data/taxonomy.json` `projects` array (`slug`, `url`, `title`, `image`, `tagIds`)
2. Create `<slug>.html` shell with `data-project-slug="<slug>"` and `<div data-project-instance-root></div>` (copy `raiden-vs-gekko.html` as template)
3. Create `Resources/Project Instances/config/<slug>.json` — filename must match the slug exactly (copy `division2-tools.json` as template)

## Featured Recommendations
Hardcoded `featuredPages` array in `home.js` (~line 209). To add:
1. Create folder: `Resources/Featured Recommendations/pages/fr-<slug>/`
2. Add images: `fr_<slug>__main.<ext>`, `fr_<slug>__thumb_01..04.<ext>`
3. Add entry to `featuredPages` array in `home.js`

**Naming validation** (enforced at runtime):
- Folder must match `fr-<slug>` exactly
- Folder name must end with the slug value
- Image extension priority: png → jpg → svg (probed at runtime via `new Image()`)

## Project Config JSON Schema
Required fields: `title`, `kicker`, `description`, `tools`, `languages`, `time`, `role`, `images` (object with `cover`, `thumb_01`..`thumb_04`)
Optional: `projectDetails.blocks` — array of `{type, text/src/url, alt/title}`. Block types: `h1`, `h2`, `h3`, `p`, `image`, `video`
Legacy: `projectDetails.initiative/pipeline/result/placeholderImage/placeholderVideo` still supported as fallback

## Styling
Single file: `style.css`. Design tokens as CSS custom properties in `:root` (lines 1-18). Mobile breakpoint: `980px`.

## Local Development
Serve from repo root with any static server (e.g., `npx serve` or VS Code Live Server). No special env or config needed.

## Design Docs (reference only)
- `PROJECT_INSTANCE_PAGE_BEST_PRACTICES.md` — project page conventions
- `FEATURED_RECOMMENDATIONS_PLAN.md` — recommendation section spec
- `TAG_SYSTEM_PLAN.md` — tag taxonomy design
