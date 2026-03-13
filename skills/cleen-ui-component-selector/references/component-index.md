# Component Index

A categorical listing of all components in `@cleen/ui`. Use this for a quick scan before narrowing down to the right component.

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
| `Button` | `@cleen/ui` | Action trigger with variants, icons, and loading state |
| `Input` | `@cleen/ui` | Single-line text field with label, icons, and character count |
| `TextArea` | `@cleen/ui` | Auto-resizing multi-line text field |
| `AiInput` | `@cleen/ui-pro` | Input enhanced with AI generation trigger |
| `AiTextArea` | `@cleen/ui-pro` | TextArea enhanced with AI generation trigger |
| `AiWidget` | `@cleen/ui-pro` | Standalone AI trigger/confirm/cancel control |
| `Select` | `@cleen/ui` | Dropdown select (single or multi) from a static option list |
| `Lookup` | `@cleen/ui` | Async searchable dropdown with debounce |
| `Checkbox` | `@cleen/ui` | Single boolean toggle with indeterminate state support |
| `CheckboxGroup` | `@cleen/ui` | Managed list of labelled checkboxes |
| `Switch` | `@cleen/ui` | Binary on/off toggle, preferred for settings |
| `RadioButtonGroup` | `@cleen/ui` | Mutually exclusive radio buttons with text labels |
| `RadioBoxGroup` | `@cleen/ui` | Selectable card-style option boxes with rich content |
| `DatePicker` | `@cleen/ui` | Calendar popover for single date or date-range selection |
| `CreditCardInput` | `@cleen/ui` | Full credit card form with number/expiry/CVC and card art |
| `Slider` | `@cleen/ui` | Single-handle numeric slider |
| `RangeSlider` | `@cleen/ui` | Dual-handle slider for selecting a min/max interval |
| `FormGroup` | `@cleen/ui` | Two-column layout row pairing a label with form controls |
| `InfoLabels` | `@cleen/ui` | Below-field messages: info, error (with `role="alert"`), subtitle |

---

## Navigation & Layout

| Component | Import | One-liner |
|---|---|---|
| `Tabs` | `@cleen/ui` | Selectable tabs for switching between content sections |
| `Breadcrumb` | `@cleen/ui` | Page hierarchy trail with router-aware links |
| `Sidebar` | `@cleen/ui` | App-level side navigation panel with icon + label items |
| `Stepper` | `@cleen/ui` | Visual step-progress indicator (horizontal or vertical) |
|  |  | Full multi-step flow: stepper + navigation + content rendering |
| `Pagination` | `@cleen/ui` | Page controls with optional page-size selector and go-to-page |
| `Divider` | `@cleen/ui` | Thin horizontal or vertical separator line |

---

## Overlays & Contextual UI

| Component | Import | One-liner |
|---|---|---|
| `Modal` | `@cleen/ui` | Centered portal dialog for focused tasks or confirmations |
| `Drawer` | `@cleen/ui` | Right-anchored slide-over panel for contextual detail or editing |
| `FilterDrawer` | `@cleen/ui` | Filter-specialized drawer with save/apply/clear footer |
| `Dropdown` | `@cleen/ui` | Button revealing arbitrary content beneath it |
| `Menu` | `@cleen/ui` | Click-triggered popup list of clickable items |
| `Popover` | `@cleen/ui` | Click-triggered floating panel with custom content |
| `Tooltip` | `@cleen/ui` | Hover-triggered label for explaining UI elements |
| `Notification` | `@cleen/ui` | Function-based toast for brief feedback messages |
| `Collapsible` | `@cleen/ui` | Accordion list of expandable/collapsible sections |

---

## Data Display

| Component | Import | One-liner |
|---|---|---|
|  |  | Full table with sorting, search, filters, drag-and-drop rows |
|  |  | DataGrid pre-wired with FilterDrawer |
| `KanbanBoard` | `@cleen/ui` | Drag-and-drop Kanban in card-grid layout |
| `KanbanList` | `@cleen/ui` | Drag-and-drop Kanban in row-list layout |
| `KanbanBlocks` | `@cleen/ui` | Individual Kanban sub-components for custom layouts |
| `Card` | `@cleen/ui` | General-purpose content container with header/footer/media |
| `Avatar` | `@cleen/ui` | User avatar from image or initials fallback |
| `AvatarRow` | `@cleen/ui` | Stacked row of avatars with "+N" overflow indicator |
| `PillBadge` | `@cleen/ui` | Compact badge for statuses, tags, and counts |
| `GroupSelector` | `@cleen/ui` | Hierarchical group → item selector with create/edit/search |
|  |  | Interactive assessment form with sections, steps, and sidebar nav |
| `Skeletons` | `@cleen/ui` | 20+ loading placeholder variants (card, grid, avatar, button…) |

---

## Progress & Feedback

| Component | Import | One-liner |
|---|---|---|
| `ProgressBar` | `@cleen/ui` | Horizontal progress bar with optional title and percentage |
| `AdvancedProgressBar` | `@cleen/ui` | Multi-bar overlapping progress track with clamp markers |
| `ProgressCircle` | `@cleen/ui` | Animated SVG donut/circle progress indicator |
| `Loader` | `@cleen/ui` | Spinning loading indicator with optional fullscreen overlay |

---

## Media & Rich Input

| Component | Import | One-liner |
|---|---|---|
| `AudioPlayback` | `@cleen/ui` | Audio player with waveform visualization (wavesurfer.js) |
| `AudioRecorder` | `@cleen/ui` | Microphone recorder with waveform and draggable trim region |

---

## Charts

| Component | Import | One-liner |
|---|---|---|
| `Chart` | `@cleen/ui/charts` | Full ApexCharts wrapper: line, bar, pie, area, scatter, radar, etc. |
| `SimpleChart` | `@cleen/ui/charts` | Minimal SVG line sparkline for compact trend display |

### Chart Variants (ApexCharts type values)

`BellCurve`, `RadarChart`, `ScatterChart` are pre-configured variants inside the `Chart` component — pass the appropriate `type` prop or use the exported sub-components from `@cleen/ui/charts`.

---

## Icons & Layout Utilities

| Component | Import | One-liner |
|---|---|---|
| `CleenIcon` | `@cleen/ui` | Renders any react-icons icon by name string |
| `IconFromLibrary` | `@cleen/ui/icons` | Icon picker/browser component for the icon library |
| `getIconByName` | `@cleen/ui/icons` | Utility to resolve a react-icons component from a name string |

---

## Hooks (exported utilities)

These hooks from `@cleen/ui` are useful when building with or extending components:

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
