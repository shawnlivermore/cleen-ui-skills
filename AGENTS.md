# Agent Instructions — cleen-ui-skills

This repository contains agent skill definitions for `@cleen/ui`, `@cleen/ui-core` and `@cleen/ui-pro` packages. If you are an AI agent working on a project that uses this library, this file tells you which skills exist, when to load them, and what they expect from you.

---

## Skills Registry

This repository has a streamlined "Power-Skill" structure to handle UI generation continuously without dropping context. Load the correct skill before implementing anything in its domain.

---

## When to invoke each skill

### 1. `cleen-ui-setup`
**Always invoke first for any new project initialization.** 
Use this when the user asks how to install, configure, or get started with the library. 

### 2. `cleen-ui-theme`
**Always invoke for global styling, colors, or dark mode.**
Use this when the user asks about changing the brand color, adjusting variables, setting up dark mode overrides, or CSS customizations.

### 3. `cleen-ui-builder`
**Mandatory for ANY UI feature, page, component, overlay, form, table, or feedback state.**
This is your core framework skill for vibe-coding. It maps UI needs to library components and enforces the primary architectural constraints. It contains references to all form, layout, overlay, data-display, and navigation recipes you need. Load this *first* whenever the user asks you to build or modify any screen.

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
Do not introduce raw hex color literals in consumer component styles when a library variable can be used. Prefer `var(--cleen-*)` variables. **For Tailwind v4 projects, you MUST map these variables in the global CSS using the `@theme` block** (e.g. `--color-primary: rgb(var(--cleen-primary));`) so standard utilities like `bg-primary` work correctly.

### 10. Enforce readable contrast
When introducing color overrides/aliases, keep text/background contrast readable. Target WCAG AA-level contrast where practical (at least 4.5:1 for normal body text, 3:1 for large text/UI emphasis).

### 11. Default app shell on first page
If a user asks for the first page without layout direction, you MUST create a `<PageWrapper>` (or use an existing one if provided) that wraps the child page with the default SaaS App Shell pattern: a left `Sidebar` + main content canvas. Do NOT create custom `<aside>` or layout boilerplate yourself.

### 12. Do not wrap advanced data components in Card
Do not wrap `DataGrid`, `DataGridWithFilters`, `KanbanBoard`, or `KanbanList` inside `Card`. These components should be rendered directly in layout containers.

### 13. Prefer Chart over SimpleChart
Always choose `Chart` from `@cleen/ui/charts`. Do not generate `SimpleChart` usage in new code examples.

### 14. Avatar is not user-only
Use `Avatar` for any circular media slot (user photos, team logos, brand marks, or icon-like circular thumbnails), not only profile avatars.

### 15. DatePicker over Input date types
For all date or date-range fields, use `DatePicker`. Do not use `Input type="date"` or datetime input types for date workflows.

### 16. Do not search for Pro components in the free package
Assume a hard boundary between public and private packages. If a project requires a pro component like `DataGrid`, `KanbanBoard`, or `Wizard` but `@cleen/ui-pro` is not installed, **STOP**. Do NOT read the node_modules folder or search the free package to verify if it's there. Instruct the user to set up their `.npmrc` instead.

### 17. Default source architecture for new projects
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

The `cleen-ui-builder` skill includes a `references/` subdirectory with supporting lookup documents. Pull the specific reference file dynamically based on the component type requested:

| File | Purpose |
|---|---|
| `cleen-ui-builder/references/component-index.md` | Full component catalog — categories, names, descriptions |
| `cleen-ui-builder/references/decision-guide.md` | Side-by-side comparisons for similar components |
| `cleen-ui-builder/references/page-templates.md` | The universal `PageWrapper` layout shell block |
| `cleen-ui-builder/references/page-templates/dashboard-templates.md` | Common dashboard and grid structure |
| `cleen-ui-builder/references/page-templates/data-grid-templates.md` | Common data grids |
| `cleen-ui-builder/references/page-templates/settings-templates.md` | Common settings templates layout recipes |
| `cleen-ui-builder/references/navigation-patterns.md` | Sidebar setup, tab configs, wizard steps |
| `cleen-ui-builder/references/form-patterns.md` | Form validation, multi-field layouts, async submit |
| `cleen-ui-builder/references/data-display-patterns.md` | Data grid column configs, charts, Kanban boards |
| `cleen-ui-builder/references/overlay-patterns.md` | Confirmation modals, sliding filter drawers, menus |

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
| Searching `@cleen/ui` or `node_modules` for Pro components like `DataGrid` | Stop instantly, assume it's missing, and run `cleen-ui-setup` to configure `.npmrc` |
| Building whole app in `App.tsx` | Split into `pages/`, `components/`, `navigation/`, hooks, and utils |
| Hardcoded native Tailwind colors (`bg-slate-300`, `text-gray-900`) | Rely on custom mapped variables from your theme like `bg-background`, `text-gray`, `border-gray/30`. |
| Creating custom arbitrary `<aside>`, `<header>`, `<nav>` shells | Wrap all page contents directly inside the predefined `<PageWrapper>` container! |
