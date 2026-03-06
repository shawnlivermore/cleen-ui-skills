---
name: cleen-ui-overlays
description: Build overlays and contextual UI using @cleen/cleen-components. Trigger this skill whenever implementing any overlay — modals, drawers, confirmation dialogs, dropdowns, context menus, tooltips, toasts, popovers, accordions, or filter panels. This includes vague prompts like "add a confirmation dialog", "show a side panel", "create a context menu", "add a tooltip", "show a success message", "make a filter panel", "add a collapsible section", or "trigger a popup". The primary purpose is to PREVENT rolling custom dialog/modal markup, hand-coded toast systems, bespoke dropdown implementations, or raw tooltip divs when the library covers all of these. Trigger on any request involving temporary, floating, or interruptive UI.
---

# Overlays Skill

This skill covers every temporary or floating UI surface — modals, drawers, menus, popovers, tooltips, notifications, and collapsible sections — using the cleen-components library. None of these should ever be built from scratch.

---

## Prime Directive

> **Never hand-roll a modal, drawer, dropdown, tooltip, toast, or collapsible. The library has them all.**

Common traps to avoid:

- Custom dialog/alert markup → use `Modal`
- Side panel with JSX from scratch → use `Drawer` or `FilterDrawer`
- `<ul>` context menu or action list → use `Menu`
- Custom dropdown with floating content → use `Dropdown` or `Popover`
- `title` attribute or custom tooltip div → use `Tooltip`
- `alert()`, `window.confirm()`, or custom toast markup → use `showNotification` or `Modal`
- Hand-rolled accordion/FAQ sections → use `Collapsible`

---

## Pick the Right Overlay

This is the most important decision. Read this table before reaching for any component.

| Scenario                                                             | Component          |
| -------------------------------------------------------------------- | ------------------ |
| Confirmation dialog, short form, alert that demands attention        | `Modal`            |
| Edit panel, record detail, step-through flow in a side panel         | `Drawer`           |
| Filter interface with save/apply/clear workflow                      | `FilterDrawer`     |
| Button that reveals custom panel content (date picker, tag selector) | `Dropdown`         |
| Right-click / action list popup with labelled items                  | `Menu`             |
| Floating panel attached to any custom trigger element                | `Popover`          |
| Hover hint, icon label, instructional text on hover                  | `Tooltip`          |
| Transient success / error / warning / info message                   | `showNotification` |
| Accordion / expandable sections                                      | `Collapsible`      |

For the three most commonly confused overlays, see the quick rule at the bottom of each section.

---

## State Management: useDisclosure

Every togglable overlay (Modal, Drawer, Popover, Dropdown) needs open/close state. Always use `useDisclosure` — never `useState(false)` for this.

```tsx
import { useDisclosure } from '@cleen/cleen-components';

const { isOpen, open, close, toggle } = useDisclosure();

<Button label="Open Modal" onClick={open} />
<Modal isOpen={isOpen} onClose={close}>
  ...
</Modal>
```

---

## Component Reference

### Modal

Centered, blocking overlay. Use for confirmations, short forms, alerts.

```tsx
import { Modal, Button, useDisclosure } from '@cleen/cleen-components';

const { isOpen, open, close } = useDisclosure();

<Button label="Delete Record" variant="danger" onClick={open} />

<Modal
  isOpen={isOpen}
  onClose={close}
  header={{ title: 'Delete record', hasDivider: true }}
  footer={{
    content: (
      <div className="cleen-flex cleen-justify-end cleen-gap-2">
        <Button variant="secondary" label="Cancel" onClick={close} />
        <Button variant="danger" label="Delete" onClick={handleDelete} isLoading={isDeleting} />
      </div>
    ),
    hasDivider: true,
  }}
  size="sm"
  closeOnOverlayClick
>
  <p className="cleen-text-gray-600">This action cannot be undone.</p>
</Modal>
```

**Key props:**

- `isOpen` / `onClose` — controlled state (always use `useDisclosure`)
- `header` — `{ title, content, hasDivider }` — use `title` for a plain string header
- `footer` — `{ content, hasDivider }` — render action buttons here
- `size` — `'xs'` | `'sm'` | `'md'` | `'lg'` | `'xl'` | `'2xl'` | number (px)
- `closeOnOverlayClick` — lets the user dismiss by clicking the backdrop
- `closeButtonIcon={null}` — hides the default ✕ button
- `p` / `gap` — shorthand padding and gap in pixels for the content area
- `zIndex` — override when stacking multiple overlays

**DO:**

- Use `footer` for action buttons, not children — it ensures consistent padding and alignment.
- Always provide an explicit close path (cancel button or `closeOnOverlayClick`).
- Set `size="sm"` for confirm dialogs, `size="lg"` or `size="xl"` for forms.

**DON'T:**

- Use Modal for complex, multi-section data editing — use `Drawer` instead.
- Stack multiple modals without incrementing `zIndex`.

---

### Drawer

Right-anchored slide-over panel. Use for editing, detailed views, step-through flows.

```tsx
import { Drawer, Button, useDisclosure } from '@cleen/cleen-components';

const { isOpen, open, close } = useDisclosure();

<Button label="Edit User" onClick={open} />

<Drawer
  isOpen={isOpen}
  onClose={close}
  title="Edit User"
  subtitle="Update the user's profile details"
  footer={
    <div className="cleen-flex cleen-justify-end cleen-gap-2">
      <Button variant="secondary" label="Cancel" onClick={close} />
      <Button variant="primary" label="Save Changes" onClick={handleSave} isLoading={isSaving} disabled={!isDirty} />
    </div>
  }
  size="md"
>
  {/* Form fields, detail content, etc. */}
</Drawer>
```

**Key props:**

- `title` / `subtitle` — header text (both accept `ReactNode`)
- `footer` — action buttons (render at the bottom of the drawer)
- `size` — `'xs'` | `'sm'` | `'md'` | `'lg'` | `'xl'` | `'2xl'` | `'3xl'` | number (px)
- `p` / `gap` — shorthand padding and gap for the content area
- `closeOnOverlayClick` — allows dismissal by clicking the overlay

**DO:**

- Use a `Drawer` when the user needs to see the underlying page context while editing.
- Always put action buttons in `footer`, not at the bottom of children.
- Pair with a form inside using `useForm` + `useValidation`.

**DON'T:**

- Use for quick confirmations — use `Modal` instead.

---

### FilterDrawer

Drawer pre-wired for filter UI. Has built-in Save / Apply / Cancel / Clear footer buttons, saved-filter management, and a two-step save-filter workflow.

```tsx
import {
  FilterDrawer,
  FormGroup,
  Select,
  useDisclosure,
} from '@cleen/cleen-components';

const { isOpen, open, close } = useDisclosure();

<FilterDrawer
  isOpen={isOpen}
  onClose={close}
  onApply={handleApply}
  onClear={handleClear}
  onCancel={close}
  savedFilters={savedFilters}
  onSave={handleSaveFilter}
  onFilterSelect={setSelectedFilter}
  selectedSavedFilter={selectedFilter}
>
  <FormGroup title="Status">
    <Select
      options={statusOptions}
      value={filters.status}
      onChange={v => setFilter('status', v)}
      isMulti
    />
  </FormGroup>
  <FormGroup title="Date range">
    <DatePicker
      value={filters.dateRange}
      onChange={v => setFilter('dateRange', v)}
      isRange
    />
  </FormGroup>
</FilterDrawer>;
```

**Key props:**

- Extends all `DrawerProps` — all Drawer props apply
- `onApply` — called when user clicks "Apply"
- `onClear` — called when user clicks "Clear" (reset filters to empty)
- `onCancel` — called when user clicks "Cancel" (close without applying)
- `onSave` — async callback `(newFilter) => Promise<void>` for saving named filters
- `savedFilters` / `selectedSavedFilter` — saved filter list and active selection
- `withSavedFilters={false}` — hide the saved filters dropdown if not needed
- `labels` — override all button/header text

**DO:**

- Put filter form fields as `children` wrapped in `FormGroup` rows.
- Handle `onApply` by re-fetching data with the new filter state.
- Use `withSavedFilters={false}` when saved filters aren't part of the product.

**DON'T:**

- Try to customize the footer buttons — they're intentionally opinionated.
- Use for general editing tasks — the footer workflow is filter-specific.

---

### Dropdown

A trigger button that reveals arbitrary content below it. Use for custom panels (date pickers, tag selectors, search panels).

```tsx
import { Dropdown } from '@cleen/cleen-components';

<Dropdown label="Assign to" fullWidth={false}>
  <div className="cleen-p-3 cleen-flex cleen-flex-col cleen-gap-2">
    {users.map(user => (
      <button key={user.id} onClick={() => assign(user)}>
        {user.name}
      </button>
    ))}
  </div>
</Dropdown>;
```

**Key props:**

- `label` — button label text
- `leftIcon` / `rightIcon` — icons on either side (chevron-down is the default right icon)
- `fullWidth` — trigger button fills container (default `true`)
- `fullWidthDropdown` — dropdown panel matches trigger width
- `keepOpenOnClickContent` — keep open when clicking inside (default `true`)
- `isOpen` / `onOpenChange` — controlled mode

**Quick rule: Dropdown vs Menu vs Popover**

- **List of action items with labels?** → `Menu`
- **Custom content opened by a styled button?** → `Dropdown`
- **Custom content opened by any custom trigger?** → `Popover`

---

### Menu

A popup list of clickable action items. Use for context menus, action dropdowns, kebab menus.

```tsx
import { Menu, Button } from '@cleen/cleen-components';
import { MdMoreVert } from 'react-icons/md';

<Menu
  items={[
    {
      type: 'button',
      label: 'Edit',
      icon: <MdEdit />,
      onClick: () => openEdit(),
    },
    {
      type: 'button',
      label: 'Duplicate',
      icon: <MdCopyAll />,
      onClick: () => duplicate(),
    },
    {
      type: 'button',
      label: 'Delete',
      icon: <MdDelete />,
      danger: true,
      onClick: () => confirmDelete(),
    },
  ]}
  position="bottom-right"
>
  <button className="cleen-p-1">
    <MdMoreVert size={20} />
  </button>
</Menu>;
```

**Key props:**

- `items` — array of `IMenuItem` (see types below)
- `position` — preferred placement: `'top'` | `'bottom'` | `'left'` | `'right'` | `'top-left'` | `'top-right'` | `'bottom-left'` | `'bottom-right'`
- `keepOpenOnClick` — keep menu open after item click (default `false`)
- `children` — the trigger element (any ReactNode)

**Item types:**

```tsx
// Button item (most common)
{ type: 'button', label: 'Edit', icon: <MdEdit />, onClick: handleEdit }
{ type: 'button', label: 'Delete', danger: true, onClick: handleDelete }
{ type: 'button', label: 'With submenu', submenu: [...nestedItems] }

// Router link item
{ type: 'link', label: 'View Profile', to: '/users/123', icon: <MdPerson /> }

// Visual group separator
{ type: 'group', label: 'Danger zone', items: [deleteItem] }
```

**DO:**

- Use `danger: true` for destructive actions — it applies red styling automatically.
- Group destructive items at the bottom with `type: 'group'`.
- Use `type: 'link'` items for navigation (integrates with React Router).

**DON'T:**

- Use Menu for non-list content — use `Dropdown` or `Popover` instead.

---

### Popover

A floating panel triggered by click on any element. Use when you need full control over both trigger and content.

```tsx
import { Popover } from '@cleen/cleen-components';

<Popover
  content={
    <div className="cleen-p-4 cleen-w-64">
      <p className="cleen-font-medium cleen-mb-2">Color picker</p>
      {/* custom color swatches */}
    </div>
  }
  position="bottom-left"
>
  <div
    className="cleen-w-8 cleen-h-8 cleen-rounded-full cleen-cursor-pointer"
    style={{ background: selectedColor }}
  />
</Popover>;
```

**Key props:**

- `content` — the floating panel content (full `ReactNode`)
- `position` — preferred placement (same 8-position options as Menu)
- `children` — the trigger element
- `isOpen` / `onOpenChange` — controlled mode

---

### Tooltip

Hover-activated label for UI elements. Use for icon buttons, truncated text, or any element that needs a hint.

```tsx
import { Tooltip } from '@cleen/cleen-components';

// Icon button tooltip
<Tooltip label="Delete record" placement="top">
  <button onClick={handleDelete}>
    <MdDelete />
  </button>
</Tooltip>

// Delayed tooltip (prevents flicker on fast mouse movement)
<Tooltip label="This metric compares..." placement="bottom" openDelay={400}>
  <span className="cleen-underline cleen-decoration-dotted">Revenue</span>
</Tooltip>
```

**Key props:**

- `label` — tooltip content (string or `ReactNode`)
- `placement` — `'top'` (default) | `'bottom'` | `'left'` | `'right'` | `'top-left'` etc.
- `openDelay` — milliseconds before showing (use 200–400 to avoid flicker on fast hovers)
- `hasArrow` — show directional arrow pointing to the trigger
- `invertColors` — dark-on-light color scheme
- `isDisabled` — never show (useful for conditional hints)
- `offset` — gap between tooltip and trigger in pixels (default 8)

**DO:**

- Use `openDelay` for tooltips attached to frequently-hovered elements.
- Use `hasArrow` when the tooltip position needs to be spatially clear.
- Use `isDisabled` to suppress a tooltip when the element is in an active/selected state.

**DON'T:**

- Use Tooltip for interactive content (links, buttons inside) — use `Popover` instead.
- Use the native `title` HTML attribute — it's unstyled and inconsistent across browsers.

---

### Notification (Toast)

Function-based transient feedback messages. Call `showNotification()` from event handlers — it is not a rendered component.

```tsx
import {
  showNotification,
  CleenNotificationContainer,
} from '@cleen/cleen-components';

// Setup: render ONCE at the app root (e.g. App.tsx or layout)
<CleenNotificationContainer />;

// Usage anywhere in event handlers:
showNotification({ message: 'Profile saved successfully', variant: 'success' });
showNotification({ message: 'Failed to save changes', variant: 'error' });
showNotification({
  message: 'Please fill all required fields',
  variant: 'warning',
});
showNotification({ message: 'A new version is available', variant: 'info' });
```

**Key options:**

- `message` — notification content (`ReactNode`)
- `variant` — `'success'` | `'error'` | `'warning'` | `'info'` | `'default'`
- `position` — `'top-right'` (default) | `'top-left'` | `'top-center'` | `'bottom-right'` | `'bottom-left'` | `'bottom-center'`
- `autoClose` — milliseconds before auto-dismiss (default: 5000), or `false` for persistent
- `animationType` — `'bounce'` | `'zoom'` | `'slide'` | `'flip'`

**DO:**

- Place `<CleenNotificationContainer />` once at the app root — notifications won't render without it.
- Use `variant: 'success'` after form saves, `variant: 'error'` after failed API calls.
- Use `autoClose: false` for critical errors that require user acknowledgment.

**DON'T:**

- Use `showNotification` for important confirmations (e.g., before deleting data) — use `Modal`.
- Replace inline field validation errors with a toast — use `infoLabels.errorMessage` on the field itself.

---

### Collapsible

Accordion-style expandable sections. Use for FAQs, categorized settings, nested navigation lists.

```tsx
import { Collapsible } from '@cleen/cleen-components';

<Collapsible
  defaultOpenIndex={0}
  sections={[
    {
      title: 'Account settings',
      items: [
        { content: <SettingsRow label="Display name" />, onClick: () => {} },
        { content: <SettingsRow label="Email address" />, onClick: () => {} },
      ],
    },
    {
      title: 'Notifications',
      items: [
        { content: <SettingsRow label="Email notifications" /> },
        { content: <SettingsRow label="Push notifications" /> },
      ],
    },
  ]}
/>;
```

**Key props:**

- `sections` — array of `{ title, items?, onClick?, key? }`
- `items` — array of `{ content, onClick?, key? }` — rendered inside when section is expanded
- `defaultOpenIndex` — which section index to open by default (`null` = all collapsed)
- Sections without `items` render as plain clickable rows (no expand/collapse toggle)

**DO:**

- Use `key` on sections and items when data is dynamic (avoids React reconciliation issues).
- Use section-level `onClick` (without items) for navigation-list patterns (sidebar sub-items).

**DON'T:**

- Use for multi-select expansion — only one section can be open at a time.
- Use when users need to compare content across sections — `Tabs` is better there.

---

## Recipes

For full working patterns — edit drawers with forms, confirmation modals, filter drawers wired to a DataGrid, and more — see `references/overlay-patterns.md`.
