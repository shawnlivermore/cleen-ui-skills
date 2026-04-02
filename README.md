# cleen-ui-skills

Agent skill definitions for building UI with the Cleen UI monorepo packages (`@cleen/ui-core`, `@cleen/ui`, `@cleen/ui-pro`). These skills are designed for use with GitHub Copilot (or any compatible AI coding agent) and teach the agent library-specific conventions, component APIs, and decision patterns — so it stops reinventing the wheel and reaches for the right component the first time.

---

## What's in here

Each skill lives in `skills/<skill-name>/` and contains a `SKILL.md` with a YAML frontmatter trigger description and full implementation guidance. The architectural skills also ship a `references/` folder with supporting lookup tables and decision guides.

| Skill | What it covers |
| --- | --- |
| [`cleen-ui-setup`](skills/cleen-ui-setup/SKILL.md) | Installing the library, npm auth, peer dep checks, root stylesheet import |
| [`cleen-ui-theme`](skills/cleen-ui-theme/SKILL.md) | CSS variable theming — overriding colors, dark mode, defining brand palettes |
| [`cleen-ui-builder`](skills/cleen-ui-builder/SKILL.md) | The core framework for vibe-coding. Building pages, layouts, forms, data grids, overlays, and feedback UI |

---

## Repository structure

```text
skills/
  cleen-ui-builder/
    SKILL.md
    references/
      component-index.md       # full component catalog with one-liners
      data-display-patterns.md # grids, charts, kanban
      decision-guide.md        # side-by-side comparisons for ambiguous choices
      form-patterns.md         # forms, validation, submission
      layout-default-patterns.md
      layout-patterns.md       # page composition
      navigation-patterns.md   # sidebars, tabs, breadcrumbs
      overlay-patterns.md      # modals, drawers, menus
  cleen-ui-setup/
    SKILL.md
  cleen-ui-theme/
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

| You say                                      | Skill invoked                                                               |
| -------------------------------------------- | --------------------------------------------------------------------------- |
| "How do I install this in my app?"           | `cleen-ui-setup`                                                            |
| "How do I change the primary color?"         | `cleen-ui-theme`                                                            |
| "Build a user dashboard with a data table"   | `cleen-ui-builder`                                                          |
| "Create a multi-step onboarding form"        | `cleen-ui-builder`                                                          |
| "Add a confirmation dialog"                  | `cleen-ui-builder`                                                          |
| "Show a loading skeleton while data fetches" | `cleen-ui-builder`                                                          |

---

## Key conventions enforced by these skills

- **No `.cleen` scope wrapper in consumer projects** — use your app's own layout/container classes at the page root.
- **No `cleen-` Tailwind utility classes in consumer code** — use your project's standard unprefixed utilities (`flex`, `gap-4`, `grid-cols-3`, etc.).
- **Tailwind-first in consumer apps** — if Tailwind is installed, avoid creating layout/component styles in `App.css`/`*.scss` unless explicitly requested.
- **CSS variables as bare RGB triplets** — `--cleen-primary: 99, 102, 241` not `rgb(...)` or hex.
- **Reuse library color variables** — prefer `var(--cleen-*)` aliases over ad-hoc custom color literals.
- **Readable contrast is required** — keep text/background combinations at accessible contrast levels where practical.
- **`useDisclosure`** for all overlay open/close state — never raw `useState(false)`.
- **`useForm` + `useValidation`** for form state — never hand-roll field state management.
- **`showNotification`** for toasts — never `alert()`, custom toast markup, or `window.confirm()`.
- **No custom tables** — `DataGrid` or `DataGridWithFilters` for all tabular data.
- **Default first-page shell** — if layout is unspecified, start with the default SaaS App Shell (left sidebar + right content, no root-level margin/padding wrappers).
- **Do not wrap advanced data surfaces in Card** — render `DataGrid`/`DataGridWithFilters`/`KanbanBoard`/`KanbanList` directly.
- **Chart over SimpleChart** — always prioritize `Chart` for new examples.
- **Avatar is reusable circular media** — use for logos/brand marks as well as user photos.
- **DatePicker over native date input** — avoid `Input type="date"` / datetime-native input types.
- **Default `src/` architecture for new projects** — split into `assets/`, `components/`, `hooks/`, `navigation/`, `pages/`, optional `store/`, optional `types/`, and `utils/`; keep `App.tsx` for top-level composition only.
