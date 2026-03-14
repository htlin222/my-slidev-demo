# Slidev Theme Extraction Plan

> **For Claude:** REQUIRED SUB-SKILL: Use superpowers:executing-plans to implement this plan task-by-task.

**Goal:** Extract the current presentation's reusable assets (layouts, components, styles, global layers) into a standalone Slidev theme package at `packages/slidev-theme-academic-teal`, while keeping `slides.md` as a demo/dev preview that consumes the theme via `theme: ./packages/slidev-theme-academic-teal`.

**Architecture:** Slidev themes are npm packages with a conventional directory structure (`layouts/`, `components/`, `styles/`, `setup/`). The theme's `package.json` must have `"slidev-theme"` in keywords and a `"slidev"` metadata field. Global layers (`global-top.vue`, `global-bottom.vue`) go in the theme root. The existing project becomes a monorepo-like structure where `slides.md` references the local theme for development.

**Tech Stack:** Slidev v52+, Vue 3, Chart.js + vue-chartjs, UnoCSS, Source Sans Pro

---

### Task 1: Scaffold the theme package directory

**Files:**
- Create: `packages/slidev-theme-academic-teal/package.json`

**Step 1: Create directory structure**

```bash
mkdir -p packages/slidev-theme-academic-teal/{layouts,components,styles,setup}
```

**Step 2: Create the theme package.json**

Create `packages/slidev-theme-academic-teal/package.json`:

```json
{
  "name": "slidev-theme-academic-teal",
  "version": "0.1.0",
  "description": "A teal-accented academic theme for Slidev with chapter navigation, progress bar, breadcrumbs, and vue-chartjs chart components",
  "author": "",
  "license": "MIT",
  "keywords": [
    "slidev-theme",
    "slidev"
  ],
  "engines": {
    "slidev": ">=0.52.0"
  },
  "slidev": {
    "colorSchema": "light",
    "defaults": {
      "transition": "fade"
    }
  },
  "dependencies": {
    "chart.js": "^4.5.1",
    "vue-chartjs": "^5.3.3"
  }
}
```

**Step 3: Verify directory exists**

```bash
ls packages/slidev-theme-academic-teal/
```

Expected: `components/ layouts/ setup/ styles/ package.json`

---

### Task 2: Move layouts into the theme

**Files:**
- Move: `layouts/my-cover.vue` → `packages/slidev-theme-academic-teal/layouts/my-cover.vue`
- Move: `layouts/chapter.vue` → `packages/slidev-theme-academic-teal/layouts/chapter.vue`
- Remove: `layouts/` (empty after move)

**Step 1: Copy layout files**

```bash
cp layouts/my-cover.vue packages/slidev-theme-academic-teal/layouts/
cp layouts/chapter.vue packages/slidev-theme-academic-teal/layouts/
```

**Step 2: Remove originals**

```bash
rip layouts/my-cover.vue layouts/chapter.vue
rmdir layouts
```

**Step 3: Verify**

```bash
ls packages/slidev-theme-academic-teal/layouts/
```

Expected: `chapter.vue  my-cover.vue`

---

### Task 3: Move components into the theme

**Files:**
- Move: `components/Chart*.vue` → `packages/slidev-theme-academic-teal/components/`
- Remove: `components/` (empty after move)

**Step 1: Copy component files**

```bash
cp components/ChartBar.vue components/ChartLine.vue components/ChartPie.vue \
   components/ChartDoughnut.vue components/ChartRadar.vue \
   packages/slidev-theme-academic-teal/components/
```

**Step 2: Remove originals**

```bash
rip components/ChartBar.vue components/ChartLine.vue components/ChartPie.vue \
    components/ChartDoughnut.vue components/ChartRadar.vue
rmdir components
```

**Step 3: Verify**

```bash
ls packages/slidev-theme-academic-teal/components/
```

Expected: `ChartBar.vue ChartDoughnut.vue ChartLine.vue ChartPie.vue ChartRadar.vue`

---

### Task 4: Move styles into the theme

**Files:**
- Move: `styles/index.css` → `packages/slidev-theme-academic-teal/styles/index.css`
- Remove: `styles/` (empty after move)

**Step 1: Copy styles**

```bash
cp styles/index.css packages/slidev-theme-academic-teal/styles/
```

**Step 2: Remove original**

```bash
rip styles/index.css
rmdir styles
```

**Step 3: Verify**

```bash
cat packages/slidev-theme-academic-teal/styles/index.css | head -5
```

Expected: `@import url(...)` and `:root { --slidev-theme-primary: #3d6869; }`

---

### Task 5: Move global layers into the theme

**Files:**
- Move: `global-top.vue` → `packages/slidev-theme-academic-teal/global-top.vue`
- Move: `global-bottom.vue` → `packages/slidev-theme-academic-teal/global-bottom.vue`

**Step 1: Copy global layers**

```bash
cp global-top.vue packages/slidev-theme-academic-teal/
cp global-bottom.vue packages/slidev-theme-academic-teal/
```

**Step 2: Remove originals**

```bash
rip global-top.vue global-bottom.vue
```

**Step 3: Verify theme structure**

```bash
find packages/slidev-theme-academic-teal -type f | sort
```

Expected:
```
packages/slidev-theme-academic-teal/components/ChartBar.vue
packages/slidev-theme-academic-teal/components/ChartDoughnut.vue
packages/slidev-theme-academic-teal/components/ChartLine.vue
packages/slidev-theme-academic-teal/components/ChartPie.vue
packages/slidev-theme-academic-teal/components/ChartRadar.vue
packages/slidev-theme-academic-teal/global-bottom.vue
packages/slidev-theme-academic-teal/global-top.vue
packages/slidev-theme-academic-teal/layouts/chapter.vue
packages/slidev-theme-academic-teal/layouts/my-cover.vue
packages/slidev-theme-academic-teal/package.json
packages/slidev-theme-academic-teal/styles/index.css
```

---

### Task 6: Update slides.md to consume the local theme

**Files:**
- Modify: `slides.md` (headmatter only)

**Step 1: Change theme reference**

In `slides.md`, update the headmatter from:
```yaml
theme: default
```
to:
```yaml
theme: ./packages/slidev-theme-academic-teal
```

**Step 2: Remove chart.js and vue-chartjs from root dependencies**

These now live in the theme's `package.json`. Update root `package.json`:

Remove `chart.js` and `vue-chartjs` from `dependencies`. Also remove `@slidev/theme-default` from `devDependencies` since the custom theme replaces it.

**Step 3: Install dependencies**

```bash
pnpm install
```

**Step 4: Verify dev server**

```bash
pnpm dev
```

Expected: Slides render with teal theme, chapter nav, charts, progress bar — same as before.

---

### Task 7: Commit

**Step 1: Stage all changes**

```bash
git add packages/slidev-theme-academic-teal/ slides.md package.json pnpm-lock.yaml
git add -u  # stages deletions of moved files
```

**Step 2: Commit**

```bash
git commit -m "refactor: extract reusable assets into slidev-theme-academic-teal package"
```

---

## Theme Directory (Final Structure)

```
packages/slidev-theme-academic-teal/
├── package.json              # Theme metadata + chart.js deps
├── global-top.vue            # Chapter nav bar + progress bar
├── global-bottom.vue         # H2 breadcrumb overlay
├── layouts/
│   ├── my-cover.vue          # Cover slide layout
│   └── chapter.vue           # Chapter divider layout
├── components/
│   ├── ChartBar.vue          # Bar chart (stacked support)
│   ├── ChartLine.vue         # Line chart (fill support)
│   ├── ChartPie.vue          # Pie chart
│   ├── ChartDoughnut.vue     # Doughnut chart
│   └── ChartRadar.vue        # Radar chart
└── styles/
    └── index.css             # Source Sans Pro, teal palette, transitions
```

## Publishing (Future)

When ready to publish to npm:

```bash
cd packages/slidev-theme-academic-teal
npm publish
```

Then any Slidev project can use it:

```yaml
---
theme: academic-teal
---
```

(Slidev auto-resolves `slidev-theme-` prefix.)
