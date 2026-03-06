# Component Decision Guide

Use this file when two or more components seem interchangeable. Each section compares candidates, clarifies intent, and provides a clear pick.

---

## Table of Contents

1. [Dropdown vs Menu vs Popover](#dropdown-vs-menu-vs-popover)
2. [Modal vs Drawer](#modal-vs-drawer)
3. [Select vs Lookup](#select-vs-lookup)
4. [Slider vs RangeSlider](#slider-vs-rangeslider)
5. [Checkbox vs Switch](#checkbox-vs-switch)
6. [RadioButtonGroup vs RadioBoxGroup](#radiobuttongroup-vs-radioboxgroup)
7. [CheckboxGroup vs RadioButtonGroup vs RadioBoxGroup](#checkboxgroup-vs-radiobuttongroup-vs-radioboxgroup)
8. [DataGrid vs DataGridWithFilters](#datagrid-vs-datagridwithfilters)
9. [KanbanBoard vs KanbanList vs KanbanBlocks](#kanbanboard-vs-kanbanlist-vs-kanbanblocks)
10. [Stepper vs Wizard vs Tabs](#stepper-vs-wizard-vs-tabs)
11. [ProgressBar vs AdvancedProgressBar vs ProgressCircle](#progressbar-vs-advancedprogressbar-vs-progresscircle)
12. [Chart vs SimpleChart](#chart-vs-simplechart)
13. [AiInput vs AiTextArea vs AiWidget](#aiinput-vs-aitextarea-vs-aiwidget)
14. [AudioPlayback vs AudioRecorder](#audioplayback-vs-audiorecorder)
15. [Tooltip vs Popover vs Menu](#tooltip-vs-popover-vs-menu)
16. [Collapsible vs Tabs vs Accordion](#collapsible-vs-tabs)
17. [Avatar vs AvatarRow](#avatar-vs-avatarrow)

---

## Dropdown vs Menu vs Popover

All three show floating content on click. The difference is the **content type**.

| | Dropdown | Menu | Popover |
|---|---|---|---|
| **Content type** | Arbitrary JSX | List of action items | Arbitrary JSX |
| **Trigger** | Built-in button | Children (any trigger) | Children (any trigger) |
| **Controlled mode** | Yes (`isOpen`/`onOpenChange`) | No (internal) | Yes (`isOpen`/`onOpenChange`) |
| **Closes on item click** | Optional (`keepOpenOnClickContent`) | Yes (default) | Optional |
| **Item-specific actions** | No | Yes (`items` array) | No |

**Pick rule:**
- Need a **list of clickable actions** (context menu, action list)? → **Menu**
- Need a **button that opens custom content** (date picker inline, tag selector, custom form)? → **Dropdown**
- Need **full control** over a floating panel attached to any trigger? → **Popover**

---

## Modal vs Drawer

Both are overlays that render outside the main DOM tree. The difference is **position and use case**.

| | Modal | Drawer |
|---|---|---|
| **Position** | Centered + backdrop | Right-anchored slide-over |
| **Use case** | Confirmations, short forms, alerts | Editing, detail view, filters |
| **Blocks interaction** | Yes (full backdrop) | Yes (backdrop optional) |
| **Title/footer** | `header` / `footer` props | `title` / `subtitle` / `footer` props |
| **Size options** | `size` prop (sm/md/lg/xl/full) | Fixed width |

**Pick rule:**
- **Confirmation, quick form, alert** that demands user attention? → **Modal**
- **Record detail, edit panel, filter interface** that slides in contextually? → **Drawer**

---

## Select vs Lookup

Both are dropdown inputs. The difference is **where options come from**.

| | Select | Lookup |
|---|---|---|
| **Options source** | Static / pre-loaded array | Async fetch on user input |
| **Built on** | `react-select` | `react-select` (async variant) |
| **Multi-select** | Yes | Yes |
| **Creatable** | Yes (`createNewOptionCallback`) | Yes |
| **Search** | Client-side filtering | Server-side via `onInputChange` callback |
| **Debounce** | No | Yes (`debounceDelay`) |
| **Character threshold** | No | Yes (min chars before firing search) |

**Pick rule:**
- Options are **already loaded** (< a few hundred items, no API call needed)? → **Select**
- Options come **from an API**, are large in volume, or need to be searched server-side? → **Lookup**

---

## Slider vs RangeSlider

| | Slider | RangeSlider |
|---|---|---|
| **Handles** | 1 | 2 (min + max) |
| **Value type** | `number` | `[number, number]` tuple |
| **Use case** | Volume, opacity, single threshold | Price range, date spread, tolerance band |

**Pick rule:** Does the user need to define **both a lower and upper bound**? → **RangeSlider**. Single value? → **Slider**.

---

## Checkbox vs Switch

Both are binary toggles. The difference is **semantic context**.

| | Checkbox | Switch |
|---|---|---|
| **Mental model** | "Select this item" / agree to terms | "Enable / disable this feature" |
| **Indeterminate state** | Yes (`allowIndeterminate`) | No |
| **List use** | Natural in `CheckboxGroup` | Awkward in lists |
| **Settings use** | Acceptable | Preferred |

**Pick rule:**
- Toggling a **setting or feature flag** ("Enable notifications")? → **Switch**
- Selecting items in a **list** or **agreeing to something**? → **Checkbox**

---

## RadioButtonGroup vs RadioBoxGroup

Both select one option from many. The difference is **visual richness**.

| | RadioButtonGroup | RadioBoxGroup |
|---|---|---|
| **Visual style** | Standard radio + label text | Card-style selectable boxes |
| **Extra content** | `title` / `postTitle` / `subTitle` per option | `leftElement` / `rightElement` injectable decorators |
| **Layout** | Vertical list | Horizontal or vertical |
| **Use case** | Simple mutually exclusive choice | Rich option cards (plans, categories, media types) |

**Pick rule:** Options need **icons, images, or structured inner content**? → **RadioBoxGroup**. Plain labelled choices? → **RadioButtonGroup**.

---

## CheckboxGroup vs RadioButtonGroup vs RadioBoxGroup

| | CheckboxGroup | RadioButtonGroup | RadioBoxGroup |
|---|---|---|---|
| **Selection** | Multiple | Single | Single |
| **Style** | Checkboxes | Radio buttons | Card boxes |
| **Use case** | "Pick all that apply" | "Pick one (simple)" | "Pick one (rich)" |

**Pick rule:** Multiple items selectable? → **CheckboxGroup**. Exactly one? → **RadioButtonGroup** or **RadioBoxGroup** based on visual complexity.

---

## DataGrid vs DataGridWithFilters

| | DataGrid | DataGridWithFilters |
|---|---|---|
| **Filter UI** | Optional column filters inline | Full FilterDrawer panel |
| **Saved filters** | No | Yes |
| **Config** | `withFilters` prop (column filters) | `filtersConfig` + `pinnedFilters` props |
| **When to use** | Simple table with optional column-level filtering | Table that needs a dedicated filter panel with save/apply |

**Pick rule:** Need a **filter drawer panel** with saved filter support? → **DataGridWithFilters**. Just inline column filters on a table? → **DataGrid** with `withFilters`.

---

## KanbanBoard vs KanbanList vs KanbanBlocks

| | KanbanBoard | KanbanList | KanbanBlocks |
|---|---|---|---|
| **Layout** | Card grid columns | Row list | Modular sub-components |
| **Drag-and-drop** | Yes | Yes | Used internally |
| **When to use** | Standard Kanban UX | List-oriented project view | Custom layout composition |

**Pick rule:**
- Standard **card board** (Trello-style)? → **KanbanBoard**
- **List view** of tasks per column? → **KanbanList**
- Need to **compose a custom Kanban** layout from parts? → **KanbanBlocks**

---

## Stepper vs Wizard vs Tabs

All three involve multiple sections. The difference is **whether steps are sequential** and **who controls content**.

| | Stepper | Wizard | Tabs |
|---|---|---|---|
| **Step sequence** | Display only (indicator) | Enforced forward/back flow | Free navigation |
| **Content** | None (indicator only) | Renders current step content | Renders active tab content |
| **Navigation buttons** | None | Built-in (Back / Next / Finish) | None (tabs are the nav) |
| **Use case** | Show progress alongside custom content | Guided multi-step flow | Independent non-ordered sections |

**Pick rule:**
- Steps are **ordered**, user must move through them? → **Wizard**
- Just need the **step indicator UI** while managing content yourself? → **Stepper**
- Sections are **independent** and order doesn't matter? → **Tabs**

---

## ProgressBar vs AdvancedProgressBar vs ProgressCircle

| | ProgressBar | AdvancedProgressBar | ProgressCircle |
|---|---|---|---|
| **Shape** | Horizontal bar | Horizontal track with multiple bars | SVG circle/donut |
| **Datasets** | Single | Multiple overlapping | Single |
| **Clamp markers** | No | Yes | No |
| **Use case** | Simple progress (upload, step completion) | Dashboard: budget vs. actuals, layered metrics | KPI display, percentage at a glance |

**Pick rule:**
- Single value progress? → **ProgressBar**
- Multiple overlapping values on one track? → **AdvancedProgressBar**
- Circular / donut style KPI? → **ProgressCircle**

---

## Chart vs SimpleChart

| | Chart | SimpleChart |
|---|---|---|
| **Engine** | ApexCharts | Pure SVG |
| **Chart types** | Line, bar, pie, area, scatter, radar, bell curve, etc. | Line sparkline only |
| **Interactivity** | Tooltips, zoom, legend, drill-down | None |
| **Bundle size** | Heavy (ApexCharts) | Minimal |
| **Use case** | Dashboard panels, dedicated chart views | Compact trend in cards, table cells |

**Pick rule:** Need **interaction** (hover, zoom, legend) or **non-line chart types**? → **Chart**. Need a **lightweight inline sparkline**? → **SimpleChart**.

---

## AiInput vs AiTextArea vs AiWidget

| | AiInput | AiTextArea | AiWidget |
|---|---|---|---|
| **Base field** | Input (single-line) | TextArea (multi-line) | None |
| **AI overlay** | Built-in | Built-in | Standalone |
| **Use case** | Short text with AI assist | Long text with AI assist | Custom field needing AI trigger UI |

**Pick rule:** Attaching AI assist to a **single-line field**? → **AiInput**. **Multi-line field**? → **AiTextArea**. Using a **custom field** and need just the AI trigger UI? → **AiWidget** + wire it yourself.

---

## AudioPlayback vs AudioRecorder

| | AudioPlayback | AudioRecorder |
|---|---|---|
| **Direction** | Playback (read) | Recording (write) |
| **Input** | `url` (existing audio file) | Microphone |
| **Output** | None | `onFinalize` callback with audio blob |
| **Waveform** | Renders existing audio waveform | Renders live recording waveform |
| **Trim** | No | Yes (draggable trim region) |

**Pick rule:** Playing back an **existing audio file**? → **AudioPlayback**. **Capturing** audio from the user's mic? → **AudioRecorder**.

---

## Tooltip vs Popover vs Menu

All three show contextual UI near a trigger. The difference is **trigger mechanism and content type**.

| | Tooltip | Popover | Menu |
|---|---|---|---|
| **Trigger** | Hover | Click | Click |
| **Content** | Text label only | Custom JSX | List of action items |
| **Interactive content** | No | Yes | Yes (items) |
| **Dismiss** | Move cursor away | Outside click | Outside click or item click |

**Pick rule:**
- Show a **non-interactive label** on hover? → **Tooltip**
- Show **custom interactive content** on click? → **Popover**
- Show a **list of actions** on click? → **Menu**

---

## Collapsible vs Tabs

Both segment content, but the UX model differs.

| | Collapsible | Tabs |
|---|---|---|
| **Layout** | Vertical accordion | Horizontal (or vertical) tab bar |
| **Multiple open** | No (one at a time) | N/A (one active at a time by default) |
| **Navigation metaphor** | Expand in place | Switch view |
| **Use case** | FAQs, content with limited vertical space, progressive disclosure | Section switching where all sections are peers |

**Pick rule:** Content **expands inline** and only one section should be visible at a time? → **Collapsible**. Sections are **peer views** and switching between them is the primary interaction? → **Tabs**.

---

## Avatar vs AvatarRow

| | Avatar | AvatarRow |
|---|---|---|
| **Quantity** | Single user | Multiple users |
| **Overflow handling** | None | "+N more" indicator |
| **Selection** | `isSelected` prop | `onAvatarToggle` callback |
| **Use case** | Single user display (comments, profile) | "Members of team X" with overflow |

**Pick rule:** Showing **one user**? → **Avatar**. Showing a **group with overflow handling**? → **AvatarRow**.
