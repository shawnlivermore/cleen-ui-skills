---
name: cleen-ui-component-selector
description: Select the right @cleen/cleen-components component before writing any UI code. ALWAYS trigger this skill when implementing any UI — dashboards, pages, forms, modals, tables, cards, lists, navigation, charts, overlays, status indicators, media players, or any other interface element. This applies to vague prompts like "build a dashboard", "create a profile page", "add a filter panel", "show a table", "make a stepper form", etc. The primary purpose is to PREVENT building custom UI from scratch when a cleen-component already exists. Trigger on any feature or page generation request, not just explicit component questions.
---

# Component Selector

This skill exists to stop you from reinventing the wheel. This project uses `@cleen/cleen-components` — a full-featured component library. **Before writing any custom UI code, check if a cleen-component already covers it.** Users will ask for pages, features, and flows — they won't say "use the DataGrid component". That's your job.

---

## Prime Directive

> **Never create a custom implementation of a UI pattern that a cleen-component already solves.**

Common traps to avoid:
- Rolling a custom table/grid → use `DataGrid` or `DataGridWithFilters`
- Building a custom modal/dialog → use `Modal`
- Writing a custom dropdown or context menu → use `Menu`, `Dropdown`, or `Popover`
- Making a spinner or skeleton → use `Loader` or `Skeletons`
- Implementing a custom progress indicator → use `ProgressBar`, `ProgressCircle`, or `AdvancedProgressBar`
- Crafting custom toast/alert logic → use `Notification` (function API)
- Hand-rolling a stepped form → use `Wizard`
- Building a custom audio player or recorder → use `AudioPlayback` or `AudioRecorder`
- Writing a custom chart → use `Chart` or `SimpleChart`

---

## Workflow

1. **Decompose the UI** — Break the requested page or feature into discrete UI elements (table, form, sidebar, chart, badge, etc.).
2. **Scan the index** — Read `references/component-index.md` for a categorical overview and quick one-liners. Match each UI element to a component.
3. **Resolve ambiguity** — If two or more components could work for an element, consult `references/decision-guide.md` for side-by-side comparisons.
4. **Implement with the library** — Use the matched components. Reach for component-specific dos/don'ts below to wire props correctly.

---

## Component Overviews & Dos/Don'ts

### Inputs & Forms

**Button**
- Triggers actions. Use variants (`primary`, `secondary`, `borderless`, `danger`) to communicate intent.
- DO use `isLoading` for async actions. DO use `leftIcon`/`rightIcon` for interactive affordance.
- DON'T use it for navigation — use a link or router `<Link>` instead.

**Input**
- Single-line text field. Use for short user text (names, search terms, IDs).
- DO use `label` + `InfoLabels` for accessible, guided forms. DO use `maxLength` + `maxLengthLabel` when length matters.
- DON'T use for multi-line text — use `TextArea`.

**TextArea**
- Multi-line text field with auto-resize.
- DO use when content length is variable (comments, descriptions).
- DON'T rely on it for structured data input — pair with `FormGroup` for layout.

**AiInput** / **AiTextArea**
- Extend `Input`/`TextArea` with an AI generation trigger and confirm/cancel overlay.
- DO use when the field benefits from AI-assisted content (e.g., bio, description, message drafting).
- DON'T use if AI generation isn't wired up — the trigger button will be dead weight.

**AiWidget**
- The AI trigger/confirm/cancel UI on its own.
- DO use when you want AI assistance on a custom field not covered by `AiInput`/`AiTextArea`.
- DON'T embed directly without an input to receive the generated content.

**Select**
- Dropdown select built on `react-select`. Single or multi-select from a fixed option list.
- DO use for static or pre-loaded options. DO use `createNewOptionCallback` for creatable items.
- DON'T use for async/server-searched options — use `Lookup` instead.

**Lookup**
- Searchable async dropdown with debounce and character threshold.
- DO use when options are fetched from an API on user input.
- DON'T keep `debounceDelay` too short — it'll hammer the server on every keystroke.

**Checkbox** / **CheckboxGroup**
- `Checkbox`: single boolean toggle with optional indeterminate state. `CheckboxGroup`: managed list of checkboxes.
- DO use `CheckboxGroup` when you have a list of independent toggles (e.g., permissions, features).
- DON'T use `Checkbox` with `allowIndeterminate` unless you control its state from parent — it won't self-manage.

**Switch**
- Binary on/off toggle. Preferred for settings and feature flags.
- DO use over `Checkbox` when the UX metaphor is "enable/disable" rather than "select".
- DON'T use for lists of options — use `CheckboxGroup`.

**RadioButtonGroup**
- Group of styled radio buttons — one option selectable at a time.
- DO use for mutually exclusive options with text labels.
- DON'T use when options vary in layout richness — use `RadioBoxGroup`.

**RadioBoxGroup**
- Selectable card-style boxes with rich content (icons, decorators, sublabels).
- DO use when options need visual context beyond a label (plan tiers, categories).
- DON'T use for simple lists — `RadioButtonGroup` is cleaner.

**DatePicker**
- Date or date-range selector via calendar popover.
- DO pass `mode="range"` for start/end date pairs. DO use `min`/`max` for constrained ranges.
- DON'T use for time — it's date-only.

**CreditCardInput**
- Full credit card form: number, expiry, CVC with card visualization.
- DO use as a single unit — don't split the fields manually.
- DON'T use without PCI-compliant backend handling for the submitted data.

**Slider** / **RangeSlider**
- `Slider`: single numeric value. `RangeSlider`: min/max interval as a tuple.
- DO use `RangeSlider` when users need to define a range (e.g., price filter, date spread).
- DON'T use `Slider` when the user needs two handles.

**FormGroup**
- Layout row: label column on the left, form control on the right.
- DO use to wrap any form input for consistent form layout.
- DON'T put complex multi-field sections inside a single `FormGroup` — nest them.

**InfoLabels**
- Renders info, error, and subtitle messages below a field.
- DO use `errorMessage` for validation feedback (it renders with `role="alert"`).
- DON'T place it above a field — it's designed for below-field placement.

---

### Navigation & Layout

**Tabs**
- Switch between predefined content sections.
- DO use `direction="vertical"` for sidebar-style navigation.
- DON'T replace router-based page navigation with tabs unless content stays on the same page.

**Breadcrumb**
- Shows the current page hierarchy.
- DO include a `root` segment for the home link. DO set `segments` from your router state.
- DON'T use on flat (single-level) page structures.

**Sidebar**
- App-level side navigation panel.
- DO use for primary navigation with icons and labels.
- DON'T use as a content drawer — use `Drawer` for that.

**Stepper**
- Visual step indicator (horizontal or vertical).
- DO use `Stepper` when you want just the indicator UI without step content management.
- DON'T use `Stepper` alone for full wizard flows — use `Wizard` instead.

**Wizard**
- Full multi-step flow with step management, navigation buttons, and content rendering.
- DO use when steps have defined content and users move forward/backward.
- DON'T use when steps don't have a linear sequence — `Tabs` may be better.

**Pagination**
- Page controls with optional page-size selector and go-to-page input.
- DO use `hasPageSizeSelector` for power users viewing heavy data sets.
- DON'T manage pagination state manually when `usePaginationState` hook exists.

---

### Overlays & Contextual UI

**Modal**
- Centered dialog for focused tasks or confirmations.
- DO use `closeOnOverlayClick` for low-stakes dialogs. DON'T block dismissal for critical confirmations.
- DON'T put navigation or complex multi-step flows inside a modal — use `Wizard` + `Modal`.

**Drawer**
- Right-anchored slide-over panel for contextual detail or editing.
- DO use for editing a record without leaving the page.
- DON'T use for blocking confirmations — use `Modal`.

**FilterDrawer** / **DataGridWithFilters**
- `FilterDrawer`: standalone filter side panel. `DataGridWithFilters`: DataGrid pre-integrated with FilterDrawer.
- DO use `DataGridWithFilters` when you want filters tightly coupled to a grid.
- DON'T rebuild a FilterDrawer manually if `DataGridWithFilters` already fits.

**Dropdown**
- Button that reveals arbitrary content beneath it.
- DO use when the revealed content isn't a simple item list.
- DON'T use `Dropdown` when you just need a list of clickable items — use `Menu`.

**Menu**
- Popup list of clickable items triggered by click.
- DO use for context menus and action lists.
- DON'T put non-item custom content in a `Menu` — use `Dropdown` or `Popover`.

**Popover**
- Floating custom-content panel triggered by click, controlled or uncontrolled.
- DO use when you need a floating panel that isn't strictly an item list or dropdown.
- DON'T use for simple tooltips — use `Tooltip`.

**Tooltip**
- Hover-triggered label for explaining UI elements.
- DO use `openDelay` to avoid tooltip spam on cursor movement.
- DON'T put interactive content (buttons, links) inside a tooltip.

**Notification**
- Toast notification for brief feedback messages.
- DO use `showNotification()` (the function API) — it doesn't require component mounting.
- DON'T use for persistent messages that require user action — use a `Modal` or inline alert.

**Collapsible**
- Accordion list of expandable sections.
- DO use when content space is limited and sections are naturally grouped.
- DON'T expect multiple sections open simultaneously — only one is open at a time.

---

### Data Display

**DataGrid**
- Full-featured table with sorting, search, filters, drag-and-drop rows.
- DO use `withSearch` and `withFilters` to progressively enhance the grid.
- DON'T use for non-tabular data — reach for `Card` + lists instead.

**DataGridWithFilters**
- DataGrid + FilterDrawer pre-wired. Use over DataGrid when filter UI is needed.
- DON'T duplicate filter logic externally when this component handles it internally.

**KanbanBoard** / **KanbanList**
- Drag-and-drop Kanban views (board = card grid, list = row list).
- DO use `KanbanBoard` for card-heavy views with column buckets. Use `KanbanList` for list-first layouts.
- DON'T use both on the same screen without an explicit view toggle.

**KanbanBlocks**
- Individual Kanban sub-components (cards, column headers, filter bar, edit modal).
- DO use when customizing or composing a custom Kanban layout.
- DON'T use `KanbanBlocks` when `KanbanBoard` already covers the need — it's extra complexity.

**Card**
- General-purpose content container. Supports `CardIcon` and `CardMedia` sub-components.
- DO use `hoverable` for clickable cards. DO use `isGlass` for layered/overlay contexts.
- DON'T nest Cards deeply — one level of Card hierarchy is usually enough.

**Avatar** / **AvatarRow**
- `Avatar`: single user avatar. `AvatarRow`: stacked row with overflow count.
- DO use `AvatarRow` when showing "members of X" with a "+N more" pattern.
- DON'T use `AvatarRow` when you need individual click actions per avatar — iterate `Avatar` manually.

**PillBadge**
- Compact badge for statuses, tags, categories, and counts.
- DO use `colorByIndex` for consistent color mapping across repeated categories.
- DON'T use for long labels — truncation hurts readability.

**GroupSelector**
- Hierarchical group/item selector with create/edit/search.
- DO use when data has a two-level group → item hierarchy.
- DON'T use for flat option lists — `Select` is simpler.

**Skeletons**
- Loading placeholders matching the shape of content being loaded.
- DO use the right skeleton variant per content type (e.g., `SkeletonDataGrid`, `SkeletonCard`).
- DON'T show skeletons indefinitely — pair with timeout error states.

---

### Progress & Feedback

**ProgressBar**
- Horizontal progress with optional percentage label.
- DO use `renderAnimated` for entrance animation on page load.
- DON'T use for multi-bar comparisons — use `AdvancedProgressBar`.

**AdvancedProgressBar**
- Multi-bar overlapping progress track with clamp markers.
- DO use for dashboards displaying overlapping datasets (e.g., budget vs. actuals).
- DON'T use for single simple progress — `ProgressBar` is cleaner.

**ProgressCircle**
- Animated SVG donut/circle progress indicator.
- DO use for KPI-style percentage displays in dashboards.
- DON'T use where horizontal bar context is more natural.

**Loader**
- Spinning indicator for loading states.
- DO use `isFullscreen` for blocking loading of entire views.
- DON'T permanently show a `Loader` — always pair it with a timeout or error fallback.

**Stepper** (see Navigation)

---

### Media & Rich Input

**AudioPlayback**
- Audio player with waveform visualization.
- DO use when the audio file URL is already available.
- DON'T use for recording — use `AudioRecorder`.

**AudioRecorder**
- Microphone recorder with waveform and trim region.
- DO use the `onFinalize` callback to receive the trimmed audio blob.
- DON'T start recording without requesting microphone permission first.

---

### Charts

**Chart**
- Full ApexCharts wrapper: line, bar, pie, area, scatter, radar, etc.
- DO use when you need interactive charts with tooltips, legends, zoom, and rich config.
- DON'T pass raw ApexCharts options without typing them — use the exported option types.

**SimpleChart**
- Minimal SVG line chart for sparkline-style display.
- DO use for compact trend indicators embedded in cards or table cells.
- DON'T use when you need interactivity, legends, or axes — use `Chart`.

---

### Icons & Layout Utilities

**CleenIcon**
- Renders any react-icons icon by name string.
- DO use when icon name is dynamic or config-driven.
- DON'T use when you can import a specific icon directly — direct import is faster.

**Divider**
- Thin separator line (horizontal or vertical).
- DO use `isHorizontal={false}` for vertical separators in flex rows.
- DON'T use as a substitute for spacing/padding — use margin utilities.

---

## Reference Files

- **`references/component-index.md`** — Categorical index of all components with one-liner summaries and import paths. Read this first for a broad scan.
- **`references/decision-guide.md`** — Side-by-side comparison tables for components that are easily confused. Consult when narrowing down between 2–3 candidates.
