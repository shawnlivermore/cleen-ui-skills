---
name: cleen-ui-layout
description: Build page layouts using @cleen/cleen-components. Trigger this skill whenever implementing any page structure, content container, dashboard, settings panel, detail view, or any UI that needs to be laid out visually. This includes vague prompts like "build a dashboard", "create a profile page", "make a settings layout", "design a detail view", "add a stats section", "build a landing page section", or "organize this content into cards". The primary purpose is to PREVENT building layouts from scratch with raw divs, rolling custom card markup, or forgetting the `cleen-` prefix and `.cleen` scoping wrapper. Trigger on any request that involves visual page composition — cards, grids, headers, stat rows, detail panels, or any content organization.
---

# Layout Skill

This skill covers structuring pages and content containers using the cleen-components library and its Tailwind system. The fundamental unit is `Card`. Grids are built with Tailwind (prefixed). Everything lives inside a `.cleen` scope wrapper.

---

## Prime Directive

> **`Card` is the default container for any distinct content block. Reach for it first.**

Common traps to avoid:

- Custom `<div>` containers with hand-rolled border/shadow/padding → use `Card`
- `<hr>` for section separators → use `Divider`
- Raw `<span>` with colored background for status tags → use `PillBadge`
- Non-prefixed Tailwind classes (`flex`, `grid`, `gap-4`) → always `cleen-flex`, `cleen-grid`, `cleen-gap-4`
- Forgetting the `.cleen` scope wrapper on the page root → everything breaks without it
- Rolling a custom avatar or initials component → use `Avatar` / `AvatarRow`

---

## The Golden Rules

### 1. Every page root needs `.cleen`

```tsx
// ✅ Correct
<div className="cleen cleen-p-6 cleen-min-h-screen">
  ...
</div>

// ❌ Wrong — no scoping wrapper, no styles will apply
<div className="cleen-p-6 cleen-min-h-screen">
  ...
</div>
```

### 2. All Tailwind classes take the `cleen-` prefix

```tsx
// ✅
<div className="cleen-flex cleen-gap-4 cleen-grid cleen-grid-cols-3">

// ❌
<div className="flex gap-4 grid grid-cols-3">
```

### 3. Responsive breakpoints also get the prefix

```tsx
<div className="cleen-grid cleen-grid-cols-1 md:cleen-grid-cols-2 lg:cleen-grid-cols-4 cleen-gap-4">
```

---

## Component Reference

### Card

The primary building block for any distinct content section — metrics, forms, lists, charts, empty states, summaries.

```tsx
import { Card } from '@cleen/cleen-components';

// Minimal
<Card>
  <p>Content here</p>
</Card>

// With header and footer
<Card
  header={{ title: 'Team members', hasDivider: true }}
  footer={{
    hasDivider: true,
    content: <Button variant="secondary" label="View all" />,
  }}
>
  <AvatarRow avatars={teamMembers} maxVisible={5} />
</Card>

// Color-tinted (useful for status cards, alert panels)
<Card color="var(--cleen-error)">
  <p className="cleen-text-sm">This action is irreversible.</p>
</Card>

// Hoverable (use for clickable cards in a grid)
<Card hoverable onClick={() => navigate(`/items/${item.id}`)}>
  ...
</Card>

// Custom padding / gap
<Card p={16} gap={8}>
  ...
</Card>
```

**Key props:**

- `header` — `{ title?, content?, hasDivider? }` — full header zone (use `title` for a plain string, `content` for custom JSX)
- `footer` — `{ content?, hasDivider? }` — footer zone; common for action buttons or pagination
- `color` — CSS color string or CSS variable; tints the background and border automatically
- `isGlass` — frosted-glass gradient overlay
- `hoverable` — adds hover shadow/border transition for clickable cards
- `p` — padding override in pixels (overrides the default `cleen-p-6`)
- `gap` — gap override in pixels between header/children/footer zones

**Subcomponents:**

`CardIcon` — Card with a left-side icon holder:

```tsx
import { CardIcon } from '@cleen/cleen-components';

<CardIcon
  icon={<MdShield size={20} />}
  header={{ content: 'Clearance level', hasDivider: true }}
>
  <p>Top Secret</p>
</CardIcon>;
```

`CardMedia` — Card with an image, video, or iframe in the header area:

```tsx
import { CardMedia } from '@cleen/cleen-components';

<CardMedia
  media={{
    type: 'image',
    src: '/mission-briefing.jpg',
    alt: 'Mission briefing',
  }}
  header={{ title: 'Operation Shadow Moses', hasDivider: true }}
>
  <p className="cleen-text-sm cleen-text-gray/80">Infiltrate the facility...</p>
</CardMedia>;
```

---

### Divider

Thin separator line. Use inside `Card` to separate groups of rows, or between page sections.

```tsx
import { Divider } from '@cleen/cleen-components';

// Horizontal (default) — full-width line
<Divider />

// Vertical — full-height line (use in flex rows)
<div className="cleen-flex cleen-h-8 cleen-items-center">
  <span>Section A</span>
  <Divider isHorizontal={false} className="cleen-mx-3" />
  <span>Section B</span>
</div>
```

---

### PillBadge

Compact badge for statuses, counts, categories, and tags.

```tsx
import { PillBadge } from '@cleen/cleen-components';

// Status badge
<PillBadge label="Active" color="green" showDot />
<PillBadge label="Inactive" color="gray" showDot />
<PillBadge label="Critical" color="red" />

// Count badge (common in headers)
<PillBadge label={pendingCount} color="blue" variant="rounded" />

// Trend indicator (common in KPI cards)
<PillBadge label="+12.4%" color="green" />
<PillBadge label="-3.1%" color="red" />

// Removable tag
<PillBadge label="Infiltration" color="purple" removeable onRemoveClicked={() => removeTag('Infiltration')} />
```

**Key props:**

- `color` — `'primary'` | `'blue'` | `'green'` | `'red'` | `'purple'` | `'pink'` | `'orange'` | `'gray'` | any CSS color
- `variant` — `'rounded'` (default) | `'full'` | `'semiRounded'` | `'sleek'`
- `showDot` — colored status dot to the left of the label
- `icon` — icon to the left of the label
- `removeable` / `onRemoveClicked` — × button for tag removal
- `colorByIndex` — cycles through the color palette by index; useful for auto-coloring tag lists

---

### Avatar

User profile picture with initials fallback.

```tsx
import { Avatar } from '@cleen/cleen-components';

// From image
<Avatar src={user.avatarUrl} alt={user.name} size="md" />

// Initials fallback
<Avatar name="Solid Snake" size="lg" />

// Selected state (e.g., in an assignee picker)
<Avatar name="Meryl Silverburgh" isSelected={selectedId === user.id} onClick={() => select(user.id)} />
```

**Sizes:** `'xs'` (24px) | `'sm'` (28px) | `'md'` (36px) | `'lg'` (44px) | `'xl'` (56px) | custom number

---

### AvatarRow

Stacked row of avatars with overflow count. Common in cards showing assignees, participants, or team members.

```tsx
import { AvatarRow } from '@cleen/cleen-components';

<AvatarRow
  avatars={[
    { name: 'Solid Snake', src: '/avatars/snake.jpg' },
    { name: 'Meryl Silverburgh' },
    { name: 'Otacon' },
    { name: 'Raiden' },
    { name: 'Colonel Roy Campbell' },
  ]}
  maxVisible={3}
  size="sm"
/>;
// Renders: 3 avatars + "+2" overflow badge
```

**Key props:**

- `avatars` — array of `AvatarProps`
- `maxVisible` — cap before showing the `+N` overflow badge
- `selectedAvatars` — array of avatar IDs to highlight with primary border
- `onAvatarToggle(id)` — callback for toggling avatar selection (use for assignee pickers)
- `size` — applies to all avatars in the row

---

## Layout Recipes

For complete page layouts — dashboards, settings, detail pages, KPI stat rows, list pages — see `references/layout-patterns.md`.
