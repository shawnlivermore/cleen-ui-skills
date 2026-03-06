---
name: cleen-ui-feedback-and-errors
description: Display feedback, loading states, and errors using @cleen/cleen-components. Trigger this skill whenever implementing any loading UI, error states, empty states, success/failure toasts, or status indicators. This includes vague prompts like "show a loading state", "add a spinner", "display an error message", "show a success toast", "add skeleton screens", "handle loading", "show empty state", or "indicate progress". The primary purpose is to PREVENT hand-rolling spinners, custom toast logic, raw `<div>` placeholders, or alert boxes when the library already has all of this. Trigger on any request involving async data, form submission results, or state transitions.
---

# Feedback & Errors Skill

This skill covers every pattern that communicates system state to the user — loading, success, error, empty, and in-progress — using the cleen-components library.

---

## Prime Directive

> **Never hand-roll a spinner, toast, or skeleton. Never use `alert()` for user feedback.**

Common traps to avoid:
- Custom spinning div or CSS animation → use `Loader`
- `<div>` grey placeholder boxes → use `Skeletons`
- `alert()` or custom toast markup → use `showNotification`
- Raw `<p className="text-red-500">Error</p>` beneath a field → use `infoLabels.errorMessage` on the field, or `InfoLabels`
- Custom empty-state JSX from scratch → use a `Card` with descriptive content + a `Button` CTA

---

## Component Map

| Need | Component | Import |
|---|---|---|
| Spinning loading indicator | `Loader` | `@cleen/cleen-components` |
| Fullscreen loading overlay | `Loader` with `isFullscreen` | `@cleen/cleen-components` |
| Content placeholder while loading | `Skeletons` (see below) | `@cleen/cleen-components` |
| Conditional skeleton/content swap | `SkeletonWrapper` | `@cleen/cleen-components` |
| Success / error / info toast | `showNotification` | `@cleen/cleen-components` |
| Toast container (setup, once) | `CleenNotificationContainer` | `@cleen/cleen-components` |
| Inline field error message | `infoLabels.errorMessage` prop on field | — |
| Standalone info / error text | `InfoLabels` | `@cleen/cleen-components` |
| Status badge (active/inactive) | `PillBadge` + color | `@cleen/cleen-components` |
| Tooltip for contextual hints | `Tooltip` | `@cleen/cleen-components` |

---

## Loader

```tsx
import { Loader } from '@cleen/cleen-components';

// Inline spinner (default xs, primary color)
<Loader />

// Sized variants
<Loader size="sm" />
<Loader size="md" />
<Loader size="lg" />
<Loader size="xl" />
<Loader size={48} />        // custom pixel size

// Speed
<Loader speed="fast" />
<Loader speed="slow" />

// Custom color
<Loader size="lg" color="var(--cleen-success)" />

// Fullscreen blocking overlay (use while navigating or on initial page load)
<Loader isFullscreen />
```

**Inline loading inside a button** — use `Button`'s built-in `isLoading` prop instead:
```tsx
<Button label="Save" isLoading={isSaving} onClick={handleSave} />
```

---

## Skeletons

Always prefer a shaped skeleton over a generic spinner when you know the layout of what's loading. Pick the variant that matches the content type.

### Named variants (ready-to-use)

```tsx
import {
  SkeletonDataGrid,   // table with header + rows
  SkeletonCard,       // card with icon + title + subtitle
  SkeletonCard2,      // card variant 2
  SkeletonCard3,      // card variant 3
  SkeletonWidgetCard, // stat/KPI widget card
  SkeletonContentCard,// content-heavy card
  SkeletonInfoCard,   // info panel card
  SkeletonCardStack,  // vertical stack of cards
  SkeletonChart,      // chart placeholder
  SkeletonForm,       // label+input pairs + submit button
  SkeletonList,       // list of rows
  SkeletonParagraph,  // block of text lines
  SkeletonText,       // single text line
  SkeletonImage,      // image placeholder
  SkeletonVideo,      // video/media placeholder
  SkeletonAvatar,     // circular avatar
  SkeletonBadge,      // pill badge placeholder
  SkeletonButton,     // button placeholder
  SkeletonInput,      // input field placeholder
  SkeletonBanner,     // full-width banner
} from '@cleen/cleen-components';

// Usage
{isLoading ? <SkeletonDataGrid itemCount={8} /> : <DataGrid ... />}
{isLoading ? <SkeletonChart /> : <Chart ... />}
{isLoading ? <SkeletonForm itemCount={5} /> : <MyForm />}
```

**`itemCount` prop** — controls how many repeated rows/items to show (available on `SkeletonDataGrid`, `SkeletonForm`, `SkeletonList`, `SkeletonParagraph`). Defaults to 3–5 depending on the component.

**`variant` prop** — `'default'` (static grey) or `'pulse'` (animated shimmer). Use `'pulse'` for a more polished feel.

```tsx
<SkeletonCard variant="pulse" />
<SkeletonDataGrid variant="pulse" itemCount={6} />
```

### Skeleton — the primitive

Use `Skeleton` directly when no named variant fits. It's a single rectangle with configurable size and roundness.

```tsx
import { Skeleton } from '@cleen/cleen-components';

<Skeleton width="60%" height={20} roundness="md" variant="pulse" />
<Skeleton width={48} height={48} roundness="full" />  // avatar shape
<Skeleton height={200} roundness="xl" />              // full-width image block
```

### SkeletonWrapper — conditional swap

The cleanest pattern for toggling between skeleton and real content:

```tsx
import { SkeletonWrapper } from '@cleen/cleen-components';

<SkeletonWrapper skeleton="dataGrid" isShowing={isLoading} itemCount={5}>
  <DataGrid ... />
</SkeletonWrapper>

<SkeletonWrapper skeleton="widgetCard" isShowing={isLoading}>
  <StatCard ... />
</SkeletonWrapper>
```

Available `skeleton` values: `'card'` | `'card2'` | `'card3'` | `'cardStack'` | `'contentCard'` | `'infoCard'` | `'widgetCard'` | `'image'` | `'video'` | `'avatar'` | `'dataGrid'` | `'text'` | `'paragraph'` | `'list'` | `'form'` | `'banner'` | `'chart'` | `'button'` | `'badge'` | `'input'`

---

## Notifications (Toasts)

`showNotification` is a function — no JSX needed. Call it from event handlers or async callbacks.

### Setup (once, at app root)

`CleenNotificationContainer` must be mounted **once** in the app root. Without it, notifications won't render.

```tsx
// App.tsx or root layout
import { CleenNotificationContainer } from '@cleen/cleen-components';

export function App() {
  return (
    <>
      <CleenNotificationContainer />
      {/* rest of the app */}
    </>
  );
}
```

### Triggering notifications

```tsx
import { showNotification } from '@cleen/cleen-components';

// Variants
showNotification({ message: 'Profile saved.', variant: 'success' });
showNotification({ message: 'Something went wrong.', variant: 'error' });
showNotification({ message: 'Your session expires in 5 minutes.', variant: 'warning' });
showNotification({ message: 'New comment on your post.', variant: 'info' });
showNotification({ message: 'Update available.', variant: 'default' });

// After an async action
const handleSave = async () => {
  try {
    await saveProfile(form);
    showNotification({ message: 'Changes saved successfully.', variant: 'success' });
  } catch {
    showNotification({ message: 'Failed to save. Please try again.', variant: 'error' });
  }
};
```

### Position & animation

```tsx
showNotification({
  message: 'File uploaded.',
  variant: 'success',
  position: 'bottom-right',       // top-left | top-center | top-right | bottom-left | bottom-center | bottom-right
  animationType: 'bounce',        // slide (default) | bounce | zoom | flip
  autoClose: 3000,                // ms, or false to keep open
  hideProgressBar: true,
  pauseOnHover: true,
});
```

---

## Inline Field Errors

For form fields, always pass errors through the field's own `infoLabels` prop — not as separate text nodes. This ensures correct placement and `role="alert"` accessibility.

```tsx
// ✅ Correct — error renders below the field with role="alert"
<Input
  value={form.email}
  onChange={e => setField('email', e.target.value)}
  infoLabels={{ errorMessage: errors.email }}
/>

<Select
  options={roleOptions}
  value={selectedRole}
  onChange={setSelectedRole}
  infoLabels={{ errorMessage: errors.role, subtitle: 'Defines access level' }}
/>
```

`infoLabels` accepts three optional keys:
- `errorMessage` — shown in red with `role="alert"`
- `infoMessage` — shown in muted color for hints
- `subtitle` — secondary label (replaced by `maxLengthLabel` countdown when threshold is reached on `Input`)

For standalone error/info text outside a field, use `InfoLabels` directly:

```tsx
import { InfoLabels } from '@cleen/cleen-components';

<InfoLabels errorMessage="This section has errors. Please review." />
<InfoLabels infoMessage="Changes are auto-saved every 30 seconds." />
```

---

## Status Badges

Use `PillBadge` for any visual status indicator rather than raw coloured text or custom chips.

```tsx
import { PillBadge } from '@cleen/cleen-components';

// Semantic colors
<PillBadge label="Active" color="green" showDot />
<PillBadge label="Inactive" color="gray" showDot />
<PillBadge label="Pending" color="orange" showDot />
<PillBadge label="Error" color="red" showDot />
<PillBadge label="Info" color="blue" />

// Auto-cycle by index (consistent across renders)
<PillBadge label={row.category} colorByIndex={row.id} />
```

Available named colors: `blue` | `lighter-blue` | `primary` | `indigo` | `green` | `red` | `purple` | `pink` | `orange` | `gray`

---

## Tooltips for Hints & Contextual Help

Use `Tooltip` to explain unclear UI elements without cluttering the layout.

```tsx
import { Tooltip } from '@cleen/cleen-components';

<Tooltip label="This ID cannot be changed after creation" placement="top" hasArrow>
  <IconInfoCircle className="cleen-text-gray/40 cleen-cursor-help" />
</Tooltip>

// Delay opening to avoid flicker on quick mouse passes
<Tooltip label="Download as CSV" placement="bottom" openDelay={300}>
  <Button variant="borderless" leftIcon={<IconDownload />} label="" />
</Tooltip>
```

DON'T put interactive content (buttons, links, inputs) inside a `Tooltip` — use `Popover` for that.

---

## Patterns Summary

| Scenario | Pattern |
|---|---|
| Page/section loading | `SkeletonWrapper` or named skeleton matching content shape |
| Button action in progress | `Button` with `isLoading` |
| Fullscreen initial load | `Loader isFullscreen` |
| Async success feedback | `showNotification({ variant: 'success' })` |
| Async error feedback | `showNotification({ variant: 'error' })` |
| Form field validation error | `infoLabels.errorMessage` on the field component |
| Global form error | `InfoLabels errorMessage` above the submit button |
| Record status in a table | `PillBadge` with `color` or `colorByIndex` in `renderRow` |
| Unclear UI element explanation | `Tooltip` wrapping the element |
