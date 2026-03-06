# cleen-ui-skills

Agent skill definitions for building UI with `@cleen/cleen-components`. These skills are designed for use with GitHub Copilot (or any compatible AI coding agent) and teach the agent library-specific conventions, component APIs, and decision patterns — so it stops reinventing the wheel and reaches for the right component the first time.

---

## What's in here

Each skill lives in `skills/<skill-name>/` and contains a `SKILL.md` with a YAML frontmatter trigger description and full implementation guidance. Some skills also ship a `references/` folder with supporting lookup tables and decision guides.

| Skill | What it covers |
|---|---|
| [`cleen-ui-setup`](skills/cleen-ui-setup/SKILL.md) | Installing the library, npm auth, peer dep checks, stylesheet import, dark mode |
| [`cleen-ui-configure`](skills/cleen-ui-configure/SKILL.md) | CSS variable theming — overriding colors for light/dark mode |
| [`cleen-ui-component-selector`](skills/cleen-ui-component-selector/SKILL.md) | Picking the right component before writing any UI code |
| [`cleen-ui-layout`](skills/cleen-ui-layout/SKILL.md) | Page layout with `Card`, `Divider`, `Avatar`, Tailwind prefix rules, `.cleen` scope wrapper |
| [`cleen-ui-navigation`](skills/cleen-ui-navigation/SKILL.md) | `Sidebar`, `Breadcrumb`, `Tabs`, `Wizard`, `Stepper`, `Pagination` |
| [`cleen-ui-forms`](skills/cleen-ui-forms/SKILL.md) | Form layout, all input components, `useForm`, `useValidation`, validation UX |
| [`cleen-ui-data-display`](skills/cleen-ui-data-display/SKILL.md) | `DataGrid`, `KanbanBoard`, `Chart`, `ProgressBar`, `PillBadge`, `Loader`, `Skeletons` |
| [`cleen-ui-overlays`](skills/cleen-ui-overlays/SKILL.md) | `Modal`, `Drawer`, `FilterDrawer`, `Menu`, `Dropdown`, `Popover`, `Tooltip`, `showNotification` |
| [`cleen-ui-feedback-and-errors`](skills/cleen-ui-feedback-and-errors/SKILL.md) | Loading states, toasts, skeleton screens, inline errors, empty states |

---

## Repository structure

```
skills/
  cleen-ui-setup/
    SKILL.md
  cleen-ui-configure/
    SKILL.md
  cleen-ui-component-selector/
    SKILL.md
    references/
      component-index.md       # full component catalog with one-liners
      decision-guide.md        # side-by-side comparisons for ambiguous choices
  cleen-ui-layout/
    SKILL.md
    references/
      layout-patterns.md
  cleen-ui-navigation/
    SKILL.md
    references/
      navigation-patterns.md
  cleen-ui-forms/
    SKILL.md
    references/
      form-patterns.md
  cleen-ui-data-display/
    SKILL.md
    references/
      data-display-patterns.md
  cleen-ui-overlays/
    SKILL.md
    references/
      overlay-patterns.md
  cleen-ui-feedback-and-errors/
    SKILL.md
```

---

## How to use these skills

### In VS Code with GitHub Copilot

Add the skills folder to your project's Copilot configuration (`.github/copilot-instructions.md` or `.vscode/settings.json` skills path), or reference individual `SKILL.md` files as agent instructions. Skills use the `description` frontmatter field as the auto-trigger condition — Copilot will invoke the relevant skill automatically based on your prompt.

Each skill file is self-contained and can be registered independently. You don't need to load all of them — pick the ones relevant to your project.

### Installation via `skills.sh`

Open terminal inside your project, run this command `npx skills add https://github.com/shawnlivermore/cleen-ui-skills` and follow instructions on the interface. It should let you choose which skills to install inside your project or globally.

### Trigger examples

| You say | Skill invoked |
|---|---|
| "How do I install this in my app?" | `cleen-ui-setup` |
| "How do I change the primary color?" | `cleen-ui-configure` |
| "Build a user dashboard" | `cleen-ui-component-selector` → `cleen-ui-layout` → `cleen-ui-data-display` |
| "Create a multi-step onboarding form" | `cleen-ui-forms` + `cleen-ui-navigation` |
| "Add a confirmation dialog" | `cleen-ui-overlays` |
| "Show a loading skeleton while data fetches" | `cleen-ui-feedback-and-errors` |

---

## Key conventions enforced by these skills

- **`.cleen` scope wrapper** — every page root needs `className="cleen ..."` or nothing renders correctly.
- **`cleen-` Tailwind prefix** — all utility classes are prefixed: `cleen-flex`, `cleen-gap-4`, `cleen-grid-cols-3`, etc.
- **CSS variables as bare RGB triplets** — `--cleen-primary: 99, 102, 241` not `rgb(...)` or hex.
- **`useDisclosure`** for all overlay open/close state — never raw `useState(false)`.
- **`useForm` + `useValidation`** for form state — never hand-roll field state management.
- **`showNotification`** for toasts — never `alert()`, custom toast markup, or `window.confirm()`.
- **No custom tables** — `DataGrid` or `DataGridWithFilters` for all tabular data.
