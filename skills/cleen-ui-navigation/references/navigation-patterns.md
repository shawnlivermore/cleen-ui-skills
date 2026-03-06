# Navigation Patterns

Reusable, copy-paste-ready patterns for common navigation scenarios. These combine multiple components, hooks, and router integration into complete working flows.

---

## Table of Contents

1. [App Shell with Sidebar + Router](#app-shell-with-sidebar--router)
2. [Tabbed Settings Page](#tabbed-settings-page)
3. [Tabs Synced to URL](#tabs-synced-to-url)
4. [Multi-step Onboarding Wizard (with validation)](#multi-step-onboarding-wizard-with-validation)
5. [Stepper inside a Drawer (custom navigation)](#stepper-inside-a-drawer-custom-navigation)
6. [Paginated Table](#paginated-table)
7. [Breadcrumb wired to React Router](#breadcrumb-wired-to-react-router)
8. [Paginated Table with Filters (reset page on filter change)](#paginated-table-with-filters-reset-page-on-filter-change)

---

## App Shell with Sidebar + Router

Full app layout with sidebar navigation and an outlet for routed pages.

```tsx
import {
  Sidebar,
  DrawerContainer,
  DrawerContentTitle,
} from '@cleen/cleen-components';
import { Outlet, useNavigate, useLocation } from 'react-router-dom';

const navItems = [
  { id: 'home', label: 'Home', iconName: 'HouseLine' },
  {
    id: 'operations',
    label: 'Operations',
    iconName: 'BarChartSquare',
    badgeCount: 4,
  },
  { id: 'personnel', label: 'Personnel', iconName: 'Users' },
  { id: 'intel', label: 'Intel', iconName: 'FileText' },
];

const bottomItems = [
  { id: 'settings', label: 'Settings', iconName: 'Settings' },
];

function AppShell() {
  const navigate = useNavigate();
  const location = useLocation();

  // Derive the active sidebar item from the current route segment
  const activeId = location.pathname.split('/')[1] || 'home';

  const drawerContent = {
    operations: (
      <DrawerContainer title="Operations">
        <DrawerContentTitle
          title="Active missions"
          onClick={() => navigate('/operations/active')}
        />
        <DrawerContentTitle
          title="Completed"
          onClick={() => navigate('/operations/completed')}
        />
        <DrawerContentTitle
          title="Reports"
          onClick={() => navigate('/operations/reports')}
        />
      </DrawerContainer>
    ),
    personnel: (
      <DrawerContainer title="Personnel">
        <DrawerContentTitle
          title="All agents"
          onClick={() => navigate('/personnel')}
        />
        <DrawerContentTitle
          title="Inactive"
          onClick={() => navigate('/personnel/inactive')}
        />
        <DrawerContentTitle
          title="Recruits"
          onClick={() => navigate('/personnel/recruits')}
        />
      </DrawerContainer>
    ),
  };

  const handleActiveChange = (id: string | null) => {
    // Items without drawer content → navigate directly
    if (!drawerContent[id as keyof typeof drawerContent] && id) {
      navigate(`/${id}`);
    }
  };

  return (
    <div className="cleen cleen-flex cleen-h-screen">
      <Sidebar
        navigationItems={navItems}
        bottomNavigationItems={bottomItems}
        drawerContent={drawerContent}
        activeId={activeId}
        onActiveChange={handleActiveChange}
        logo={<img src="/logo.svg" alt="FOXHOUND" className="cleen-h-8" />}
        onLogoClick={() => navigate('/')}
        userAvatar={<img src={user.avatarUrl} alt={user.name} />}
        userInfo={
          <span className="cleen-text-sm cleen-font-medium">{user.name}</span>
        }
      />
      <main className="cleen-flex-1 cleen-overflow-auto cleen-p-6">
        <Outlet />
      </main>
    </div>
  );
}
```

**Pattern notes:**

- Derive `activeId` from the route rather than managing it as separate state — they must stay in sync.
- Items without a `drawerContent` entry navigate immediately via `onActiveChange`. Items with drawer content open the drawer; navigation happens when the user clicks a `DrawerContentTitle`.
- `DrawerContainer` + `DrawerContentTitle` are exported from `@cleen/cleen-components` — no custom components needed.

---

## Tabbed Settings Page

Multi-section settings page where all sections are independent (no order required).

```tsx
import { Tabs, Card } from '@cleen/cleen-components';

function SettingsPage() {
  return (
    <Card>
      <Tabs
        variant="padded"
        tabs={[
          {
            id: 'general',
            label: 'General',
            content: <GeneralSettings />,
          },
          {
            id: 'security',
            label: 'Security & Privacy',
            content: <SecuritySettings />,
          },
          {
            id: 'notifications',
            label: 'Notifications',
            badge: { label: 3 },
            content: <NotificationSettings />,
          },
          {
            id: 'billing',
            label: 'Billing',
            isDisabled: !hasBillingAccess,
            content: <BillingSettings />,
          },
        ]}
      />
    </Card>
  );
}
```

**Pattern notes:**

- Wrap tabs in a `Card` for proper scoping and visual framing.
- `badge` on a tab with a count drives attention to unread items — use `PillBadge` prop shape.
- `isDisabled` is purely presentational; also guard the content itself if needed.
- Uncontrolled mode is fine here — no need to sync to the URL for in-page tabs.

---

## Tabs Synced to URL

Tab state stored in the URL query string so users can share direct links to a specific tab.

```tsx
import { Tabs } from '@cleen/cleen-components';
import { useSearchParams } from 'react-router-dom';

const TAB_IDS = ['overview', 'missions', 'equipment', 'history'] as const;
type TabId = (typeof TAB_IDS)[number];

const tabs = [
  { id: 'overview', label: 'Overview', content: <Overview /> },
  { id: 'missions', label: 'Missions', content: <Missions /> },
  { id: 'equipment', label: 'Equipment', content: <Equipment /> },
  { id: 'history', label: 'History', content: <History /> },
];

function AgentDetailPage() {
  const [params, setParams] = useSearchParams();
  const tabId = (params.get('tab') as TabId) ?? 'overview';
  const currentTabIndex = Math.max(
    tabs.findIndex(t => t.id === tabId),
    0
  );

  return (
    <Tabs
      tabs={tabs}
      currentTabIndex={currentTabIndex}
      onTabChange={(_, tab) => {
        setParams(prev => {
          prev.set('tab', tab?.id ?? 'overview');
          return prev;
        });
      }}
    />
  );
}
```

**Pattern notes:**

- `setParams` with a function wraps existing params — prevents wiping out unrelated query params (like pagination or filters).
- Default to tab index `0` via `Math.max(..., 0)` so an invalid `?tab=` value doesn't crash.

---

## Multi-step Onboarding Wizard (with validation)

Step-by-step onboarding flow where each step is validated before the user can advance.

```tsx
import { Wizard, showNotification } from '@cleen/cleen-components';
import { useForm, useValidation } from '@cleen/cleen-components';

interface OnboardingData {
  codename: string;
  unit: string;
  clearanceLevel: string;
  termsAccepted: boolean;
}

function OnboardingWizard({
  onComplete,
}: {
  onComplete: (data: OnboardingData) => void;
}) {
  const [currentStep, setCurrentStep] = useState(0);
  const { form, setField } = useForm<OnboardingData>({
    defaultValue: {
      codename: '',
      unit: '',
      clearanceLevel: '',
      termsAccepted: false,
    },
  });
  const { errors, setError, clearErrors } = useValidation<OnboardingData>({});

  const validateStep = (stepIndex: number): boolean => {
    clearErrors();
    if (stepIndex === 0) {
      if (!form.codename.trim()) setError('codename', 'Codename is required');
      if (!form.unit) setError('unit', 'Unit assignment is required');
      return !!form.codename.trim() && !!form.unit;
    }
    if (stepIndex === 1) {
      if (!form.clearanceLevel)
        setError('clearanceLevel', 'Clearance level is required');
      return !!form.clearanceLevel;
    }
    if (stepIndex === 2) {
      if (!form.termsAccepted)
        setError('termsAccepted', 'You must accept the terms');
      return form.termsAccepted;
    }
    return true;
  };

  const handleStepChange = (nextIndex: number) => {
    // Only validate when advancing (not going back)
    if (nextIndex > currentStep && !validateStep(currentStep)) return;
    clearErrors();
    setCurrentStep(nextIndex);
  };

  const handleFinish = async () => {
    if (!validateStep(currentStep)) return;
    try {
      await onComplete(form);
    } catch {
      showNotification({
        message: 'Onboarding failed. Try again.',
        variant: 'error',
      });
    }
  };

  const steps = [
    {
      id: 'identity',
      title: 'Identity',
      subtitle: 'Your operative profile',
      content: (
        <div className="cleen-flex cleen-flex-col cleen-gap-4">
          <FormGroup title="Codename" required>
            <Input
              value={form.codename}
              onChange={e => setField('codename', e.target.value)}
              infoLabels={{ errorMessage: errors.codename }}
            />
          </FormGroup>
          <FormGroup title="Unit" required>
            <Select
              options={unitOptions}
              value={unitOptions.find(o => o.value === form.unit) ?? null}
              onChange={opt => setField('unit', opt?.value ?? '')}
              infoLabels={{ errorMessage: errors.unit }}
            />
          </FormGroup>
        </div>
      ),
    },
    {
      id: 'clearance',
      title: 'Clearance',
      subtitle: 'Access level assignment',
      content: (
        <FormGroup title="Clearance level" required>
          <RadioBoxGroup
            options={clearanceLevels}
            value={form.clearanceLevel}
            onChange={v => setField('clearanceLevel', v)}
          />
        </FormGroup>
      ),
    },
    {
      id: 'confirm',
      title: 'Confirm',
      subtitle: 'Review and submit',
      content: (
        <div className="cleen-flex cleen-flex-col cleen-gap-4">
          <ReviewSummary data={form} />
          <Checkbox
            label="I accept the operative terms and conditions"
            checked={form.termsAccepted}
            onChange={e => setField('termsAccepted', e.target.checked)}
            infoLabels={{ errorMessage: errors.termsAccepted }}
          />
        </div>
      ),
    },
  ];

  return (
    <div className="cleen-max-w-2xl cleen-mx-auto cleen-p-6">
      <Wizard
        steps={steps}
        currentStepIndex={currentStep}
        onStepChange={handleStepChange}
        onFinish={handleFinish}
        labels={{ nextBtn: 'Continue', previousBtn: 'Back' }}
      />
    </div>
  );
}
```

**Pattern notes:**

- `nextIndex > currentStep` check prevents running forward-validation when the user clicks Back.
- `clearErrors()` on every step change prevents stale errors from previous steps bleeding through.
- Each step's `content` is defined inside the component so it closes over `form`, `setField`, `errors`.
- `onFinish` fires on the last step's "Continue" click — wire your API call there.

---

## Stepper inside a Drawer (custom navigation)

Using `Stepper` alone when you need step progress inside an existing layout (like a Drawer) with your own next/back controls.

```tsx
import {
  Drawer,
  Stepper,
  Button,
  useDisclosure,
} from '@cleen/cleen-components';

const STEPS = [
  { title: 'Select target', subtitle: 'Choose objective' },
  { title: 'Assign agent', subtitle: 'Pick your operative' },
  { title: 'Set timeline', subtitle: 'Schedule deployment' },
];

function MissionBuilderDrawer() {
  const { isOpen, open, close } = useDisclosure();
  const [step, setStep] = useState(0);

  const handleNext = () => {
    if (step < STEPS.length - 1) setStep(s => s + 1);
    else handleSubmit();
  };

  const handleBack = () => setStep(s => Math.max(s - 1, 0));

  const handleClose = () => {
    setStep(0);
    close();
  };

  return (
    <>
      <Button label="New mission" onClick={open} />
      <Drawer
        isOpen={isOpen}
        onClose={handleClose}
        title="Mission builder"
        size="lg"
        footer={
          <div className="cleen-flex cleen-justify-between cleen-w-full">
            <Button
              variant="secondary"
              label="Back"
              onClick={handleBack}
              disabled={step === 0}
            />
            <Button
              variant="primary"
              label={step === STEPS.length - 1 ? 'Launch' : 'Next'}
              onClick={handleNext}
            />
          </div>
        }
      >
        <Stepper steps={STEPS} activeStep={step} orientation="horizontal" />
        <div className="cleen-mt-6">
          {step === 0 && <TargetSelector />}
          {step === 1 && <AgentPicker />}
          {step === 2 && <TimelineForm />}
        </div>
      </Drawer>
    </>
  );
}
```

**Pattern notes:**

- `Stepper` is purely visual here — `step` state lives outside it and drives both the stepper and content.
- Reset `step` to `0` on close so re-opening starts fresh.
- Use `disabled={step === 0}` on Back to prevent going below 0 at the UI level.
- This pattern is the right call when you need step progress inside a Drawer — `Wizard` doesn't compose into a Drawer footer layout.

---

## Paginated Table

Standard pattern for a list page with server-side pagination.

```tsx
import {
  DataGrid,
  Pagination,
  usePaginationState,
} from '@cleen/cleen-components';
import type { TableHeaderProps } from '@cleen/cleen-components';

interface Agent {
  id: number;
  codename: string;
  status: string;
  unit: string;
  clearance: string;
}

const headers: TableHeaderProps<Agent>[] = [
  { field: 'codename', headerElement: 'Codename', sortable: true },
  { field: 'unit', headerElement: 'Unit' },
  { field: 'status', headerElement: 'Status', width: 120, align: 'center' },
  { field: 'clearance', headerElement: 'Clearance', width: 120 },
];

function AgentsPage() {
  const { page, pageSize, setPageSize, handlePageChange } = usePaginationState({
    initialPage: 1,
    initialPageSize: 20,
  });
  const { data, isLoading } = useFetchAgents({ page, pageSize });

  const totalPages = Math.ceil((data?.totalCount ?? 0) / pageSize);

  return (
    <div className="cleen cleen-flex cleen-flex-col cleen-gap-4">
      <DataGrid
        title="Agents"
        tableHeaders={headers}
        rowData={data?.agents ?? []}
        isLoading={isLoading}
        withSearch
      />
      {totalPages > 1 && (
        <Pagination
          currentPage={page}
          totalPages={totalPages}
          pageSize={pageSize}
          pageSizes={[10, 20, 50]}
          onPageChange={handlePageChange}
          onPageSizeChange={newSize => {
            setPageSize(newSize);
            handlePageChange(1); // reset to page 1 when page size changes
          }}
        />
      )}
    </div>
  );
}
```

**Pattern notes:**

- `{totalPages > 1 &&` — hide Pagination when there's only one page; it adds noise with no value.
- `handlePageChange(1)` in `onPageSizeChange` — changing page size must reset to page 1 or you'll request a page that no longer exists.
- `Math.ceil(... ?? 0)` — guard against undefined `totalCount` during initial load.

---

## Breadcrumb wired to React Router

Auto-generated breadcrumbs from the current route location.

```tsx
import { Breadcrumb } from '@cleen/cleen-components';
import { useLocation, useParams } from 'react-router-dom';

// Route: /operations/:missionId/debriefing
// Renders: Home > Operations > Shadow Moses > Debriefing

function MissionBreadcrumb() {
  const { missionId } = useParams<{ missionId: string }>();
  const { data: mission } = useFetchMission(missionId);

  return (
    <Breadcrumb
      segments={[
        { path: '/operations', label: 'Operations' },
        {
          path: `/operations/${missionId}`,
          label: mission?.title ?? missionId,
        },
        { path: `/operations/${missionId}/debriefing`, label: 'Debriefing' },
      ]}
    />
  );
}
```

**Dynamic breadcrumb hook (for deeply nested routes):**

```tsx
// Build segments by splitting the pathname and resolving labels
function useBreadcrumbSegments(): BreadcrumbSegment[] {
  const location = useLocation();
  const segments = location.pathname.split('/').filter(Boolean);

  return segments.map((segment, index) => ({
    path: '/' + segments.slice(0, index + 1).join('/'),
    label: ROUTE_LABELS[segment] ?? segment, // ROUTE_LABELS maps route keys to human-readable names
  }));
}
```

**Pattern notes:**

- `mission?.title ?? missionId` gracefully falls back to the raw ID while the data loads.
- The last segment is auto-styled as the current page — no need to mark it separately.
- For apps with dynamic routes, a `ROUTE_LABELS` map avoids leaking raw slugs/IDs into the breadcrumb.

---

## Paginated Table with Filters (reset page on filter change)

Combining pagination with filter state — this is the most common pattern that breaks when not handled carefully.

```tsx
function FilteredAgentList() {
  const { page, pageSize, setPageSize, handlePageChange, setPage } =
    usePaginationState({ initialPage: 1 });
  const [filters, setFilters] = useState<AgentFilters>({
    status: [],
    unit: null,
  });

  // Reset page whenever filters change — critical! (¬_¬)
  const applyFilters = (newFilters: AgentFilters) => {
    setFilters(newFilters);
    setPage(1);
  };

  const { data, isLoading } = useFetchAgents({ page, pageSize, ...filters });
  const totalPages = Math.ceil((data?.totalCount ?? 0) / pageSize);

  return (
    <>
      <FilterControls onApply={applyFilters} />
      <DataGrid
        tableHeaders={headers}
        rowData={data?.agents ?? []}
        isLoading={isLoading}
      />
      {totalPages > 1 && (
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
      )}
    </>
  );
}
```

**Pattern notes:**

- `setPage(1)` inside `applyFilters` is the key insight — any change to query parameters must reset the page. Skipping this causes "page 5 with 3 total pages" states after filtering.
- Also reset on `onPageSizeChange` — a change in page size recalculates `totalPages`.
- Keep `filters` and `page`/`pageSize` in separate state — they have different reset lifecycles.
