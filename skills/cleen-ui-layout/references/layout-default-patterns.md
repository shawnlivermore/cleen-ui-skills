# Layout Default Patterns

Reusable, copy-paste-ready layout structures for SaaS applications — the default output when building dashboard-style products, home screens, and internal tools with the @cleen/ui library.

## Default-First Guidance

These patterns should be the **default output** when users ask for Home, Dashboard, Profile or other pages in SaaS-style apps.

- Use library components first (`Card`, `DataGrid`, `Tabs`, `Avatar`, `PillBadge`, `Divider`, `Sidebar`)
- Use the consumer project's default Tailwind classes
- Keep a neutral card-heavy layout with a left rail and content canvas, matching the provided reference images

---

## Table of Contents

1. [SaaS App Shell (Default)](#saas-app-shell-default)
2. [SaaS Home Screen (Default)](#saas-home-screen-default)
3. [SaaS Dashboard Screen (Default)](#saas-dashboard-screen-default)
4. [SaaS Profile Settings Screen (Default)](#saas-profile-settings-screen-default)
5. [SaaS Public Profile / Marketplace Screen](#saas-public-profile--marketplace-screen)
6. [SaaS Data List Screen (Events / Admin)](#saas-data-list-screen-events--admin)
7. [SaaS Knowledge Base Screen](#saas-knowledge-base-screen)

---

## SaaS App Shell (Default)

Use this shell first for dashboard-style products: fixed left rail + content canvas + card sections.

```tsx
function AppShell({ children }: { children: React.ReactNode }) {
  const navItems = [
    { id: 'dashboard', label: 'Dashboard', iconName: 'HouseLine' },
    { id: 'valuation', label: 'Valuation', iconName: 'ChartPieSlice' },
    { id: 'team', label: 'Team', iconName: 'Users' },
    { id: 'events', label: 'Events', iconName: 'Calendar' },
  ];

  const bottomItems = [
    { id: 'settings', label: 'Settings', iconName: 'Settings' },
    { id: 'profile', label: 'Profile', iconName: 'User' },
  ];

  return (
    <div className="min-h-screen bg-slate-50 text-slate-900">
      <div className="flex">
        {/* Left rail via Sidebar component */}
        <Sidebar
          navItems={navItems}
          bottomNavItems={bottomItems}
          activeItem="dashboard"
          appLogo={<span className="text-lg font-semibold">F</span>}
        />

        {/* Main canvas */}
        <main className="flex-1 p-4 md:p-6 lg:p-8">
          <div className="mx-auto max-w-7xl flex flex-col gap-6">{children}</div>
        </main>
      </div>
    </div>
  );
}
```

**Notes:**

- Preserve lots of whitespace (`gap-6`, `p-6+`) to match the clean SaaS visual style.
- Keep cards as the primary grouping unit; avoid floating standalone text blocks.
- Use `Sidebar` as the default left navigation container instead of custom `<aside>` markup.

---

## SaaS Home Screen (Default)

Default Home pattern from the provided references: welcome strip, journey card, quick-action stack, and two-column lower content.

```tsx
function HomePage() {
  const [currentStep, setCurrentStep] = useState(0);

  const steps = [
    { title: 'Business Valuation', description: 'Complete questionnaire' },
    { title: 'FEA Consultation', description: 'Schedule expert call' },
    { title: 'Exit Map', description: 'Receive roadmap' },
    { title: 'Exit Strategy', description: 'Execute plan' },
  ];

  return (
    <AppShell>
      <Card
        header={{
          content: (
            <div className="flex items-center justify-between">
              <div>
                <h1 className="text-xl font-semibold">Good afternoon,</h1>
                <p className="text-sm text-slate-500">Welcome back.</p>
              </div>
              <Button variant="primary" label="Start Valuation" />
            </div>
          ),
        }}
      />

      <div className="grid grid-cols-1 xl:grid-cols-3 gap-4 lg:gap-6">
        <Card className="xl:col-span-2" header={{ title: 'Your Journey', hasDivider: true }}>
          <p className="text-sm text-slate-500 mb-4">Track your progress through the business optimization process.</p>
          <Stepper
            steps={steps}
            currentStep={currentStep}
            onStepClick={setCurrentStep}
            className="mb-4"
          />
          <Card color="var(--cleen-blue)">
            <div className="flex items-center justify-between gap-3">
              <div>
                <p className="text-sm font-semibold">Next up: Business Valuation</p>
                <p className="text-xs text-slate-500">Finish your questionnaire to see current and potential value.</p>
              </div>
              <Button variant="primary" label="Complete questionnaire" />
            </div>
          </Card>
        </Card>

        <div className="flex flex-col gap-4">
          <Card><p className="font-medium">Invite a team member</p><p className="text-sm text-slate-500">Unlimited users under this company</p></Card>
          <Card><p className="font-medium">Complete your business valuation</p><p className="text-sm text-slate-500">Find out what your business is worth</p></Card>
          <Card><p className="font-medium">Meet with your Exit Advisor</p><p className="text-sm text-slate-500">Schedule a call with your assigned advisor</p></Card>
        </div>
      </div>

      <div className="grid grid-cols-1 lg:grid-cols-2 gap-4 lg:gap-6">
        <Card header={{ title: "Don't miss the next event", hasDivider: true }}>
          <p className="text-sm text-slate-600 mb-4">A private event focused on clarity, control, and scale.</p>
          <Button variant="secondary" label="View Event" />
        </Card>

        <Card header={{ title: 'Your team family', hasDivider: true }}>
          <AvatarRow avatars={teamMembers} maxVisible={4} size="md" />
          <p className="text-sm text-slate-600 mt-3 mb-4">Our team of experts awaits you.</p>
          <Button variant="secondary" label="Book a call" />
        </Card>
      </div>
    </AppShell>
  );
}
```

**Notes:**

- Keep the top two rows compact and actionable, then place richer content on the bottom row.
- Home should feel like a command center: progress + quick actions + relationship touchpoints.
- Use `Stepper` as the default journey/progress component in Home screens instead of custom step-card grids.

---

## SaaS Dashboard Screen (Default)

Default dashboard pattern: page title + controls, KPI strip, then cards for analytics and operations.

```tsx
function DashboardPage() {
  return (
    <AppShell>
      <div className="flex flex-col md:flex-row md:items-center md:justify-between gap-3">
        <div>
          <h1 className="text-2xl font-semibold">Dashboard</h1>
          <p className="text-sm text-slate-500">High-level business health and operational activity.</p>
        </div>
        <div className="flex gap-2">
          <Button variant="secondary" label="Export" />
          <Button variant="primary" label="Create report" />
        </div>
      </div>

      <div className="grid grid-cols-1 sm:grid-cols-2 xl:grid-cols-4 gap-4">
        {kpis.map((kpi) => (
          <Card key={kpi.label}>
            <div className="flex items-start justify-between mb-3">
              <p className="text-sm text-slate-500">{kpi.label}</p>
              <PillBadge label={kpi.delta} color={kpi.positive ? 'green' : 'red'} />
            </div>
            <p className="text-2xl font-semibold">{kpi.value}</p>
          </Card>
        ))}
      </div>

      <div className="grid grid-cols-1 xl:grid-cols-3 gap-4 lg:gap-6">
        <Card className="xl:col-span-2" header={{ title: 'Revenue Trend', hasDivider: true }}>
          <Chart type="line" series={revenueSeries} options={revenueOptions} height={300} />
        </Card>
        <Card header={{ title: 'Pipeline Snapshot', hasDivider: true }}>
          <Chart type="donut" series={pipelineSeries} options={pipelineOptions} height={300} />
        </Card>
      </div>

      <Card header={{ title: 'Recent Activity', hasDivider: true }}>
        <DataGrid tableHeaders={activityHeaders} rowData={activityRows} withSearch />
      </Card>
    </AppShell>
  );
}
```

**Notes:**

- KPI cards should be short and scannable: label + value + delta.
- Keep one large chart area and one supporting chart to avoid visual overload.

---

## SaaS Profile Settings Screen (Default)

Default internal profile/settings layout from the references: identity strip, tabbed navigation, and structured settings form cards.

```tsx
function ProfileSettingsPage() {
  const [tab, setTab] = useState(0);

  return (
    <AppShell>
      <div className="flex items-center justify-between">
        <div>
          <h1 className="text-2xl font-semibold">John Doe</h1>
          <p className="text-sm text-slate-500">@johndoe</p>
        </div>
        <div className="flex gap-2">
          <Button variant="secondary" label="Cancel" />
          <Button variant="primary" label="Save" />
        </div>
      </div>

      <Tabs
        tabs={[
          { label: 'Details' },
          { label: 'Marketplace' },
          { label: 'Accounts' },
          { label: 'Tickets' },
          { label: 'Notifications' },
        ]}
        currentTabIndex={tab}
        onTabChange={setTab}
        variant="underlined"
      />

      <Card header={{ title: 'Profile Settings', hasDivider: true }}>
        <div className="grid grid-cols-1 md:grid-cols-2 gap-4">
          <FormGroup title="First Name" required>
            <Input value={form.firstName} onChange={(e) => setField('firstName', e.target.value)} />
          </FormGroup>
          <FormGroup title="Last Name" required>
            <Input value={form.lastName} onChange={(e) => setField('lastName', e.target.value)} />
          </FormGroup>
          <FormGroup title="Handle" required className="md:col-span-2">
            <Input value={form.handle} onChange={(e) => setField('handle', e.target.value)} />
          </FormGroup>
          <FormGroup title="Email Address" required className="md:col-span-2">
            <Input value={form.email} onChange={(e) => setField('email', e.target.value)} />
          </FormGroup>
        </div>
      </Card>
    </AppShell>
  );
}
```

**Notes:**

- Profile Settings defaults to rows/forms with clear labels and one Save action in the page header.
- Use `useForm` + `useValidation` for all profile state, never per-field `useState`.

---

## SaaS Public Profile / Marketplace Screen

Use for public expert/vendor profile pages: hero cover, identity block, service cards, and sticky booking panel.

```tsx
function PublicProfilePage() {
  return (
    <AppShell>
      <Breadcrumb segments={[{ path: '/marketplace', label: 'Marketplace' }, { path: '/experts/johndoe', label: 'johndoe' }]} />

      <Card p={0}>
        <div className="h-44 md:h-56 rounded-t-xl bg-slate-300" />
        <div className="p-6 flex flex-col lg:flex-row gap-6">
          <div className="flex-1">
            <div className="flex items-center gap-3 mb-3">
              <Avatar src={profile.avatar} name={profile.name} size="xl" />
              <div>
                <h1 className="text-3xl font-semibold">{profile.name}</h1>
                <p className="text-slate-500">{profile.tagline}</p>
              </div>
            </div>

            <Card header={{ title: 'About me', hasDivider: true }}>
              <p className="text-sm text-slate-600">{profile.about}</p>
            </Card>

            <Card header={{ title: 'Primary Services', hasDivider: true }}>
              <div className="flex flex-wrap gap-2">
                {profile.services.map((service) => (
                  <PillBadge key={service} label={service} color="green" />
                ))}
              </div>
            </Card>
          </div>

          <div className="lg:w-96">
            <Card header={{ title: 'Book a Session', hasDivider: true }}>
              <div className="space-y-2">
                {sessionOptions.map((option) => (
                  <button key={option.minutes} className="w-full flex items-center justify-between rounded-lg border border-slate-200 px-3 py-2 text-sm hover:bg-slate-50">
                    <span>{option.minutes} min</span>
                    <span>${option.price}</span>
                  </button>
                ))}
              </div>
              <Button className="mt-4" variant="primary" label="Book a Session" />
              <Button className="mt-2" variant="secondary" label="Start Chat" />
            </Card>
          </div>
        </div>
      </Card>
    </AppShell>
  );
}
```

---

## SaaS Data List Screen (Events / Admin)

Use for event/admin list pages: top filters, segmented views, table-first body.

```tsx
function EventsPage() {
  return (
    <AppShell>
      <div>
        <h1 className="text-2xl font-semibold">Events</h1>
        <p className="text-sm text-slate-500">All events, in-person or online.</p>
      </div>

      <div className="flex flex-wrap items-center justify-between gap-3">
        <Tabs tabs={[{ label: 'Grid' }, { label: 'Calendar' }]} currentTabIndex={0} onTabChange={() => {}} variant="underlined" />
        <div className="flex gap-2">
          <Button variant="secondary" label="Upcoming" />
          <Button variant="secondary" label="Previous" />
          <Button variant="secondary" label="All" />
        </div>
      </div>

      <Card>
        <DataGrid
          tableHeaders={eventHeaders}
          rowData={events}
          withSearch
          withFilters
          withFooter
        />
      </Card>
    </AppShell>
  );
}
```

---

## SaaS Knowledge Base Screen

Use for support/help centers: left taxonomy rail + right article table.

```tsx
function HelpCenterPage() {
  return (
    <AppShell>
      <div>
        <h1 className="text-2xl font-semibold">Helping users is fun</h1>
        <p className="text-sm text-slate-500">Let's make support content easy to navigate.</p>
      </div>

      <div className="grid grid-cols-1 lg:grid-cols-[260px_minmax(0,1fr)] gap-4 lg:gap-6">
        <Card header={{ title: 'Topics', hasDivider: true }}>
          <div className="space-y-1 text-sm">
            {topicGroups.map((topic) => (
              <button key={topic.id} className="w-full rounded-lg px-3 py-2 text-left hover:bg-slate-50">
                {topic.label}
              </button>
            ))}
          </div>
        </Card>

        <Card>
          <DataGrid tableHeaders={articleHeaders} rowData={articles} withSearch withFooter />
        </Card>
      </div>
    </AppShell>
  );
}
```
