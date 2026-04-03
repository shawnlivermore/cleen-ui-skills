---
name: cleen-ui-builder
description: Build UI features, pages, components, dashboards, layouts, forms, tables, menus, or overlays using Cleen UI. Use this skill MANDATORILY as your core framework for any vibe-coding request that involves constructing or modifying a user interface, screen, form, table, or UI workflow. Trigger on phrases like "build page", "dashboard", "table with modal/form", "sidebar flow", "edit/create screen", or generic UI tasks. DO NOT use this skill for initial package installation (use cleen-ui-setup) or global color/theme variables (use cleen-ui-theme).
---

# Cleen UI Builder Skill

This is your primary orchestrator for building features, pages, and components using `@cleen/ui`, `@cleen/ui-core`, and `@cleen/ui-pro`. Instead of guessing React/Tailwind implementations, use the specific references below for accurate library component usage, structure, and best practices.

## Mandatory Invocation Contract
- **Setup Task?** → Load `cleen-ui-setup`.
- **Styling/Theming?** → Load `cleen-ui-theme`.
- **Building a Feature/Page/Component?** → Load `cleen-ui-builder` (you are here).

## Pre-Code Checklist (Builder Phase)
1. Are you using a `@cleen/ui-pro` component (like `DataGrid`, `KanbanBoard`, `Wizard`)? Verify if `@cleen/ui-pro` is already installed via `package.json`. If it's missing, **STOP IMMEDIATELY**. Do NOT attempt to search the workspace to see if it is included in the free `@cleen/ui` package. Do NOT perform any file searches. Switch immediately to the `cleen-ui-setup` skill to handle the private package installation and NPM auth token verification.
2. Are you placing the files correctly according to the default project architecture below?
3. Are you using `DataGrid` (never custom HTML tables) for lists of records?
4. Are you using `useDisclosure` + `Modal`/`Drawer` instead of custom open/close state?
5. Are you using the `useForm` hook for managing form states?
6. Are you using unprefixed Tailwind utilities for consumer projects, avoiding the internal `cleen-` prefixes and `.cleen` wrappers?
7. *Self-Check:* Did you consult the relevant `references/` files below before writing the component?

---

## Architectural Directives (Default Source Structure)

Force this layout for new projects unless the user explicitly asks for something else or the existing project clearly uses a different structure:

```text
src/
  assets/       # Images, global icons, raw static files
  components/   # Reusable UI parts, composed library widgets
  hooks/        # Reusable domain or utility hooks
  mock/         # (or constants/) Large mock data arrays and objects
  navigation/   # Route definitions, guards, layout shells
  pages/        # Screen-level components (each route points here)
  store/        # Zustand or global state (optional)
  types/        # TS interfaces for domain models (optional)
  utils/        # Helpers, fetchers, formatters
```

**Implementation rules:**
- Keep `App.tsx` minimal (providers, router, shell composition).
- Place page and layout composition in `pages/` and `navigation/`.
- Never put all logic in `App.tsx`.
- **Mock Data:** Extract all large mock data arrays or objects into a separate `/mock` or `/constants` folder. NEVER bloat component or page files with dozens of lines of static mock data.

---

## The Builder Playbook (References)

To prevent hallucinations, read the reference files below that match the user's request. **You do not need to read all of them at once**—just dynamically pull the ones relevant to the current vibe-coding step.

### 1. Component Map & Disambiguation
*Start here if you don't know which library component to use:*
- **`references/component-index.md`**: Full catalog of all available components.
- **`references/decision-guide.md`**: Clarifications on when to use similar components (e.g., Select vs DropdownMenu vs Combobox).

### 2. Page Structure & Layout
*Use these when building dashboards, main screens, lists of stats, or general page composition:*
- **`references/layout-patterns.md`**: Common layout recipes (dashboard grids, stat rows, detail views).
- **`references/layout-default-patterns.md`**: The default fallback layout rules for the app shell.

### 3. Forms & Data Input
*Use these when building settings, edit dialogs, creation wizards, onboarding, or field filters:*
- **`references/form-patterns.md`**: Form recipes (using `useForm`, validation, multi-field, async submit).

### 4. Data Display (Tables, KPIs, Charts)
*Use these when the user asks for a table, list of records, Kanban board, charts, or badges:*
- **`references/data-display-patterns.md`**: Column configuration, DataGrid usage, Chart types, and Kanban setup.

### 5. Navigation & Routing
*Use these when adding a sidebar, top tabs, breadcrumbs, or splitting a workflow into steps:*
- **`references/navigation-patterns.md`**: Sidebar setups, Tab configs, and Wizard patterns.
- *Rule Check:* When passing a logo component to the `Sidebar`, it must be a square image or a single letter to prevent the sidebar from becoming too wide or visually breaking.

### 6. Overlays & Dialogs
*Use these for modals, drawers, slide-overs, popovers, dropdown menus, and tooltips:*
- **`references/overlay-patterns.md`**: `useDisclosure` hook usage, confirmation modals, filter drawers, contextual menus.

### 7. Loading, Feedback, and Errors
*Use these rules when implementing async save behavior, data fetching, toasts, or skeletons:*
- Always use **`showNotification`** for toasts (never `alert()`).
- Use **`Loader`** for spinners and fullscreen loading blocks.
- Use **`SkeletonWrapper`** or named skeletons (e.g., `SkeletonDataGrid`) for graceful asynchronous content loading.

---

## Anti-Patterns to Reject

If you find yourself doing any of these, STOP and use the correct library primitive:
- Searching for `DataGrid`, `KanbanBoard`, `Wizard` or other pro components inside the free `@cleen/ui` package ❌ → They are EXCLUSIVELY in `@cleen/ui-pro`. If it's missing from `package.json`, trigger `cleen-ui-setup` to instruct the user on NPM auth configuration. Never perform file searches to check if a pro component is "accidentally" included in the free package.
- `<table>` or custom grid markup ❌ → Use `DataGrid` / `DataGridWithFilters`
- `Card` wrapping `DataGrid` / `KanbanBoard` ❌ → Render advanced components directly in the container.
- Hardcoded native Tailwind colors (e.g. `text-red-500`) ❌ → Rely on mapped CSS variables (e.g. `text-error` or `bg-primary`) derived from the `@theme` via `cleen-ui-theme`.
- Custom modal/dialog JSX ❌ → Use `Modal` + `useDisclosure`
- `useState(false)` for overlay open/close ❌ → Use `useDisclosure`
- `alert()` or custom toast ❌ → Use `showNotification`
- `<input>`, `<select>` raw elements ❌ → Use Library `Input`, `Select`, `TextArea`, etc.
- `App.tsx` acting as a 1000-line monolith ❌ → Split into `pages/` and `components/`
- Internal `cleen-` prefixed utilities or `.cleen` in consumer app code ❌ → Use standard tailwind utilities.