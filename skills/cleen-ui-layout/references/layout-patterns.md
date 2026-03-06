# Layout Patterns

Reusable, copy-paste-ready layout structures for common page types. All use `Card`, `Divider`, `PillBadge`, `Avatar`, and Tailwind grid/flex via the `cleen-` prefix.

---

## Table of Contents

1. [Page Shell (header + content area)](#page-shell-header--content-area)
2. [KPI / Stats Row](#kpi--stats-row)
3. [Dashboard Page](#dashboard-page)
4. [Settings Page (tabbed)](#settings-page-tabbed)
5. [Detail Page (main + sidebar)](#detail-page-main--sidebar)
6. [List Page (table + empty state)](#list-page-table--empty-state)
7. [Card Grid (flexible column count)](#card-grid-flexible-column-count)
8. [Card with Inline Row List](#card-with-inline-row-list)
9. [Danger Zone Card](#danger-zone-card)
10. [Mixed-width Grid (asymmetric columns)](#mixed-width-grid-asymmetric-columns)

---

## Page Shell (header + content area)

The standard outer shell for any page — title, subtitle, action buttons, then content below.

```tsx
function MissionsPage() {
  return (
    <div className="cleen cleen-p-6 cleen-bg-gray-50 cleen-min-h-screen">
      {/* Page header */}
      <div className="cleen-flex cleen-justify-between cleen-items-start cleen-mb-6">
        <div>
          <h1 className="cleen-text-3xl cleen-font-bold cleen-text-gray">
            Missions
          </h1>
          <p className="cleen-text-gray/70 cleen-mt-1">
            Active and completed field operations
          </p>
        </div>
        <div className="cleen-flex cleen-gap-2">
          <Button
            variant="secondary"
            label="Export"
            leftIcon={<MdDownload />}
          />
          <Button
            variant="primary"
            label="New mission"
            leftIcon={<MdAdd />}
            onClick={openCreate}
          />
        </div>
      </div>

      {/* Page content */}
      <div className="cleen-flex cleen-flex-col cleen-gap-6">
        {/* ... sections go here */}
      </div>
    </div>
  );
}
```

**Notes:**

- Always `cleen` + `cleen-p-6` + `cleen-min-h-screen` on the page root.
- `cleen-bg-gray-50` gives a subtle off-white page background that makes Cards pop.
- Use `cleen-flex cleen-flex-col cleen-gap-6` as the content area — consistent vertical rhythm throughout the page.

---

## KPI / Stats Row

Row of metric cards — the most common dashboard element.

```tsx
interface Metric {
  label: string;
  value: string;
  change: number; // positive = up, negative = down
  icon: ReactNode;
  color: string;
}

const metrics: Metric[] = [
  {
    label: 'Active missions',
    value: '24',
    change: +12,
    icon: <MdFlashOn />,
    color: 'primary',
  },
  {
    label: 'Agents deployed',
    value: '8',
    change: -2,
    icon: <MdPerson />,
    color: 'blue',
  },
  {
    label: 'Success rate',
    value: '91%',
    change: +4.5,
    icon: <MdCheckCircle />,
    color: 'green',
  },
  {
    label: 'Avg. mission time',
    value: '18d',
    change: -8,
    icon: <MdTimer />,
    color: 'orange',
  },
];

<div className="cleen-grid cleen-grid-cols-1 md:cleen-grid-cols-2 lg:cleen-grid-cols-4 cleen-gap-4 cleen-mb-6">
  {metrics.map((metric, index) => (
    <Card key={index}>
      <div className="cleen-flex cleen-justify-between cleen-items-start cleen-mb-4">
        <div
          className={`cleen-p-3 cleen-rounded-lg cleen-bg-${metric.color}/10 cleen-text-${metric.color}`}
        >
          {metric.icon}
        </div>
        <PillBadge
          label={`${metric.change > 0 ? '+' : ''}${metric.change}%`}
          color={metric.change > 0 ? 'green' : 'red'}
        />
      </div>
      <p className="cleen-text-sm cleen-font-medium cleen-text-gray/70 cleen-mb-1">
        {metric.label}
      </p>
      <p className="cleen-text-2xl cleen-font-bold cleen-text-gray">
        {metric.value}
      </p>
    </Card>
  ))}
</div>;
```

**Notes:**

- 1 col mobile → 2 cols tablet → 4 cols desktop. Adjust `lg:cleen-grid-cols-*` for 2 or 3 metric cards.
- `cleen-bg-${metric.color}/10` + `cleen-text-${metric.color}` for icon box coloring — uses the color palette CSS variables.
- `PillBadge` with `color="green"` / `color="red"` for trend indicator, no dot needed here.

---

## Dashboard Page

Full dashboard with KPI row, charts grid, and a mixed-width bottom section.

```tsx
function Dashboard() {
  const [activeTab, setActiveTab] = useState(0);

  return (
    <div className="cleen cleen-p-6 cleen-bg-gray-50 cleen-min-h-screen">
      {/* Header */}
      <div className="cleen-mb-6">
        <div className="cleen-flex cleen-justify-between cleen-items-start cleen-mb-4">
          <div>
            <h1 className="cleen-text-3xl cleen-font-bold cleen-text-gray">
              Command Dashboard
            </h1>
            <p className="cleen-text-gray/70 cleen-mt-1">
              FOX-HOUND operational overview
            </p>
          </div>
          <Button
            variant="secondary"
            label="Export report"
            leftIcon={<MdDownload />}
          />
        </div>
        <Tabs
          tabs={[
            { label: 'Overview' },
            { label: 'Operations' },
            { label: 'Personnel' },
          ]}
          currentTabIndex={activeTab}
          onTabChange={setActiveTab}
          variant="underlined"
        />
      </div>

      {/* KPI row */}
      <div className="cleen-grid cleen-grid-cols-1 md:cleen-grid-cols-2 lg:cleen-grid-cols-4 cleen-gap-4 cleen-mb-6">
        {metrics.map(/* ... see KPI pattern above ... */)}
      </div>

      {/* Charts: 2-column grid */}
      <div className="cleen-grid cleen-grid-cols-1 lg:cleen-grid-cols-2 cleen-gap-6 cleen-mb-6">
        <Card header={{ title: 'Mission success rate', hasDivider: true }}>
          <Chart
            type="line"
            series={successSeries}
            options={lineOpts}
            height={280}
          />
        </Card>
        <Card header={{ title: 'Deployments by region', hasDivider: true }}>
          <Chart
            type="bar"
            series={regionalSeries}
            options={barOpts}
            height={280}
          />
        </Card>
      </div>

      {/* Bottom: asymmetric 3-column grid */}
      <div className="cleen-grid cleen-grid-cols-1 lg:cleen-grid-cols-3 cleen-gap-6">
        <Card header={{ title: 'Mission types', hasDivider: true }}>
          <Chart
            type="pie"
            series={typeSeries}
            options={pieOpts}
            height={280}
          />
        </Card>
        <Card
          className="lg:cleen-col-span-2"
          header={{ title: 'Top operatives', hasDivider: true }}
        >
          <DataGrid tableHeaders={operativeHeaders} rowData={topOperatives} />
        </Card>
      </div>
    </div>
  );
}
```

**Notes:**

- Section rhythm: `cleen-mb-6` between rows keeps vertical spacing consistent.
- Charts should always live inside a `Card` with a `header.title` — a floating chart with no context is confusing.
- `lg:cleen-col-span-2` on the last card in a 3-column grid creates the "side panel + main" asymmetric layout.

---

## Settings Page (tabbed)

Classic settings layout: tabs on the left or top, content cards stacked vertically per section.

```tsx
function SettingsPage() {
  const [activeTab, setActiveTab] = useState('profile');

  const tabs = [
    { id: 'profile', label: 'Profile' },
    { id: 'security', label: 'Security' },
    { id: 'notifications', label: 'Notifications' },
    { id: 'preferences', label: 'Preferences' },
  ];

  return (
    <div className="cleen cleen-p-6 cleen-min-h-screen">
      <div className="cleen-flex cleen-justify-between cleen-items-center cleen-mb-6">
        <h1 className="cleen-text-2xl cleen-font-bold cleen-text-gray">
          Settings
        </h1>
        {hasUnsavedChanges && (
          <div className="cleen-flex cleen-gap-2">
            <Button variant="secondary" label="Discard" onClick={reset} />
            <Button
              variant="primary"
              label="Save changes"
              isLoading={isSaving}
              onClick={save}
            />
          </div>
        )}
      </div>

      <Tabs
        tabs={tabs.map(t => ({ id: t.id, label: t.label }))}
        currentTabIndex={tabs.findIndex(t => t.id === activeTab)}
        onTabChange={(_, tab) => setActiveTab(tab?.id ?? 'profile')}
        variant="underlined"
      />

      <div className="cleen-mt-6 cleen-flex cleen-flex-col cleen-gap-6">
        {activeTab === 'profile' && (
          <>
            {/* Profile info card */}
            <Card header={{ title: 'Personal information', hasDivider: true }}>
              <div className="cleen-grid cleen-grid-cols-1 md:cleen-grid-cols-2 cleen-gap-4">
                <FormGroup title="First name" required>
                  <Input
                    value={profile.firstName}
                    onChange={e => update('firstName', e.target.value)}
                  />
                </FormGroup>
                <FormGroup title="Last name" required>
                  <Input
                    value={profile.lastName}
                    onChange={e => update('lastName', e.target.value)}
                  />
                </FormGroup>
                <FormGroup title="Bio" className="md:cleen-col-span-2">
                  <TextArea
                    value={profile.bio}
                    onChange={e => update('bio', e.target.value)}
                  />
                </FormGroup>
              </div>
            </Card>

            {/* Danger zone at the bottom */}
            <Card color="var(--cleen-error)">
              <h3 className="cleen-text-base cleen-font-semibold cleen-text-error cleen-mb-1">
                Danger zone
              </h3>
              <p className="cleen-text-sm cleen-text-gray/70 cleen-mb-4">
                Permanently delete your account and all data.
              </p>
              <Button
                variant="danger"
                label="Delete account"
                onClick={openDeleteDialog}
              />
            </Card>
          </>
        )}

        {activeTab === 'notifications' && (
          <Card
            header={{ title: 'Notification preferences', hasDivider: true }}
          >
            {notificationRows.map((row, i) => (
              <div key={row.id}>
                <div className="cleen-flex cleen-justify-between cleen-items-center cleen-py-3">
                  <div>
                    <p className="cleen-text-sm cleen-font-medium cleen-text-gray">
                      {row.label}
                    </p>
                    <p className="cleen-text-xs cleen-text-gray/60">
                      {row.description}
                    </p>
                  </div>
                  <Switch
                    checked={row.enabled}
                    onChange={e => toggleNotification(row.id, e.target.checked)}
                  />
                </div>
                {i < notificationRows.length - 1 && <Divider />}
              </div>
            ))}
          </Card>
        )}
      </div>
    </div>
  );
}
```

**Notes:**

- Put a `<Divider />` between rows inside a card instead of `border-b` on each row — it's cleaner and uses the library.
- The danger-zone card uses `color="var(--cleen-error)"` to apply a subtle red tint — no custom border needed.
- `md:cleen-col-span-2` on wide fields (bio, address) inside a 2-column form grid spans them full width on tablet+.

---

## Detail Page (main + sidebar)

Two-column layout: wide main content area + narrower detail sidebar. Common for record detail pages.

```tsx
function AgentDetailPage({ agent }: { agent: Agent }) {
  return (
    <div className="cleen cleen-p-6 cleen-min-h-screen">
      {/* Breadcrumb */}
      <Breadcrumb
        segments={[
          { path: '/agents', label: 'Agents' },
          { path: `/agents/${agent.id}`, label: agent.codename },
        ]}
      />

      {/* Page title row */}
      <div className="cleen-flex cleen-justify-between cleen-items-center cleen-mt-4 cleen-mb-6">
        <div className="cleen-flex cleen-items-center cleen-gap-3">
          <Avatar name={agent.codename} src={agent.avatarUrl} size="xl" />
          <div>
            <h1 className="cleen-text-2xl cleen-font-bold cleen-text-gray">
              {agent.codename}
            </h1>
            <div className="cleen-flex cleen-items-center cleen-gap-2 cleen-mt-1">
              <PillBadge
                label={agent.status}
                color={agent.status === 'Active' ? 'green' : 'gray'}
                showDot
              />
              <PillBadge label={agent.clearance} color="blue" />
            </div>
          </div>
        </div>
        <div className="cleen-flex cleen-gap-2">
          <Button variant="secondary" label="Edit" onClick={openEdit} />
          <Button
            variant="danger"
            label="Deactivate"
            onClick={openDeactivate}
          />
        </div>
      </div>

      {/* Main 2-column grid */}
      <div className="cleen-grid cleen-grid-cols-1 lg:cleen-grid-cols-3 cleen-gap-6">
        {/* Wide main column */}
        <div className="lg:cleen-col-span-2 cleen-flex cleen-flex-col cleen-gap-6">
          <Card header={{ title: 'Mission history', hasDivider: true }}>
            <DataGrid tableHeaders={missionHeaders} rowData={agent.missions} />
          </Card>
          <Card header={{ title: 'Performance metrics', hasDivider: true }}>
            <Chart
              type="bar"
              series={performanceSeries}
              options={opts}
              height={240}
            />
          </Card>
        </div>

        {/* Narrow sidebar column */}
        <div className="cleen-flex cleen-flex-col cleen-gap-6">
          <Card header={{ title: 'Details', hasDivider: true }}>
            <div className="cleen-flex cleen-flex-col cleen-gap-3 cleen-text-sm">
              <div className="cleen-flex cleen-justify-between">
                <span className="cleen-text-gray/60">Unit</span>
                <span className="cleen-font-medium">{agent.unit}</span>
              </div>
              <Divider />
              <div className="cleen-flex cleen-justify-between">
                <span className="cleen-text-gray/60">Rank</span>
                <span className="cleen-font-medium">{agent.rank}</span>
              </div>
              <Divider />
              <div className="cleen-flex cleen-justify-between">
                <span className="cleen-text-gray/60">Active since</span>
                <span className="cleen-font-medium">{agent.activeSince}</span>
              </div>
            </div>
          </Card>

          <Card header={{ title: 'Team', hasDivider: true }}>
            <AvatarRow avatars={agent.teamMembers} maxVisible={4} size="sm" />
          </Card>
        </div>
      </div>
    </div>
  );
}
```

**Notes:**

- `lg:cleen-col-span-2` on the main column, sidebar takes the remaining col — standard 2:1 split.
- Key-value rows inside sidebar cards: `cleen-flex cleen-justify-between` + `Divider` between items is cleaner than a table.
- Avatar + status badges in the title row establish identity at a glance before any content loads.

---

## List Page (table + empty state)

Page for browsing a collection of records. Wraps the `DataGrid` in a standard shell with empty and loading states.

```tsx
function OperativesListPage() {
  const { page, pageSize, setPageSize, handlePageChange, setPage } =
    usePaginationState({ initialPage: 1 });
  const { data, isLoading } = useFetchOperatives({ page, pageSize });
  const totalPages = Math.ceil((data?.totalCount ?? 0) / pageSize);

  return (
    <div className="cleen cleen-p-6 cleen-min-h-screen">
      <div className="cleen-flex cleen-justify-between cleen-items-center cleen-mb-6">
        <h1 className="cleen-text-2xl cleen-font-bold cleen-text-gray">
          Operatives
        </h1>
        <Button variant="primary" label="Add operative" onClick={openCreate} />
      </div>

      {isLoading ? (
        <SkeletonDataGrid variant="pulse" itemCount={8} />
      ) : !data?.operatives.length ? (
        <Card className="cleen-text-center cleen-py-16">
          <MdPeopleOutline
            size={48}
            className="cleen-text-gray/30 cleen-mx-auto cleen-mb-4"
          />
          <h3 className="cleen-text-lg cleen-font-semibold cleen-text-gray cleen-mb-2">
            No operatives yet
          </h3>
          <p className="cleen-text-sm cleen-text-gray/60 cleen-mb-4">
            Add your first operative to get started.
          </p>
          <Button
            variant="primary"
            label="Add operative"
            onClick={openCreate}
          />
        </Card>
      ) : (
        <>
          <DataGrid
            tableHeaders={headers}
            rowData={data.operatives}
            withSearch
          />
          {totalPages > 1 && (
            <div className="cleen-mt-4">
              <Pagination
                currentPage={page}
                totalPages={totalPages}
                pageSize={pageSize}
                onPageChange={handlePageChange}
                onPageSizeChange={size => {
                  setPageSize(size);
                  setPage(1);
                }}
              />
            </div>
          )}
        </>
      )}
    </div>
  );
}
```

**Notes:**

- Three states: loading → `SkeletonDataGrid`, empty → Card with CTA, populated → DataGrid + Pagination.
- Empty state lives in a `Card` centered — don't just put loose text on the page.
- `cleen-mt-4` between DataGrid and Pagination keeps them visually anchored together.

---

## Card Grid (flexible column count)

Reusable pattern for rendering a collection as a responsive card grid.

```tsx
// 3-column responsive grid (most common)
<div className="cleen-grid cleen-grid-cols-1 md:cleen-grid-cols-2 lg:cleen-grid-cols-3 cleen-gap-4">
  {items.map(item => (
    <Card key={item.id} hoverable onClick={() => navigate(`/items/${item.id}`)}>
      <h3 className="cleen-font-semibold cleen-text-gray cleen-mb-1">{item.title}</h3>
      <p className="cleen-text-sm cleen-text-gray/70">{item.description}</p>
      <div className="cleen-flex cleen-items-center cleen-gap-2 cleen-mt-3">
        <PillBadge label={item.status} color={statusColor[item.status]} showDot />
        <AvatarRow avatars={item.assignees} maxVisible={3} size="xs" />
      </div>
    </Card>
  ))}
</div>

// 4-column (KPI-style)
<div className="cleen-grid cleen-grid-cols-2 lg:cleen-grid-cols-4 cleen-gap-4">

// 2-column (chart pairs, form sections)
<div className="cleen-grid cleen-grid-cols-1 lg:cleen-grid-cols-2 cleen-gap-6">
```

---

## Card with Inline Row List

Common pattern for notification feeds, activity logs, or setting rows — items separated by Dividers inside a single Card.

```tsx
<Card header={{ title: 'Recent activity', hasDivider: true }}>
  {activityItems.map((item, index) => (
    <div key={item.id}>
      <div className="cleen-flex cleen-items-start cleen-gap-3 cleen-py-3">
        <Avatar name={item.user} size="sm" />
        <div className="cleen-flex-1 cleen-min-w-0">
          <p className="cleen-text-sm cleen-text-gray">
            <span className="cleen-font-medium">{item.user}</span> {item.action}
          </p>
          <p className="cleen-text-xs cleen-text-gray/50 cleen-mt-0.5">
            {item.timestamp}
          </p>
        </div>
        <PillBadge label={item.type} color={item.color} />
      </div>
      {index < activityItems.length - 1 && <Divider />}
    </div>
  ))}
</Card>
```

**Notes:**

- `index < items.length - 1 && <Divider />` — only renders a divider between items, not after the last one.
- `cleen-flex-1 cleen-min-w-0` on the text column prevents long text from overflowing the row.

---

## Danger Zone Card

Visually distinct section for destructive actions. Always at the bottom of a settings section.

```tsx
<Card color="var(--cleen-error)">
  <div className="cleen-flex cleen-justify-between cleen-items-center">
    <div>
      <h3 className="cleen-text-base cleen-font-semibold cleen-text-error">
        Danger zone
      </h3>
      <p className="cleen-text-sm cleen-text-gray/70 cleen-mt-1">
        Permanently deletes all data. This cannot be undone.
      </p>
    </div>
    <Button variant="danger" label="Delete account" onClick={openConfirm} />
  </div>
</Card>
```

**Notes:**

- `color="var(--cleen-error)"` automatically applies a reddish tint to the card background and border. No custom class needed.
- Pair with a confirmation `Modal` — never execute destructive actions on a single click.

---

## Mixed-width Grid (asymmetric columns)

A 3-column grid where the last item spans 2 columns — useful for paired small + large content.

```tsx
<div className="cleen-grid cleen-grid-cols-1 lg:cleen-grid-cols-3 cleen-gap-6">
  {/* Narrow card */}
  <Card header={{ title: 'Summary', hasDivider: true }}>
    <Chart
      type="donut"
      series={summarySeries}
      options={donutOpts}
      height={260}
    />
  </Card>

  {/* Wide card — spans 2 of 3 columns */}
  <Card
    className="lg:cleen-col-span-2"
    header={{ title: 'Detailed breakdown', hasDivider: true }}
  >
    <DataGrid tableHeaders={detailHeaders} rowData={detailRows} />
  </Card>
</div>
```

**Notes:**

- `lg:cleen-col-span-2` only kicks in at the `lg` breakpoint — on mobile, both cards stack full-width.
- This is the standard pattern for "summary + detail" side-by-side layout.
