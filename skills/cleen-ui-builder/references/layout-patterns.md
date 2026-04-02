# Layout Patterns

Reusable layout structures for consumer projects. Choose a base layout pattern (header nav, sidebar, or icon sidebar) and combine with content areas and card grids.

**For SaaS applications, refer to [`layout-default-patterns.md`](layout-default-patterns.md) first.**

## Table of Contents

1. [Header Navigation Layout](#header-navigation-layout)
2. [Header Navigation with Two-Row Layout](#header-navigation-with-two-row-layout)
3. [Left Sidebar Navigation Layout](#left-sidebar-navigation-layout)
4. [Compact Icon Sidebar Layout](#compact-icon-sidebar-layout)
5. [Multi-Column Card Grid](#multi-column-card-grid)
6. [Asymmetric Column Layout](#asymmetric-column-layout)

---

## Header Navigation Layout

Horizontal navigation bar at the top with main content below. Best for apps with 4-6 top-level sections.

```tsx
import { Avatar, Button, Card, Tabs } from '@cleen/ui';
import { useState } from 'react';

function AppWithHeaderNav() {
  const [activeTab, setActiveTab] = useState(0);

  const tabs = [
    { label: 'Dashboard' },
    { label: 'Team' },
    { label: 'Projects' },
    { label: 'Calendar' },
    { label: 'Reports' },
  ];

  return (
    <div className="min-h-screen bg-gray-50">
      {/* Header Navigation */}
      <header className="bg-white border-b border-gray-200">
        <div className="max-w-7xl mx-auto px-6 py-4 flex items-center justify-between">
          <div className="flex items-center gap-8">
            <span className="text-xl font-semibold text-gray-900">Logo</span>
            <Tabs
              tabs={tabs}
              currentTabIndex={activeTab}
              onTabChange={setActiveTab}
              variant="underlined"
            />
          </div>
          <div className="flex items-center gap-3">
            <Button variant="secondary" label="Notifications" />
            <Avatar name="Jane Doe" size="md" />
          </div>
        </div>
      </header>

      {/* Main Content */}
      <main className="max-w-7xl mx-auto px-6 py-8">
        <Card header={{ title: 'Overview', hasDivider: true }}>
          <p className="text-sm text-gray-600">Page content goes here.</p>
        </Card>
      </main>
    </div>
  );
}
```

**Notes:**

- Navigation items use underline indicator for active state — clear and minimal.
- Header stays at fixed 64px height; adjust `py-4` for different heights.
- Logo and nav are grouped on left; utilities (search, notifications, profile) on right.

---

## Header Navigation with Two-Row Layout

Horizontal top nav in first row, search + utilities in second row. Good for search-heavy apps.

```tsx
import { Avatar, Button, Card, Input, Tabs } from '@cleen/ui';
import { useState } from 'react';

function AppWithSearchHeader() {
  const [activeTab, setActiveTab] = useState(0);
  const [search, setSearch] = useState('');

  const tabs = [
    { label: 'Dashboard' },
    { label: 'Team' },
    { label: 'Projects' },
    { label: 'Calendar' },
    { label: 'Reports' },
  ];

  return (
    <div className="min-h-screen bg-gray-50">
      {/* Row 1: Logo + Navigation */}
      <header className="bg-white border-b border-gray-200">
        <div className="max-w-7xl mx-auto px-6 py-3 flex items-center justify-between">
          <div className="flex items-center gap-8">
            <span className="text-xl font-semibold text-gray-900">Logo</span>
            <Tabs
              tabs={tabs}
              currentTabIndex={activeTab}
              onTabChange={setActiveTab}
              variant="underlined"
            />
          </div>
        </div>
      </header>

      {/* Row 2: Search + Utilities */}
      <nav className="bg-white border-b border-gray-200">
        <div className="max-w-7xl mx-auto px-6 py-3 flex items-center justify-between">
          <div className="flex-1 max-w-sm">
            <Input
              placeholder="Search..."
              value={search}
              onChange={(event) => setSearch(event.target.value)}
            />
          </div>
          <div className="flex items-center gap-3">
            <Button variant="secondary" label="Notifications" />
            <Avatar name="Jane Doe" size="md" />
          </div>
        </div>
      </nav>

      {/* Main Content */}
      <main className="max-w-7xl mx-auto px-6 py-8">
        <Card header={{ title: 'Search Results', hasDivider: true }}>
          <p className="text-sm text-gray-600">Page content goes here.</p>
        </Card>
      </main>
    </div>
  );
}
```

**Notes:**

- Two header rows keep navigation and search visually separated but logically grouped.
- Search input uses `flex-1 max-w-sm` to constrain width while filling available space.
- Both rows use same border-bottom style for continuity.

---

## Left Sidebar Navigation Layout

Vertical sidebar on left with main content on right. Good for dashboards with many nav items.

```tsx
import { Avatar, Button, Card, Sidebar } from '@cleen/ui';

function AppWithSidebar() {
  const activePage = 'dashboard';

  const navItems = [
    { id: 'dashboard', label: 'Dashboard', iconName: 'HouseLine' },
    { id: 'team', label: 'Team', iconName: 'Users' },
    { id: 'projects', label: 'Projects', iconName: 'FolderOpen' },
    { id: 'calendar', label: 'Calendar', iconName: 'Calendar' },
    { id: 'documents', label: 'Documents', iconName: 'FileText' },
    { id: 'reports', label: 'Reports', iconName: 'ChartBar' },
  ];

  const bottomNavItems = [
    { id: 'settings', label: 'Settings', iconName: 'Gear' },
    { id: 'help', label: 'Help', iconName: 'Question' },
  ];

  return (
    <div className="flex min-h-screen bg-gray-50">
      {/* Left Sidebar via CleenUI */}
      <Sidebar
        navItems={navItems}
        bottomNavItems={bottomNavItems}
        activeItem={activePage}
        appLogo={<span className="text-lg font-semibold">Logo</span>}
      />

      {/* Main Content */}
      <main className="flex-1">
        <header className="bg-white border-b border-gray-200 px-8 py-6">
          <div className="flex items-center justify-between">
            <h1 className="text-3xl font-bold text-gray-900">Dashboard</h1>
            <div className="flex items-center gap-3">
              <Button variant="secondary" label="Notifications" />
              <Avatar name="Jane Doe" size="md" />
            </div>
          </div>
        </header>

        <section className="px-8 py-8">
          <div className="grid grid-cols-1 xl:grid-cols-2 gap-6">
            <Card header={{ title: 'Team Activity', hasDivider: true }}>
              <p className="text-sm text-gray-600">Page content goes here.</p>
            </Card>
            <Card header={{ title: 'Upcoming Work', hasDivider: true }}>
              <p className="text-sm text-gray-600">Page content goes here.</p>
            </Card>
          </div>
        </section>
      </main>
    </div>
  );
}
```

**Notes:**

- `Sidebar` centralizes icon + label nav structure and active item rendering.
- Pass `activeItem` from route/page state to keep selection synchronized.
- Keep secondary actions (Settings, Help) in `bottomNavItems` for consistent placement.
- `appLogo` renders the brand mark in the sidebar header.

---

## Compact Icon Sidebar Layout

Minimal left sidebar showing only icons; labels appear on hover. Maximizes content area.

```tsx
import { Avatar, Button, Card, Input, PillBadge, Sidebar } from '@cleen/ui';
import { useState } from 'react';

function AppWithIconSidebar() {
  const activePage = 'dashboard';
  const [search, setSearch] = useState('');

  const navItems = [
    { id: 'dashboard', label: 'Dashboard', iconName: 'HouseLine' },
    { id: 'team', label: 'Team', iconName: 'Users' },
    { id: 'projects', label: 'Projects', iconName: 'FolderOpen' },
    { id: 'calendar', label: 'Calendar', iconName: 'Calendar' },
    { id: 'documents', label: 'Documents', iconName: 'FileText' },
    { id: 'reports', label: 'Reports', iconName: 'ChartBar' },
  ];

  const bottomNavItems = [
    { id: 'settings', label: 'Settings', iconName: 'Gear' },
    { id: 'help', label: 'Help', iconName: 'Question' },
  ];

  return (
    <div className="flex min-h-screen bg-gray-50">
      {/* Same Sidebar component as AppWithSidebar for identical behavior */}
      <Sidebar
        navItems={navItems}
        bottomNavItems={bottomNavItems}
        activeItem={activePage}
        appLogo={<span className="text-lg font-semibold">Logo</span>}
      />

      {/* Main Content */}
      <main className="flex-1">
        <header className="bg-white border-b border-gray-200 px-8 py-6">
          <div className="flex items-center justify-between">
            <h1 className="text-3xl font-bold text-gray-900">Dashboard</h1>
            <div className="flex items-center gap-4">
              <Input
                placeholder="Search..."
                value={search}
                onChange={(event) => setSearch(event.target.value)}
              />
              <Button variant="secondary" label="Notifications" />
              <Avatar name="Jane Doe" size="md" />
            </div>
          </div>
        </header>

        <section className="px-8 py-8">
          <div className="grid grid-cols-1 md:grid-cols-3 gap-6">
            <Card className="md:col-span-2" header={{ title: 'Overview', hasDivider: true }}>
              <p className="text-sm text-gray-600">Page content goes here.</p>
            </Card>
            <Card header={{ title: 'Status', hasDivider: true }}>
              <div className="flex flex-wrap gap-2">
                <PillBadge label="Healthy" color="green" showDot />
                <PillBadge label="2 Alerts" color="orange" />
              </div>
            </Card>
          </div>
        </section>
      </main>
    </div>
  );
}
```

**Notes:**

- Uses the same `Sidebar` component as the standard sidebar example for API parity.
- Keep the same `navItems` and `bottomNavItems` shape (`id`, `label`, `iconName`) across both layouts.
- Pass `activeItem` from page state/route to keep selection behavior identical.
- Keep visual differences in content cards, not in sidebar implementation.

---

## Multi-Column Card Grid

Responsive grid layout for displaying cards in 2, 3, or 4 columns depending on screen size.

```tsx
import { Card, PillBadge } from '@cleen/ui';

function MultiColumnCardLayout() {
  const cards = [
    { id: 1, title: 'Card 1', description: 'First item' },
    { id: 2, title: 'Card 2', description: 'Second item' },
    { id: 3, title: 'Card 3', description: 'Third item' },
    { id: 4, title: 'Card 4', description: 'Fourth item' },
    { id: 5, title: 'Card 5', description: 'Fifth item' },
    { id: 6, title: 'Card 6', description: 'Sixth item' },
  ];

  return (
    <main className="max-w-7xl mx-auto px-6 py-8">
      <h1 className="text-3xl font-bold text-gray-900 mb-8">Items</h1>

      {/* 2-column md, 3-column lg, 4-column xl */}
      <div className="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 xl:grid-cols-4 gap-6">
        {cards.map((card) => (
          <Card
            key={card.id}
            hoverable
            header={{
              content: (
                <div className="flex items-center justify-between">
                  <h3 className="text-lg font-semibold text-gray-900">{card.title}</h3>
                  <PillBadge label="New" color="blue" />
                </div>
              ),
            }}
          >
            <div className="w-full h-32 rounded-lg bg-gray-200 mb-4" />
            <p className="text-sm text-gray-600">{card.description}</p>
          </Card>
        ))}
      </div>
    </main>
  );
}
```

**Notes:**

- Responsive grid: 1 col mobile → 2 cols tablet → 3 cols desktop → 4 cols large screens.
- Cards use `Card` with `hoverable` for interactivity.
- Adjust gap-size: `gap-4` for tighter, `gap-8` for spacious layouts.
- Use `xl:grid-cols-3` if 4 columns feels too cramped.

---

## Asymmetric Column Layout

One small column + one large column. Useful for sidebar + main content.

```tsx
import { Card, Input, PillBadge, Tabs } from '@cleen/ui';
import { useState } from 'react';

function AsymmetricLayout() {
  const [activeTab, setActiveTab] = useState(0);
  const [keyword, setKeyword] = useState('');

  return (
    <main className="max-w-7xl mx-auto px-6 py-8">
      <div className="grid grid-cols-1 lg:grid-cols-3 gap-6">
        {/* Small left column (1/3 width) */}
        <aside className="lg:col-span-1">
          <Card className="sticky top-6" header={{ title: 'Filters', hasDivider: true }}>
            <div className="space-y-4">
              <Input
                placeholder="Search by keyword..."
                value={keyword}
                onChange={(event) => setKeyword(event.target.value)}
              />
              <Tabs
                tabs={[{ label: 'All' }, { label: 'Open' }, { label: 'Closed' }]}
                currentTabIndex={activeTab}
                onTabChange={setActiveTab}
                variant="underlined"
              />
              <div className="flex flex-wrap gap-2">
                <PillBadge label="Priority" color="orange" />
                <PillBadge label="Assigned" color="blue" />
                <PillBadge label="SLA" color="purple" />
              </div>
            </div>
          </Card>
        </aside>

        {/* Large right column (2/3 width) */}
        <section className="lg:col-span-2">
          <div className="space-y-6">
            {/* Card 1 */}
            <Card header={{ title: 'Main Content', hasDivider: true }}>
              <h2 className="text-2xl font-bold text-gray-900 mb-4">
                Main Content
              </h2>
              <p className="text-gray-600 mb-6">
                This is the primary content area. The sidebar on the left stays sticky as you scroll.
              </p>
              <div className="h-64 rounded-lg bg-gray-100" />
            </Card>

            {/* Card 2 */}
            <Card header={{ title: 'Secondary Content', hasDivider: true }}>
              <h3 className="text-lg font-semibold text-gray-900 mb-4">
                Secondary Content
              </h3>
              <p className="text-gray-600">
                Add more cards or sections as needed.
              </p>
            </Card>
          </div>
        </section>
      </div>
    </main>
  );
}
```

**Notes:**

- Left sidebar uses `lg:col-span-1`, right main uses `lg:col-span-2` — creates 1:2 split.
- `sticky top-6` on sidebar keeps filters visible while scrolling.
- On mobile, sidebar appears above main content (both full width).
- Grid uses `gap-6` for consistent spacing between columns.
