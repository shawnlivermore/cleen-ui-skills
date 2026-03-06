# Agent Instructions — cleen-ui-skills

This repository contains agent skill definitions for `@cleen/cleen-components`. If you are an AI agent working on a project that uses this library, this file tells you which skills exist, when to load them, and what they expect from you.

---

## Skills Registry

Skills are defined in `skills/<skill-name>/SKILL.md`. Each file contains a YAML frontmatter block with a `name` and `description` that describes the trigger conditions, followed by full implementation guidance.

Load a skill by reading its `SKILL.md` before implementing anything in its domain.

---

## When to invoke each skill

### Always invoke first for any new project

**`cleen-ui-setup`** — If the user asks how to install, configure, or get started with the library in their project. Also trigger if you are about to import anything from `@cleen/cleen-components` and there is no evidence the library is already installed (i.e. no entry in `dependencies`).

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

### 1. `.cleen` scope wrapper
Every page root **must** have `className="cleen ..."` as the first class. Without it, Tailwind styles and component tokens do not apply.

```tsx
// correct
<div className="cleen cleen-p-6">...</div>

// broken — no scope
<div className="cleen-p-6">...</div>
```

### 2. `cleen-` Tailwind prefix
All Tailwind utility classes are prefixed. Using unprefixed classes (`flex`, `gap-4`, `grid`) will have no effect.

```tsx
// correct
<div className="cleen-flex cleen-gap-4 cleen-grid-cols-3">

// no-op
<div className="flex gap-4 grid-cols-3">
```

Responsive prefixes also take the `cleen-` prefix on the utility part:
```tsx
<div className="cleen-grid md:cleen-grid-cols-2 lg:cleen-grid-cols-4">
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
import { useDisclosure } from '@cleen/cleen-components';
const { isOpen, open, close } = useDisclosure();
```

### 5. No custom tables
Use `DataGrid` or `DataGridWithFilters` for all tabular data. Never write `<table>` markup or roll a custom grid.

### 6. No custom toast/dialog logic
Use `showNotification` for all ephemeral feedback (success, error, warning, info). Never use `alert()`, `window.confirm()`, or custom toast JSX.

### 7. `useForm` + `useValidation` for forms
Do not manage form field state with individual `useState` calls or raw `onChange` handlers. Use `useForm` for state and `useValidation` for validation logic.

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
| `<div className="flex gap-4">` | `<div className="cleen-flex cleen-gap-4">` |
| Page root without `.cleen` class | Add `cleen` as first class on root element |
| `--cleen-primary: #6366f1` | `--cleen-primary: 99, 102, 241` |
| Custom spinner/loading div | `Loader` |
| `<ul><li>` list of records | `DataGrid` or `Card` layout |
| Manual tab switching with `activeTab` state | `Tabs` component |
| Hand-built step tracker + next/back buttons | `Wizard` |
| Individual `useState` per form field | `useForm` |
| `<input>`, `<select>`, `<textarea>` raw elements | Library input components (`Input`, `Select`, `TextArea`, etc.) |
