---
name: cleen-ui-navigation
description: Build navigation flows using @cleen/cleen-components. Trigger this skill whenever implementing any navigation structure — sidebars, breadcrumbs, tab-based views, multi-step onboarding flows, wizards, step indicators, or paginated lists. This includes vague prompts like "build an app shell", "add a sidebar", "create a multi-step form", "make a settings page with tabs", "show progress through onboarding steps", "add breadcrumbs to the page", "paginate this table", or "build a wizard flow". The primary purpose is to PREVENT rolling custom tab switching logic, hand-built sidebar menus, manual step tracking state, or raw pagination buttons when the library already covers all of these. Trigger on any request involving page structure, section switching, app-level navigation, or multi-step content flows.
---

# Navigation Skill

This skill covers all navigation and flow-control UI using the cleen-components library — from app-level sidebars to breadcrumbs, tab views, guided multi-step wizards, and paginated data. None of these should be built from scratch.

---

## Prime Directive

> **Never hand-roll a sidebar, tab switcher, breadcrumb trail, stepper, or pagination component.**

Common traps to avoid:

- Custom sidebar with manual active-state tracking → use `Sidebar`
- `<nav>` with manually computed breadcrumb paths → use `Breadcrumb`
- Conditional rendering with tab state via `activeTab === 'details'` → use `Tabs`
- Manual `currentStep` + next/back buttons + stepper UI → use `Wizard`
- `Stepper` used alone expecting built-in navigation → use `Wizard` instead (Stepper is display-only)
- Custom pagination buttons with page math → use `Pagination` + `usePaginationState`

---

## Pick the Right Component

| Scenario                                     | Component    |
| -------------------------------------------- | ------------ |
| App-level side navigation panel              | `Sidebar`    |
| Page hierarchy trail (where am I?)           | `Breadcrumb` |
| Switch between independent content sections  | `Tabs`       |
| Guided multi-step flow with Next/Back/Finish | `Wizard`     |
| Visual step progress indicator only (no nav) | `Stepper`    |
| Page controls for a list or table            | `Pagination` |

**Stepper vs Wizard — the most common confusion:**

- `Stepper` is a **display-only** UI indicator. It shows which step is active but has no built-in navigation, no content rendering, and no buttons. Use it when you need to show progress alongside your own custom content and navigation.
- `Wizard` wraps `Stepper` and adds content rendering, Back/Next/Finish buttons, and step management. Use it whenever the user needs to move through ordered steps.

---

## Component Reference

### Sidebar

App-level navigation panel — icon strip on the left with an optional expandable drawer for sub-navigation. The main chrome for any multi-section application.

```tsx
import { Sidebar } from '@cleen/cleen-components';

const navItems = [
  { id: 'dashboard', label: 'Dashboard', iconName: 'HouseLine' },
  {
    id: 'missions',
    label: 'Missions',
    iconName: 'BarChartSquare',
    badgeCount: 3,
  },
  { id: 'agents', label: 'Agents', iconName: 'Users' },
];

const bottomItems = [
  { id: 'settings', label: 'Settings', iconName: 'Settings' },
];

const drawerContent = {
  missions: (
    <DrawerContainer title="Missions">
      <DrawerContentTitle
        title="Active missions"
        onClick={() => navigate('/missions/active')}
      />
      <DrawerContentTitle
        title="Completed"
        onClick={() => navigate('/missions/completed')}
      />
      <DrawerContentTitle
        title="Archived"
        onClick={() => navigate('/missions/archive')}
      />
    </DrawerContainer>
  ),
  agents: (
    <DrawerContainer title="Agents">
      <DrawerContentTitle
        title="All agents"
        onClick={() => navigate('/agents')}
      />
      <DrawerContentTitle
        title="Inactive"
        onClick={() => navigate('/agents/inactive')}
      />
    </DrawerContainer>
  ),
};

<Sidebar
  navigationItems={navItems}
  bottomNavigationItems={bottomItems}
  drawerContent={drawerContent}
  logo={<img src={logo} alt="Logo" />}
  onLogoClick={() => navigate('/')}
  userAvatar={<Avatar src={user.avatar} />}
  userInfo={<span>{user.name}</span>}
  onActiveChange={id => setActiveSection(id)}
/>;
```

**Key props:**

- `navigationItems` — main nav items at the top (required)
- `bottomNavigationItems` — utility items at the bottom (settings, help)
- `drawerContent` — `Record<itemId, ReactNode>` — what to render in the drawer when each item is active
- `logo` / `onLogoClick` — logo rendered above nav items
- `userAvatar` / `userInfo` — user section above bottom items
- `drawerFooter` — footer content or factory `(activeMenuId) => ReactNode`
- `drawerWidth` — drawer panel width in px (default 330)
- `activeId` / `onActiveChange` — controlled mode
- `defaultActiveId` — open a specific drawer on first render
- `closeOnOutsideClick` / `closeOnBlur` — auto-close the drawer (both default `true`)
- `renderSidebarItem` — fully custom render function for each sidebar icon item

**Item config shape:**

```ts
{ id: string; label: string; iconName?: string; iconSrc?: string; badgeCount?: number | string; withBottomDivider?: boolean }
```

**DO:**

- Items without a matching `drawerContent` key behave as plain navigation links — wire `onActiveChange` to router navigation.
- Use `badgeCount` on items that have pending counts (unread messages, action items).
- Use `withBottomDivider: true` to visually separate groups of nav items.

**DON'T:**

- Build custom sidebar logic — the whole open/close/active-tracking system is handled internally.
- Use `iconName` values outside what the icon library supports; check with `getIconByName` utility.

---

### Breadcrumb

Page hierarchy trail. Use on any page that lives more than one level deep.

```tsx
import { Breadcrumb } from '@cleen/cleen-components';

<Breadcrumb
  segments={[
    { path: '/agents', label: 'Agents' },
    { path: '/agents/snake-eater', label: 'Snake Eater' },
    { path: '/agents/snake-eater/missions', label: 'Missions' },
  ]}
/>;
```

**Key props:**

- `segments` — array of `{ path, label, icon?, id? }` — renders as router `<Link>` elements
- `root` — the home segment (defaults to a home icon at `/`). Pass `root={null}` to hide it
- Last segment is automatically styled as the current page (no hover, rounded bg, `aria-current="page"`)
- Chevron separators are inserted automatically

**DO:**

- Derive `segments` from your route params — don't hardcode paths.
- Keep labels short — this is a nav trail, not a page title.

**DON'T:**

- Leave out `segments` — the component renders nothing if it's empty.
- Use the raw string `path` for the last segment expecting it to be unlinked — the last item is still a link but styled differently; React Router handles it.

---

### Tabs

Switch between independent content sections. Use for settings pages, detail views with multiple facets, or any non-sequential multi-section UI.

```tsx
import { Tabs } from '@cleen/cleen-components';

// Uncontrolled (internal state)
<Tabs
  tabs={[
    { id: 'general', label: 'General', content: <GeneralSettings /> },
    { id: 'security', label: 'Security', content: <SecuritySettings /> },
    {
      id: 'notifications',
      label: 'Notifications',
      badge: { label: 5 },
      content: <NotificationSettings />,
    },
    { id: 'billing', label: 'Billing', isDisabled: true, content: null },
  ]}
  variant="padded"
/>

// Controlled (synced to router)
<Tabs
  tabs={tabs}
  currentTabIndex={activeTabIndex}
  onTabChange={(index, tab) => navigate(`?tab=${tab?.id}`)}
  variant="underlined"
  direction="vertical"
/>
```

**Key props:**

- `tabs` — array of `{ id?, label, content?, badge?, isDisabled? }`
- `currentTabIndex` / `onTabChange` — controlled mode (omit both for uncontrolled)
- `variant` — `'underlined'` (default) | `'padded'`
- `direction` — `'horizontal'` (default) | `'vertical'`
- `badge` — passes a `PillBadgeProps` object to show a count bubble on the tab

**DO:**

- Use `badge` to show unread counts, pending items, or notification numbers on tab labels.
- Prefer uncontrolled mode unless you need to sync the active tab with the URL.
- Use `variant="padded"` for settings-style tabs, `variant="underlined"` for document-style tabs.

**DON'T:**

- Use Tabs for ordered steps where the user must complete each section in sequence — use `Wizard`.
- Leave `content` as `undefined` without disabling the tab — an empty tab is confusing.

---

### Wizard

Guided multi-step flow. Handles step rendering, navigation buttons (Back / Next / Finish), and step-progress indicator. The go-to for onboarding flows, multi-step forms, and setup flows.

```tsx
import { Wizard } from '@cleen/cleen-components';

// Uncontrolled (simplest — Wizard manages step state internally)
<Wizard
  steps={[
    {
      id: 'account',
      title: 'Account',
      subtitle: 'Set up your credentials',
      content: <AccountStep />,
    },
    {
      id: 'profile',
      title: 'Profile',
      subtitle: 'Tell us about yourself',
      content: <ProfileStep />,
    },
    {
      id: 'review',
      title: 'Review',
      subtitle: 'Confirm your details',
      content: <ReviewStep />,
    },
  ]}
  onFinish={handleFinish}
/>

// Controlled (manage step externally — needed for step validation before advancing)
<Wizard
  steps={steps}
  currentStepIndex={currentStep}
  onStepChange={(index) => {
    if (!validateCurrentStep()) return; // abort advance if validation fails
    setCurrentStep(index);
  }}
  onFinish={handleSubmit}
  labels={{ nextBtn: 'Continue', previousBtn: 'Back' }}
/>
```

**Key props:**

- `steps` — array of `{ id?, title, subtitle?, content }` — `content` is the JSX for each step
- `currentStepIndex` / `onStepChange` — controlled mode (omit for uncontrolled)
- `onFinish` — called when Next is clicked on the last step (wire your submit logic here)
- `showButtons` — hide the built-in Back/Next buttons when `false` (manage navigation yourself)
- `labels.nextBtn` / `labels.previousBtn` — custom button labels (last step's Next still calls `onFinish`)

**DO:**

- Use controlled mode whenever steps require validation before advancing — check validity in `onStepChange` and return early to stay on the current step.
- Keep each step's `content` as a standalone component — don't inline complex JSX in the `steps` array.
- Wire `onFinish` to your API submit call.

**DON'T:**

- Use `Wizard` for sections the user can freely jump between — use `Tabs` instead.
- Reach for `Stepper` when you actually need step navigation — `Wizard` includes the stepper display plus all the navigation machinery.

---

### Stepper

Visual step-progress indicator. Use this alone only when you need to show step progress alongside content and navigation that you control yourself (e.g., a multi-page form embedded in a drawer).

```tsx
import { Stepper } from '@cleen/cleen-components';

const steps = [
  { title: 'Briefing', subtitle: 'Review mission' },
  { title: 'Equipment', subtitle: 'Choose loadout' },
  { title: 'Deployment', subtitle: 'Confirm and launch' },
];

// activeStep is 0-based. 0 = first step active, 1 = second active, etc.
<Stepper steps={steps} activeStep={currentStep} orientation="horizontal" />;
```

**Key props:**

- `steps` — array of `{ title?, subtitle? }`
- `activeStep` — 0-based index of the current step; steps before it show a ✓
- `orientation` — `'horizontal'` (default) | `'vertical'`
- `color` — CSS color string for the active/completed accent color

**Stepper is display-only** — it emits no events and has no click handlers. You drive it from your own state.

---

### Pagination

Page controls for paginated data lists. Always fully controlled — you own the page state.

```tsx
import { Pagination, usePaginationState } from '@cleen/cleen-components';

// Simplest: use the built-in hook
function AgentList() {
  const { page, pageSize, setPageSize, handlePageChange } = usePaginationState({
    initialPage: 1,
  });
  const { data: agents, totalCount } = useFetchAgents({ page, pageSize });
  const totalPages = Math.ceil((totalCount ?? 0) / pageSize);

  return (
    <>
      <AgentTable agents={agents} />
      <Pagination
        currentPage={page}
        totalPages={totalPages}
        pageSize={pageSize}
        pageSizes={[10, 25, 50]}
        onPageChange={handlePageChange}
        onPageSizeChange={setPageSize}
      />
    </>
  );
}
```

**Key props:**

- `currentPage` / `totalPages` — required; fully controlled
- `pageSize` / `pageSizes` — current size and the options array (default options: `[10, 20, 50]`)
- `onPageChange(page)` / `onPageSizeChange(size)` — update callbacks
- `hasPageSizeSelector` — show the page-size dropdown (default `true`)
- `hasGotoPage` — show the "go to page" input (default `true`)

**`usePaginationState` hook:**

```ts
const { page, pageSize, setPageSize, handlePageChange } = usePaginationState({
  initialPage: 1,
  initialPageSize: 10,
});
```

Returns `{ page, setPage, pageSize, setPageSize, handleNextPage, handlePreviousPage, handlePageChange }`.

**DO:**

- Always reset `page` back to `1` when filters or search terms change — stale page + new filter = wrong data.
- Use `usePaginationState` from the library; don't reach for `useState` separately for page and pageSize.
- Keep `DataGrid` / `DataGridWithFilters` and `Pagination` as siblings, not nested.

**DON'T:**

- Hardcode `totalPages` — derive it from `Math.ceil(totalCount / pageSize)`.
- Show `Pagination` when `totalPages <= 1` — it renders but looks awkward; conditionally hide it.

---

## Recipes

For full working patterns — app shell with sidebar + router, tabbed settings page, multi-step onboarding wizard with validation, paginated table, and breadcrumb wired to a router — see `references/navigation-patterns.md`.
