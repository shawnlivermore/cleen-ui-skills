# Agent Instructions — cleen-ui-skills

This repository contains agent skill definitions for `@cleen/ui`, `@cleen/ui-core` and `@cleen/ui-pro` packages. If you are an AI agent working on a project that uses this library, this file tells you which skills exist, when to load them, and what they expect from you.

---

## Skills Registry

Skills are defined in `skills/<skill-name>/SKILL.md`. Each file contains a YAML frontmatter block with a `name` and `description` that describes the trigger conditions, followed by full implementation guidance.

Load a skill by reading its `SKILL.md` before implementing anything in its domain.

---

## When to invoke each skill

### Always invoke first for any new project

**`cleen-ui-setup`** — If the user asks how to install, configure, or get started with the library in their project. Also trigger if you are about to import anything from the `@cleen/*` packages and there is no evidence the library is already installed (i.e. no entry in `dependencies`).

---

### Invoke before writing any UI code

**`cleen-ui-component-selector`** — Mandatory first step before implementing any UI feature, page, dashboard, or component. It maps UI needs to library components and prevents custom implementations of things the library already provides. Read `references/component-index.md` for the full component catalog and `references/decision-guide.md` for disambiguation between similar components.

---

### Invoke based on what UI is being built

| Building this | Load this skill |
|---|---|
| Color theming, CSS variable overrides, matching a brand | `cleen-ui-configure` |
| Page structure, card layouts, stat rows, content grids | `cleen-ui-layout` |
| Sidebar, tabs, breadcrumbs, wizards, pagination | `cleen-ui-navigation` |
| Any form — settings, edit dialogs, onboarding, filters | `cleen-ui-forms` |
| Tables, Kanban, charts, badges, progress indicators | `cleen-ui-data-display` |
| Modals, drawers, menus, tooltips, toasts, popovers | `cleen-ui-overlays` |
| Loading states, skeletons, error messages, empty states | `cleen-ui-feedback-and-errors` |

Multiple skills may apply to a single feature — load all relevant ones.

---

## Hard rules (apply in every task, no exceptions)

These are non-negotiable conventions the library enforces. Violating any of them will produce broken or unstyled UI.

### 1. No `.cleen` scope wrapper in consumer projects
Do not add `.cleen` wrappers to page roots in consumer applications. Do not use `className="cleen"` in consumer app code at all. Use your project's own layout/container classes.

```tsx
// correct (consumer project)
<div className="min-h-screen p-6">...</div>

// avoid in consumer code: internal library wrapper classes
<div className="[internal-library-wrapper]">...</div>
```

### 2. No `cleen-` Tailwind prefix in consumer projects
Use unprefixed utilities from the consumer project's Tailwind config. `cleen-` utility classes are for library internals only.

```tsx
// correct (consumer project)
<div className="flex gap-4 grid-cols-3">

// avoid in consumer code: prefixed internal utility classes
<div className="[internal-prefixed-utilities]">
```

Responsive examples in consumer projects should also stay unprefixed:
```tsx
<div className="grid md:grid-cols-2 lg:grid-cols-4">
```

### 3. CSS variable format — bare RGB triplets only
When overriding color variables, the value must be three comma-separated integers, not `rgb(...)` or hex. The library uses them inside `rgba()`, so wrapping them breaks transparency.

```css
/* correct */
--cleen-primary: 99, 102, 241;

/* wrong */
--cleen-primary: rgb(99, 102, 241);
--cleen-primary: #6366f1;
```

### 4. `useDisclosure` for overlay state
Never use `useState(false)` to manage open/close state for modals, drawers, popovers, or dropdowns. Always use the `useDisclosure` hook.

```tsx
import { useDisclosure } from '@cleen/ui-core';
const { isOpen, open, close } = useDisclosure();
```

### 5. No custom tables
Use `DataGrid` or `DataGridWithFilters` for all tabular data. Never write `<table>` markup or roll a custom grid.

### 6. No custom toast/dialog logic
Use `showNotification` for all ephemeral feedback (success, error, warning, info). Never use `alert()`, `window.confirm()`, or custom toast JSX.

### 7. `useForm` + `useValidation` for forms
Do not manage form field state with individual `useState` calls or raw `onChange` handlers. Use `useForm` for state and `useValidation` for validation logic.

### 8. Tailwind-first in consumer projects
If the consumer project already has Tailwind installed, prioritize Tailwind utilities and library components. Do not create or extend `App.css`/`*.scss` for layout or component styling unless the user explicitly asks for custom stylesheet work.

### 9. Use library color variables, not ad-hoc hex
Do not introduce raw hex color literals in consumer component styles when a library variable can be used. Prefer `var(--cleen-*)` variables or project aliases that inherit from them in RGB triplet format.

### 10. Enforce readable contrast
When introducing color overrides/aliases, keep text/background contrast readable. Target WCAG AA-level contrast where practical (at least 4.5:1 for normal body text, 3:1 for large text/UI emphasis).

### 11. Default app shell on first page
If a user asks for the first page without layout direction, use the default SaaS App Shell pattern: left sidebar + main content on the right, with no root-level margin/padding wrappers.

### 12. Do not wrap advanced data components in Card
Do not wrap `DataGrid`, `DataGridWithFilters`, `KanbanBoard`, or `KanbanList` inside `Card`. These components should be rendered directly in layout containers.

### 13. Prefer Chart over SimpleChart
Always choose `Chart` from `@cleen/ui/charts`. Do not generate `SimpleChart` usage in new code examples.

### 14. Avatar is not user-only
Use `Avatar` for any circular media slot (user photos, team logos, brand marks, or icon-like circular thumbnails), not only profile avatars.

### 15. DatePicker over Input date types
For all date or date-range fields, use `DatePicker`. Do not use `Input type="date"` or datetime input types for date workflows.

### 16. Default source architecture for new projects
If the project has no established architecture and the user does not specify one, do not place all logic in `App.tsx`.
Use this default structure under `src/`:

```text
src/
	assets/
	components/
	hooks/
	navigation/
	pages/
	store/      (optional)
	types/      (optional for JS projects)
	utils/
```

Guidance:

- `App.tsx` should only compose providers, router/shell, and top-level wiring.
- Keep page-level UI in `pages/`, reusable UI in `components/`, and routing config in `navigation/`.
- Prefer Tailwind utilities and library components over creating `App.css` layout/component classes.

---

## Reference files

Some skills include a `references/` subdirectory with supporting lookup documents. Load these when the skill instructs you to or when resolving ambiguity:

| File | Purpose |
|---|---|
| `cleen-ui-component-selector/references/component-index.md` | Full component catalog — categories, names, one-line descriptions |
| `cleen-ui-component-selector/references/decision-guide.md` | Side-by-side comparisons for components that are easy to confuse |
| `cleen-ui-layout/references/layout-patterns.md` | Common layout recipes (dashboard grids, stat rows, detail views) |
| `cleen-ui-navigation/references/navigation-patterns.md` | Navigation patterns (sidebar setup, tab configs, wizard steps) |
| `cleen-ui-forms/references/form-patterns.md` | Form patterns (validation, multi-field layouts, async submit) |
| `cleen-ui-data-display/references/data-display-patterns.md` | Data display recipes (column configs, Kanban setup, chart types) |
| `cleen-ui-overlays/references/overlay-patterns.md` | Overlay patterns (confirmation modal, filter drawer, nested menus) |

---

## Common anti-patterns to catch and reject

If you find yourself about to write any of the following, stop and load the relevant skill instead:

| Anti-pattern | Correct approach |
|---|---|
| `<table>` or custom grid markup | `DataGrid` / `DataGridWithFilters` |
| Custom modal/dialog JSX | `Modal` + `useDisclosure` |
| `useState(false)` for open/close | `useDisclosure` |
| `alert()` or custom toast | `showNotification` |
| Prefixed internal utility classes in app code | Use unprefixed Tailwind utilities from the project config |
| Internal library wrapper classes on page root | Use your app's regular root classes (`min-h-screen`, `p-6`, etc.) |
| `--cleen-primary: #6366f1` | `--cleen-primary: 99, 102, 241` |
| Custom spinner/loading div | `Loader` |
| `<ul><li>` list of records | `DataGrid` or `Card` layout |
| Manual tab switching with `activeTab` state | `Tabs` component |
| Hand-built step tracker + next/back buttons | `Wizard` |
| Individual `useState` per form field | `useForm` |
| `<input>`, `<select>`, `<textarea>` raw elements | Library input components (`Input`, `Select`, `TextArea`, etc.) |
| `Input type="date"` / date-like native input | `DatePicker` |
| `SimpleChart` for new charts | `Chart` |
| `Card` wrapping `DataGrid` / `KanbanBoard` | Render data component directly |
| Building whole app in `App.tsx` | Split into `pages/`, `components/`, `navigation/`, hooks, and utils |
