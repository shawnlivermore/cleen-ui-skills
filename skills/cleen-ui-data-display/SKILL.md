---
name: cleen-ui-data-display
description: Display data using @cleen/cleen-components. Trigger this skill whenever implementing any data-heavy UI — tables, grids, lists, Kanban boards, dashboards, stat cards, charts, progress indicators, status badges, or loading skeletons. This includes vague prompts like "show a list of users", "build a dashboard", "create a Kanban", "display analytics", "add a stats section", "show loading state", or "visualize metrics". The primary purpose is to PREVENT hand-rolling tables, custom card layouts, custom charts, or bare `<ul>/<li>` lists when the library already has purpose-built components for these. Trigger on any feature or page request that shows collections of data.
---

# Data Display Skill

This skill covers rendering data — lists, tables, boards, charts, badges, progress, and loading states — using the cleen-components library. Do not hand-roll any of these patterns.

---

## Prime Directive

> **If you're about to write a `<table>`, `<ul>`, a custom chart, or a spinner div — stop and use a component instead.**

Common traps to avoid:
- Custom HTML table → use `DataGrid` or `DataGridWithFilters`
- Raw `<ul>/<li>` card list → use `Card` + flex/grid or `DataGrid`
- Hand-rolled Kanban columns → use `KanbanBoard` or `KanbanList`
- Custom chart with D3/recharts → use `Chart` (ApexCharts) or `SimpleChart`
- Custom spinner / loading div → use `Loader` or `Skeletons`
- Raw `<span>` status labels → use `PillBadge`
- Custom `<progress>` element → use `ProgressBar` or `ProgressCircle`

---

## Component Map

| Need | Component | Import |
|---|---|---|
| Tabular row data | `DataGrid` | `@cleen/cleen-components` |
| Table + filter drawer | `DataGridWithFilters` | `@cleen/cleen-components` |
| Kanban (card grid) | `KanbanBoard` | `@cleen/cleen-components` |
| Kanban (row list) | `KanbanList` | `@cleen/cleen-components` |
| Content container | `Card` | `@cleen/cleen-components` |
| User avatar | `Avatar` / `AvatarRow` | `@cleen/cleen-components` |
| Status tag / label | `PillBadge` | `@cleen/cleen-components` |
| Horizontal progress | `ProgressBar` | `@cleen/cleen-components` |
| Overlapping progress | `AdvancedProgressBar` | `@cleen/cleen-components` |
| Circular KPI | `ProgressCircle` | `@cleen/cleen-components` |
| Full interactive chart | `Chart` | `@cleen/cleen-components/charts` |
| Sparkline / trend | `SimpleChart` | `@cleen/cleen-components/charts` |
| Loading spinner | `Loader` | `@cleen/cleen-components` |
| Loading placeholder | `Skeletons` (see below) | `@cleen/cleen-components` |

---

## DataGrid

The go-to for any tabular data. Every row must have a unique `id` field.

```tsx
import { DataGrid } from '@cleen/cleen-components';
import type { TableHeaderProps } from '@cleen/cleen-components';

interface UserRow extends Record<string, unknown> {
  id: number;
  name: string;
  email: string;
  role: string;
  status: string;
}

const headers: TableHeaderProps<UserRow>[] = [
  { field: 'name', headerElement: 'Name', sortable: true },
  { field: 'email', headerElement: 'Email' },
  { field: 'role', headerElement: 'Role', width: 150 },
  { field: 'status', headerElement: 'Status', width: 120, align: 'center' },
];

<DataGrid
  title="Users"
  tableHeaders={headers}
  rowData={users}
  withSearch
  withFooter
  totalPages={totalPages}
  activePage={page}
  onPageChange={setPage}
  onRowClick={(row) => openEditModal(row)}
  isLoading={isLoading}
/>
```

**Key props at a glance:**
- `renderRow` — override how specific cells render (badges, avatars, actions)
- `threeDotContextMenuOptions` — per-row action menu (edit, delete, etc.)
- `withSearch` — adds a search input in the header
- `withFilters` — adds a filter button (wire your own filter UI via `customFilterElements`)
- `withFooter` — shows pagination controls
- `isDraggable` — enables drag-to-reorder rows
- `spacing` — `'loose'` | `'moderate'` | `'tight'` | `'forensic'`
- `isLoading` / `isError` — built-in loading and error states
- `customNoDataBanner` — custom empty state

### Rendering custom cells

Use `renderRow` to replace specific cell values with components. Only fields you return are overridden.

```tsx
renderRow={(row) => ({
  status: <PillBadge label={row.status} colorByIndex={row.id as number} />,
  name: (
    <div className="cleen-flex cleen-items-center cleen-gap-2">
      <Avatar name={row.name} size="sm" />
      <span>{row.name}</span>
    </div>
  ),
})}
```

### Context menu (three-dot)

```tsx
threeDotContextMenuOptions={[
  {
    label: 'Edit',
    icon: <IconEditable />,
    onClick: (row) => openEdit(row),
  },
  {
    label: 'Delete',
    icon: <IconTrash />,
    onClick: (row) => confirmDelete(row),
    disabledOptionCallback: (row) => row.role === 'admin',
  },
]}
```

### Pagination with usePaginationState

```tsx
import { usePaginationState } from '@cleen/cleen-components';

const { page, pageSize, setPageSize, handlePageChange } = usePaginationState({
  initialPage: 1,
  initialPageSize: 20,
});

<DataGrid
  withFooter
  activePage={page}
  pageSize={pageSize}
  totalPages={Math.ceil(total / pageSize)}
  setPageSize={setPageSize}
  onPageChange={handlePageChange}
  // ...
/>
```

---

## DataGridWithFilters

Drop-in upgrade over `DataGrid` when you need a filter drawer panel. Pass all standard `DataGrid` props plus `filtersConfig`.

```tsx
import { DataGridWithFilters } from '@cleen/cleen-components';

<DataGridWithFilters
  title="Projects"
  tableHeaders={headers}
  rowData={rows}
  withSearch
  withFooter
  totalPages={totalPages}
  activePage={page}
  onPageChange={setPage}
  filtersConfig={{
    filters: <MyFilterControls />,   // your form controls inside the drawer
    onApply: (savedFilter) => applyFilters(savedFilter),
    onClear: () => clearFilters(),
  }}
/>
```

---

## Card

General-purpose container. Use for stat widgets, info panels, detail sections — anything that needs a bordered box with optional header/footer.

```tsx
import { Card } from '@cleen/cleen-components';

// Stat widget
<Card
  header={{ title: 'Total Revenue', hasDivider: false }}
  p={24}
  hoverable
>
  <div className="cleen-text-3xl cleen-font-bold">$48,200</div>
  <SimpleChart data={revenueTrend} />
</Card>

// Coloured card
<Card color="var(--cleen-primary)" p={20} gap={12}>
  <p>Highlighted content</p>
</Card>

// Card with header + footer
<Card
  header={{ title: 'Shipment #1042', content: <PillBadge label="In Transit" color="blue" />, hasDivider: true }}
  footer={{ content: <Button label="Track" />, hasDivider: true }}
  p={20}
>
  <p>Estimated arrival: March 10</p>
</Card>
```

**Key props:**
- `header.title` — bold title in the card header
- `header.content` — custom content beside/below the title
- `header.hasDivider` — separator line below header
- `footer.content` / `footer.hasDivider` — same for the footer
- `color` — tints background and border from a CSS color
- `isGlass` — frosted glass appearance (great for overlaid panels)
- `hoverable` — shadow + border transition on hover, use on clickable cards

---

## PillBadge

For statuses, tags, roles, categories — anywhere you'd reach for a `<Badge>` component.

```tsx
import { PillBadge } from '@cleen/cleen-components';

// Named color
<PillBadge label="Active" color="green" />
<PillBadge label="Error" color="red" showDot />

// Auto-cycle by index (consistent colors for dynamic data)
<PillBadge label={row.category} colorByIndex={row.id} />

// Removable tag
<PillBadge label="React" removeable onRemoveClicked={() => removeTag('react')} />

// Variants
<PillBadge label="Admin" variant="pill" color="primary" />
<PillBadge label="Draft" variant="square" color="gray" />
```

---

## Progress Components

```tsx
// Horizontal bar
<ProgressBar percentage={72} title="Storage Used" showPercentage renderAnimated />

// Circular KPI
<ProgressCircle percentage={84} size="lg" />

// Multi-bar (budget vs actuals-style)
<AdvancedProgressBar
  minValue={0}
  maxValue={100}
  series={[
    { value: 80, color: 'var(--cleen-primary)', label: 'Budget' },
    { value: 65, color: 'var(--cleen-success)', label: 'Actual' },
  ]}
  renderAnimated
/>
```

---

## Charts

Import from the separate `charts` entry point.

```tsx
import { Chart, SimpleChart } from '@cleen/cleen-components/charts';

// Full interactive chart (line, bar, pie, area, scatter, radar...)
<Chart
  type="bar"
  series={[{ name: 'Revenue', data: [44000, 55000, 48000, 61000, 58000] }]}
  options={{
    xaxis: { categories: ['Jan', 'Feb', 'Mar', 'Apr', 'May'] },
    colors: ['rgba(var(--cleen-primary))'],
  }}
  height={300}
/>

// Pie chart
<Chart
  type="pie"
  series={[44, 33, 23]}
  options={{ labels: ['Direct', 'Organic', 'Referral'] }}
  height={250}
/>

// Sparkline inside a card
<Card header={{ title: 'Weekly Signups' }} p={20}>
  <div className="cleen-text-2xl cleen-font-bold">1,284</div>
  <SimpleChart data={[32, 45, 38, 56, 49, 62]} width={200} height={60} />
</Card>
```

**Chart types:** `'line'` | `'bar'` | `'area'` | `'pie'` | `'donut'` | `'scatter'` | `'radar'` | `'heatmap'` | `'treemap'` | `'rangeBar'`

---

## Loading States

### Loader (spinner)

```tsx
import { Loader } from '@cleen/cleen-components';

// Inline
<Loader size="md" />

// Fullscreen overlay while page loads
<Loader isFullscreen />
```

### Skeletons (content placeholders)

Match the skeleton to the content it's replacing. Never show a generic spinner where shaped skeletons are possible.

```tsx
import {
  SkeletonCard, SkeletonDataGrid, SkeletonChart,
  SkeletonForm, SkeletonList, SkeletonAvatar,
  SkeletonText, SkeletonWidgetCard
} from '@cleen/cleen-components';

// While table loads
{isLoading ? <SkeletonDataGrid /> : <DataGrid ... />}

// While cards load
{isLoading ? (
  <div className="cleen-grid cleen-grid-cols-3 cleen-gap-4">
    <SkeletonCard />
    <SkeletonCard />
    <SkeletonCard />
  </div>
) : <MyCardGrid />}

// While a chart loads
{isLoading ? <SkeletonChart /> : <Chart ... />}
```

**Available skeletons:** `Skeleton`, `SkeletonAvatar`, `SkeletonBadge`, `SkeletonBanner`, `SkeletonButton`, `SkeletonCard`, `SkeletonCard2`, `SkeletonCard3`, `SkeletonCardStack`, `SkeletonChart`, `SkeletonContentCard`, `SkeletonDataGrid`, `SkeletonForm`, `SkeletonImage`, `SkeletonInfoCard`, `SkeletonInput`, `SkeletonList`, `SkeletonParagraph`, `SkeletonText`, `SkeletonVideo`, `SkeletonWidgetCard`

---

## KanbanBoard / KanbanList

```tsx
import { KanbanBoard } from '@cleen/cleen-components';

<KanbanBoard
  columns={[
    {
      id: 'backlog',
      title: 'Backlog',
      cards: [
        { id: 'c1', title: 'Design login page', assignees: [] },
        { id: 'c2', title: 'Setup auth service', assignees: [] },
      ],
    },
    { id: 'in-progress', title: 'In Progress', cards: [] },
    { id: 'done', title: 'Done', cards: [] },
  ]}
  isDraggable
  canAddCards
  onCardSave={(card) => updateCard(card)}
  onCardDragEnd={(cardId, fromCol, toCol) => moveCard(cardId, fromCol, toCol)}
/>
```

Use `KanbanList` for a row-list layout with the same props. Use `KanbanBlocks` sub-components only for fully custom layouts.

---

## Reference Files

- **`references/data-display-patterns.md`** — Full page examples: dashboard with stat cards + charts, user management table, Kanban project board, analytics page.
