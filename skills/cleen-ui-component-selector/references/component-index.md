# Component Index

A categorical listing of all components in `@cleen/cleen-components`. Use this for a quick scan before narrowing down to the right component.

> For ambiguous picks between similar components, see `decision-guide.md`.

---

## Table of Contents

1. [Inputs & Forms](#inputs--forms)
2. [Navigation & Layout](#navigation--layout)
3. [Overlays & Contextual UI](#overlays--contextual-ui)
4. [Data Display](#data-display)
5. [Progress & Feedback](#progress--feedback)
6. [Media & Rich Input](#media--rich-input)
7. [Charts](#charts)
8. [Icons & Layout Utilities](#icons--layout-utilities)

---

## Inputs & Forms

| Component | Import | One-liner |
|---|---|---|
| `Button` | `@cleen/cleen-components` | Action trigger with variants, icons, and loading state |
| `Input` | `@cleen/cleen-components` | Single-line text field with label, icons, and character count |
| `TextArea` | `@cleen/cleen-components` | Auto-resizing multi-line text field |
| `AiInput` | `@cleen/cleen-components` | Input enhanced with AI generation trigger |
| `AiTextArea` | `@cleen/cleen-components` | TextArea enhanced with AI generation trigger |
| `AiWidget` | `@cleen/cleen-components` | Standalone AI trigger/confirm/cancel control |
| `Select` | `@cleen/cleen-components` | Dropdown select (single or multi) from a static option list |
| `Lookup` | `@cleen/cleen-components` | Async searchable dropdown with debounce |
| `Checkbox` | `@cleen/cleen-components` | Single boolean toggle with indeterminate state support |
| `CheckboxGroup` | `@cleen/cleen-components` | Managed list of labelled checkboxes |
| `Switch` | `@cleen/cleen-components` | Binary on/off toggle, preferred for settings |
| `RadioButtonGroup` | `@cleen/cleen-components` | Mutually exclusive radio buttons with text labels |
| `RadioBoxGroup` | `@cleen/cleen-components` | Selectable card-style option boxes with rich content |
| `DatePicker` | `@cleen/cleen-components` | Calendar popover for single date or date-range selection |
| `CreditCardInput` | `@cleen/cleen-components` | Full credit card form with number/expiry/CVC and card art |
| `Slider` | `@cleen/cleen-components` | Single-handle numeric slider |
| `RangeSlider` | `@cleen/cleen-components` | Dual-handle slider for selecting a min/max interval |
| `FormGroup` | `@cleen/cleen-components` | Two-column layout row pairing a label with form controls |
| `InfoLabels` | `@cleen/cleen-components` | Below-field messages: info, error (with `role="alert"`), subtitle |

---

## Navigation & Layout

| Component | Import | One-liner |
|---|---|---|
| `Tabs` | `@cleen/cleen-components` | Selectable tabs for switching between content sections |
| `Breadcrumb` | `@cleen/cleen-components` | Page hierarchy trail with router-aware links |
| `Sidebar` | `@cleen/cleen-components` | App-level side navigation panel with icon + label items |
| `Stepper` | `@cleen/cleen-components` | Visual step-progress indicator (horizontal or vertical) |
| `Wizard` | `@cleen/cleen-components` | Full multi-step flow: stepper + navigation + content rendering |
| `Pagination` | `@cleen/cleen-components` | Page controls with optional page-size selector and go-to-page |
| `Divider` | `@cleen/cleen-components` | Thin horizontal or vertical separator line |

---

## Overlays & Contextual UI

| Component | Import | One-liner |
|---|---|---|
| `Modal` | `@cleen/cleen-components` | Centered portal dialog for focused tasks or confirmations |
| `Drawer` | `@cleen/cleen-components` | Right-anchored slide-over panel for contextual detail or editing |
| `FilterDrawer` | `@cleen/cleen-components` | Filter-specialized drawer with save/apply/clear footer |
| `Dropdown` | `@cleen/cleen-components` | Button revealing arbitrary content beneath it |
| `Menu` | `@cleen/cleen-components` | Click-triggered popup list of clickable items |
| `Popover` | `@cleen/cleen-components` | Click-triggered floating panel with custom content |
| `Tooltip` | `@cleen/cleen-components` | Hover-triggered label for explaining UI elements |
| `Notification` | `@cleen/cleen-components` | Function-based toast for brief feedback messages |
| `Collapsible` | `@cleen/cleen-components` | Accordion list of expandable/collapsible sections |

---

## Data Display

| Component | Import | One-liner |
|---|---|---|
| `DataGrid` | `@cleen/cleen-components` | Full table with sorting, search, filters, drag-and-drop rows |
| `DataGridWithFilters` | `@cleen/cleen-components` | DataGrid pre-wired with FilterDrawer |
| `KanbanBoard` | `@cleen/cleen-components` | Drag-and-drop Kanban in card-grid layout |
| `KanbanList` | `@cleen/cleen-components` | Drag-and-drop Kanban in row-list layout |
| `KanbanBlocks` | `@cleen/cleen-components` | Individual Kanban sub-components for custom layouts |
| `Card` | `@cleen/cleen-components` | General-purpose content container with header/footer/media |
| `Avatar` | `@cleen/cleen-components` | User avatar from image or initials fallback |
| `AvatarRow` | `@cleen/cleen-components` | Stacked row of avatars with "+N" overflow indicator |
| `PillBadge` | `@cleen/cleen-components` | Compact badge for statuses, tags, and counts |
| `GroupSelector` | `@cleen/cleen-components` | Hierarchical group → item selector with create/edit/search |
| `Assessment` | `@cleen/cleen-components` | Interactive assessment form with sections, steps, and sidebar nav |
| `Skeletons` | `@cleen/cleen-components` | 20+ loading placeholder variants (card, grid, avatar, button…) |

---

## Progress & Feedback

| Component | Import | One-liner |
|---|---|---|
| `ProgressBar` | `@cleen/cleen-components` | Horizontal progress bar with optional title and percentage |
| `AdvancedProgressBar` | `@cleen/cleen-components` | Multi-bar overlapping progress track with clamp markers |
| `ProgressCircle` | `@cleen/cleen-components` | Animated SVG donut/circle progress indicator |
| `Loader` | `@cleen/cleen-components` | Spinning loading indicator with optional fullscreen overlay |

---

## Media & Rich Input

| Component | Import | One-liner |
|---|---|---|
| `AudioPlayback` | `@cleen/cleen-components` | Audio player with waveform visualization (wavesurfer.js) |
| `AudioRecorder` | `@cleen/cleen-components` | Microphone recorder with waveform and draggable trim region |

---

## Charts

| Component | Import | One-liner |
|---|---|---|
| `Chart` | `@cleen/cleen-components/charts` | Full ApexCharts wrapper: line, bar, pie, area, scatter, radar, etc. |
| `SimpleChart` | `@cleen/cleen-components/charts` | Minimal SVG line sparkline for compact trend display |

### Chart Variants (ApexCharts type values)

`BellCurve`, `RadarChart`, `ScatterChart` are pre-configured variants inside the `Chart` component — pass the appropriate `type` prop or use the exported sub-components from `@cleen/cleen-components/charts`.

---

## Icons & Layout Utilities

| Component | Import | One-liner |
|---|---|---|
| `CleenIcon` | `@cleen/cleen-components` | Renders any react-icons icon by name string |
| `IconFromLibrary` | `@cleen/cleen-components/icons` | Icon picker/browser component for the icon library |
| `getIconByName` | `@cleen/cleen-components/icons` | Utility to resolve a react-icons component from a name string |

---

## Hooks (exported utilities)

These hooks from `@cleen/cleen-components` are useful when building with or extending components:

| Hook | Purpose |
|---|---|
| `useAnimateNumber` | Smooth numeric animation from start to target value |
| `useControlled` | Managed controlled/uncontrolled state pattern |
| `useDebounce` | Debounce a value by a delay in ms |
| `useDisclosure` | Simple open/close/toggle state for modals, drawers, dropdowns |
| `useForm` | Lightweight form state with dirty tracking and reset |
| `useOutsideClick` | Fire callback on click outside a referenced element |
| `usePaginationState` | Pagination state with page/pageSize and navigation helpers |
| `usePositionClose` | Fixed-position overlay placement with viewport-overflow avoidance |
| `useValidation` | Form field validation utilities |
