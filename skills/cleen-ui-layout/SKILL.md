---
name: cleen-ui-layout
description: Build page layouts using @cleen/ui. Trigger this skill whenever implementing any page structure, content container, dashboard, settings panel, detail view, or any UI that needs to be laid out visually. This includes vague prompts like "build a dashboard", "create a profile page", "make a settings layout", "design a detail view", "add a stats section", "build a landing page section", or "organize this content into cards". The primary purpose is to PREVENT building layouts from scratch with raw divs, rolling custom card markup, or forgetting the `cleen-` prefix and `.cleen` scoping wrapper. Trigger on any request that involves visual page composition — cards, grids, headers, stat rows, detail panels, or any content organization.
---

# Layout Skill

This skill covers structuring pages and content containers using the @cleen/* packages and its Tailwind system. The fundamental unit is `Card`. Grids are built with Tailwind (prefixed). Everything lives inside a `.cleen` scope wrapper.

---

## Prime Directive

> **`Card` is the default container for any distinct content block. Reach for it first.**
> **Reuse library layout components within each other — avoid building wrapper divs or custom styled containers if the design of the layout component already provides the desired styling.**

Common traps to avoid:

- Custom `<div>` containers with hand-rolled border/shadow/padding → use `Card`
- Section wrapper div inside a Card that duplicates Card styling → nest `Card` inside `Card` or use `Divider` instead
- `<hr>` for section separators → use `Divider`
- Raw `<span>` with colored background for status tags → use `PillBadge`
- Custom CSS file to style a wrapper container → use `Card` or another layout component
- Non-prefixed Tailwind classes (`flex`, `grid`, `gap-4` in a consumer project) → use your project's default Tailwind config (not the library's `cleen-` prefixed classes, which are for the library's internal use only)
- Rolling a custom avatar or initials component → use `Avatar` / `AvatarRow`

---

## The Golden Rules

### 1. Use your project's Tailwind config, not library prefixes

When styling **your own components** in a consumer project, use Tailwind utilities from your project's configuration (unprefixed). The `cleen-` prefix is **for the library's internals only**.

```tsx
// ✅ In a consumer project — use unprefixed Tailwind
<div className="flex gap-4 grid grid-cols-3 p-6">

// ❌ Don't spread cleen- classes through your app
<div className="flex gap-4 grid grid-cols-3 p-6">
```

### 2. Prefer library components over custom styling

Instead of reaching for CSS files or custom Tailwind utilities, **reuse library layout components** (Card, Divider, PillBadge, etc.). This ensures visual consistency and reduces custom code.

```tsx
// ✅ Use library components
<Card header={{ title: 'Stats' }}>
  <div className="flex gap-4">
    <StatItem value={123} label="Total" />
    <StatItem value={45} label="Active" />
  </div>
</Card>

// ❌ Don't create wrapper divs that mimic Card
<div style={{ border: '1px solid #e5e7eb', padding: '24px', borderRadius: '8px' }}>
  {/* custom styling that duplicates Card functionality */}
</div>
```

---

## Styling Approach: Use Components, Not Custom CSS

**When you're tempted to create a custom CSS file or write stylesheet code, reach for a library component instead.**

### The Decision Tree

1. **"I need a container with padding, border, and shadow"** → `Card` (not a `<div>` with custom CSS)
2. **"I need to separate sections visually"** → `Divider` (not an `<hr>` or custom `border-top`)
3. **"I need a status indicator or label"** → `PillBadge` (not a `<span>` with background color)
4. **"I need to layout rows/columns with spacing"** → `Card` + Tailwind utilities from your project config (not custom layout CSS)
5. **"I need grid or flex layout"** → Use your project's Tailwind config (`flex`, `gap-4`, `grid`) \*inside\* Card or library components (not unprefixed classes at page root; not custom CSS)

### Anti-Pattern: Custom CSS for Component-Like Styling

```tsx
// ❌ Creating custom CSS for something the library provides
// styles.css
.section-container {
  padding: 24px;
  border: 1px solid #e5e7eb;
  border-radius: 8px;
  box-shadow: 0 1px 3px rgba(0, 0, 0, 0.1);
}

// App.tsx
<div className="section-container">
  <h2>Content</h2>
</div>

// ✅ Use Card instead
<Card header={{ title: 'Content' }}>
  {/* No custom CSS needed */}
</Card>
```

### When Custom CSS IS Appropriate

Create custom CSS **only** for truly unique visual styling that the library doesn't provide:
- Custom animations or transitions
- Brand-specific visual effects (gradients, glows, etc.)
- Component-specific styling that doesn't belong in Tailwind

When you do create custom CSS, **keep it minimal and isolated to that one component**. Use CSS variables (in RGB format) inherited from the library's color system.

---

## Component Reference

### Card

The primary building block for any distinct content section — metrics, forms, lists, charts, empty states, summaries. Nest Cards within Cards to create rich layouts without custom styling.

```tsx
import { Card } from '@cleen/ui';

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
  <p className="text-sm">This action is irreversible.</p>
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
- `p` — padding override in pixels (overrides the default card body padding)
- `gap` — gap override in pixels between header/children/footer zones

**Subcomponents:**

`CardIcon` — Card with a left-side icon holder:

```tsx
import { CardIcon } from '@cleen/ui';

<CardIcon
  icon={<MdShield size={20} />}
  header={{ content: 'Clearance level', hasDivider: true }}
>
  <p>Top Secret</p>
</CardIcon>;
```

`CardMedia` — Card with an image, video, or iframe in the header area:

```tsx
import { CardMedia } from '@cleen/ui';

<CardMedia
  media={{
    type: 'image',
    src: '/mission-briefing.jpg',
    alt: 'Mission briefing',
  }}
  header={{ title: 'Operation Shadow Moses', hasDivider: true }}
>
  <p className="text-sm text-gray/80">Infiltrate the facility...</p>
</CardMedia>;
```

---

### Divider

Thin separator line. Use inside `Card` to separate groups of rows, or between page sections.

```tsx
import { Divider } from '@cleen/ui';

// Horizontal (default) — full-width line
<Divider />

// Vertical — full-height line (use in flex rows)
<div className="flex h-8 items-center">
  <span>Section A</span>
  <Divider isHorizontal={false} className="mx-3" />
  <span>Section B</span>
</div>
```

---

### PillBadge

Compact badge for statuses, counts, categories, and tags.

```tsx
import { PillBadge } from '@cleen/ui';

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
import { Avatar } from '@cleen/ui';

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
import { AvatarRow } from '@cleen/ui';

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

Use `references/layout-default-patterns.md` for applications by default. It includes:

- `SaaS App Shell (Default)` — dashboard with left sidebar navigation
- `SaaS Home Screen (Default)` — welcome + journey tracking + quick actions
- `SaaS Dashboard Screen (Default)` — KPI strip + charts + activity table
- `SaaS Profile Settings Screen (Default)` — tabbed settings with form cards
- `SaaS Public Profile / Marketplace Screen` — public-facing profile + booking
- `SaaS Data List Screen (Events / Admin)` — filtered data tables
- `SaaS Knowledge Base Screen` — topic taxonomy + searchable content
- `SaaS Subsequential Data List Screen` — hierarchical data with drill-down navigation

Use `references/layout-patterns.md` only when the user explicitly requests a non-SaaS layout or a specialized pattern (detail pages, mixed-width grids, etc.). It contains 6 generic fallback recipes.
