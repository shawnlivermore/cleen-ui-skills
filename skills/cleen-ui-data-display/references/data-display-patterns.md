# Data Display Patterns

Full-page examples of common data-heavy UI layouts.

---

## Table of Contents

1. [Dashboard with Stat Cards + Charts](#dashboard-with-stat-cards--charts)
2. [User Management Table](#user-management-table)
3. [Kanban Project Board](#kanban-project-board)
4. [Analytics Page](#analytics-page)

---

## Dashboard with Stat Cards + Charts

A typical dashboard: top row of KPI stat cards, followed by a chart panel and a slim data table.

```tsx
import {
  Card, PillBadge, ProgressBar, Avatar,
  DataGrid, Loader, SkeletonWidgetCard, SkeletonChart,
} from '@cleen/cleen-components';
import { Chart, SimpleChart } from '@cleen/cleen-components/charts';
import type { TableHeaderProps } from '@cleen/cleen-components';

interface ActivityRow extends Record<string, unknown> {
  id: number;
  user: string;
  action: string;
  timestamp: string;
  status: string;
}

const activityHeaders: TableHeaderProps<ActivityRow>[] = [
  { field: 'user', headerElement: 'User' },
  { field: 'action', headerElement: 'Action' },
  { field: 'timestamp', headerElement: 'Time', width: 160 },
  { field: 'status', headerElement: 'Status', width: 120, align: 'center' },
];

interface StatCardProps {
  title: string;
  value: string;
  trend: number[];
  badge?: string;
  badgeColor?: string;
}

function StatCard({ title, value, trend, badge, badgeColor }: StatCardProps) {
  return (
    <Card p={20} gap={8} hoverable>
      <div className="cleen-flex cleen-items-center cleen-justify-between">
        <span className="cleen-text-sm cleen-text-gray/70">{title}</span>
        {badge && <PillBadge label={badge} color={badgeColor} variant="pill" />}
      </div>
      <div className="cleen-text-2xl cleen-font-bold">{value}</div>
      <SimpleChart data={trend} width={160} height={40} />
    </Card>
  );
}

interface DashboardProps {
  isLoading: boolean;
  activityRows: ActivityRow[];
  totalActivityPages: number;
  activityPage: number;
  onActivityPageChange: (page: number) => void;
}

export function Dashboard({
  isLoading,
  activityRows,
  totalActivityPages,
  activityPage,
  onActivityPageChange,
}: DashboardProps) {
  const revenueSeriesData = [44000, 55000, 48000, 61000, 58000, 72000];
  const userGrowthData = [120, 145, 162, 190, 210, 248];

  return (
    <div className="cleen-flex cleen-flex-col cleen-gap-6 cleen-p-6">

      {/* KPI stat cards */}
      <div className="cleen-grid cleen-grid-cols-4 cleen-gap-4">
        {isLoading ? (
          <>
            <SkeletonWidgetCard />
            <SkeletonWidgetCard />
            <SkeletonWidgetCard />
            <SkeletonWidgetCard />
          </>
        ) : (
          <>
            <StatCard title="Monthly Revenue" value="$72,400" trend={revenueSeriesData} badge="+12%" badgeColor="green" />
            <StatCard title="Active Users" value="2,481" trend={userGrowthData} badge="+8%" badgeColor="green" />
            <StatCard title="Churn Rate" value="3.2%" trend={[5, 4.5, 4.1, 3.8, 3.5, 3.2]} badge="-0.3%" badgeColor="blue" />
            <StatCard title="Open Tickets" value="47" trend={[30, 38, 42, 55, 50, 47]} badge="Stable" badgeColor="gray" />
          </>
        )}
      </div>

      {/* Charts row */}
      <div className="cleen-grid cleen-grid-cols-2 cleen-gap-4">
        {isLoading ? (
          <>
            <SkeletonChart />
            <SkeletonChart />
          </>
        ) : (
          <>
            <Card header={{ title: 'Revenue Over Time', hasDivider: true }} p={20}>
              <Chart
                type="area"
                series={[{ name: 'Revenue', data: revenueSeriesData }]}
                options={{
                  xaxis: { categories: ['Jan', 'Feb', 'Mar', 'Apr', 'May', 'Jun'] },
                  colors: ['rgba(var(--cleen-primary))'],
                  fill: { type: 'gradient' },
                  stroke: { curve: 'smooth' },
                }}
                height={220}
              />
            </Card>

            <Card header={{ title: 'Traffic Sources', hasDivider: true }} p={20}>
              <Chart
                type="donut"
                series={[44, 33, 23]}
                options={{ labels: ['Direct', 'Organic', 'Referral'] }}
                height={220}
              />
            </Card>
          </>
        )}
      </div>

      {/* Recent activity table */}
      <DataGrid
        title="Recent Activity"
        tableHeaders={activityHeaders}
        rowData={activityRows}
        isLoading={isLoading}
        withFooter
        totalPages={totalActivityPages}
        activePage={activityPage}
        onPageChange={onActivityPageChange}
        spacing="moderate"
        renderRow={(row) => ({
          status: <PillBadge label={row.status} colorByIndex={row.id as number} variant="pill" />,
          user: (
            <div className="cleen-flex cleen-items-center cleen-gap-2">
              <Avatar name={row.user} size="xs" />
              <span>{row.user}</span>
            </div>
          ),
        })}
      />
    </div>
  );
}
```

---

## User Management Table

A paginated, searchable, sortable table with row actions and custom cell rendering.

```tsx
import {
  DataGridWithFilters, PillBadge, Avatar,
  Button, Select, CheckboxGroup, useForm,
} from '@cleen/cleen-components';
import { IconEditable, IconTrash } from '@cleen/cleen-components';
import type { TableHeaderProps, ThreeDotMenuItem } from '@cleen/cleen-components';
import { usePaginationState } from '@cleen/cleen-components';
import { useState } from 'react';

interface UserRow extends Record<string, unknown> {
  id: number;
  name: string;
  email: string;
  role: string;
  status: 'active' | 'inactive' | 'pending';
  lastSeen: string;
}

const STATUS_COLOR: Record<string, string> = {
  active: 'green',
  inactive: 'gray',
  pending: 'yellow',
};

const headers: TableHeaderProps<UserRow>[] = [
  { field: 'name', headerElement: 'Name', sortable: true },
  { field: 'email', headerElement: 'Email', sortable: true },
  { field: 'role', headerElement: 'Role', width: 140 },
  { field: 'status', headerElement: 'Status', width: 120, align: 'center' },
  { field: 'lastSeen', headerElement: 'Last Seen', width: 160, sortable: true },
];

const menuItems: ThreeDotMenuItem<UserRow>[] = [
  { label: 'Edit', icon: <IconEditable />, onClick: (row) => console.log('edit', row) },
  {
    label: 'Delete',
    icon: <IconTrash />,
    onClick: (row) => console.log('delete', row),
    disabledOptionCallback: (row) => row.role === 'admin',
  },
];

interface Filters {
  roles: string[];
  statuses: string[];
}

interface UserTableProps {
  users: UserRow[];
  total: number;
  isLoading: boolean;
  onSortChange: (sort: Record<string, 'asc' | 'desc' | null>) => void;
  onSearch: (q: string) => void;
  onFiltersApply: (filters: Filters) => void;
}

export function UserTable({
  users, total, isLoading, onSortChange, onSearch, onFiltersApply,
}: UserTableProps) {
  const { page, pageSize, setPageSize, handlePageChange } = usePaginationState({ initialPage: 1, initialPageSize: 25 });
  const [search, setSearch] = useState('');
  const { form, setField } = useForm<Filters>({ defaultValue: { roles: [], statuses: [] } });

  const handleSearch = (q: string) => {
    setSearch(q);
    onSearch(q);
  };

  const ROLE_OPTIONS = [
    { id: 'admin', label: 'Admin' },
    { id: 'editor', label: 'Editor' },
    { id: 'viewer', label: 'Viewer' },
  ];

  const STATUS_OPTIONS = [
    { id: 'active', label: 'Active' },
    { id: 'inactive', label: 'Inactive' },
    { id: 'pending', label: 'Pending' },
  ];

  return (
    <DataGridWithFilters
      title="Users"
      tableHeaders={headers}
      rowData={users}
      isLoading={isLoading}
      withSearch
      withFooter
      searchInputValue={search}
      onSearchChange={handleSearch}
      onSortClick={onSortChange}
      activePage={page}
      totalPages={Math.ceil(total / pageSize)}
      pageSize={pageSize}
      setPageSize={setPageSize}
      onPageChange={handlePageChange}
      threeDotContextMenuOptions={menuItems}
      renderRow={(row) => ({
        name: (
          <div className="cleen-flex cleen-items-center cleen-gap-2">
            <Avatar name={row.name} size="sm" />
            <span>{row.name}</span>
          </div>
        ),
        status: (
          <PillBadge
            label={row.status}
            color={STATUS_COLOR[row.status]}
            showDot
            variant="pill"
          />
        ),
        role: <PillBadge label={row.role} colorByIndex={row.id as number} />,
      })}
      filtersConfig={{
        filters: (
          <div className="cleen-flex cleen-flex-col cleen-gap-6">
            <div>
              <p className="cleen-text-sm cleen-font-semibold cleen-mb-2">Role</p>
              <CheckboxGroup
                checkboxes={ROLE_OPTIONS.map(r => ({
                  id: r.id, label: r.label,
                  checked: form?.roles.includes(r.id) ?? false,
                }))}
                onChange={updated =>
                  setField('roles', updated.filter(c => c.checked).map(c => c.id as string))
                }
              />
            </div>
            <div>
              <p className="cleen-text-sm cleen-font-semibold cleen-mb-2">Status</p>
              <CheckboxGroup
                checkboxes={STATUS_OPTIONS.map(s => ({
                  id: s.id, label: s.label,
                  checked: form?.statuses.includes(s.id) ?? false,
                }))}
                onChange={updated =>
                  setField('statuses', updated.filter(c => c.checked).map(c => c.id as string))
                }
              />
            </div>
          </div>
        ),
        onApply: () => form && onFiltersApply(form),
      }}
      customTitleElements={
        <Button variant="primary" label="Invite User" />
      }
    />
  );
}
```

---

## Kanban Project Board

A drag-and-drop project board with columns, cards, and user assignments.

```tsx
import { KanbanBoard } from '@cleen/cleen-components';
import type { KanbanColumnData } from '@cleen/cleen-components';

const COLUMNS: KanbanColumnData[] = [
  {
    id: 'backlog',
    title: 'Backlog',
    cards: [
      {
        id: 'task-1',
        title: 'Redesign onboarding flow',
        description: 'Update the first-run experience for new users',
        tags: [{ id: 'ux', label: 'UX' }],
        assignees: [{ id: 'u1', name: 'Ash Lynx', src: '' }],
      },
      {
        id: 'task-2',
        title: 'API rate limiting',
        description: 'Implement per-tenant request throttling',
        tags: [{ id: 'backend', label: 'Backend' }],
        assignees: [],
      },
    ],
  },
  {
    id: 'in-progress',
    title: 'In Progress',
    cards: [
      {
        id: 'task-3',
        title: 'DataGrid sorting',
        description: 'Server-side multi-column sort support',
        tags: [{ id: 'feature', label: 'Feature' }],
        assignees: [{ id: 'u2', name: 'Eiji Okumura', src: '' }],
      },
    ],
  },
  {
    id: 'review',
    title: 'In Review',
    cards: [],
  },
  {
    id: 'done',
    title: 'Done',
    cards: [
      {
        id: 'task-4',
        title: 'Fix login redirect bug',
        tags: [{ id: 'bug', label: 'Bug' }],
        assignees: [],
      },
    ],
  },
];

interface ProjectBoardProps {
  onCardSave: (card: KanbanColumnData['cards'][0]) => void;
  onCardMove: (cardId: string, fromColumnId: string, toColumnId: string) => void;
}

export function ProjectBoard({ onCardSave, onCardMove }: ProjectBoardProps) {
  return (
    <KanbanBoard
      columns={COLUMNS}
      isDraggable
      canAddCards
      canAddColumns={false}
      onCardSave={onCardSave}
      onCardDragEnd={(cardId, fromCol, toCol) => onCardMove(String(cardId), String(fromCol), String(toCol))}
      filterUsers={[
        { id: 'u1', name: 'Ash Lynx', src: '' },
        { id: 'u2', name: 'Eiji Okumura', src: '' },
      ]}
      showAssigneesInFilter
    />
  );
}
```

---

## Analytics Page

A metrics-heavy page with multiple chart types, progress KPIs, and a summary table.

```tsx
import {
  Card, ProgressBar, ProgressCircle, PillBadge,
  DataGrid, SkeletonChart, SkeletonCard,
} from '@cleen/cleen-components';
import { Chart } from '@cleen/cleen-components/charts';
import type { TableHeaderProps } from '@cleen/cleen-components';

interface CampaignRow extends Record<string, unknown> {
  id: number;
  name: string;
  impressions: number;
  clicks: number;
  ctr: string;
  status: string;
}

const campaignHeaders: TableHeaderProps<CampaignRow>[] = [
  { field: 'name', headerElement: 'Campaign', sortable: true },
  { field: 'impressions', headerElement: 'Impressions', sortable: true, align: 'right' },
  { field: 'clicks', headerElement: 'Clicks', sortable: true, align: 'right' },
  { field: 'ctr', headerElement: 'CTR', width: 100, align: 'center' },
  { field: 'status', headerElement: 'Status', width: 120, align: 'center' },
];

interface AnalyticsPageProps {
  isLoading: boolean;
  campaigns: CampaignRow[];
}

export function AnalyticsPage({ isLoading, campaigns }: AnalyticsPageProps) {
  return (
    <div className="cleen-flex cleen-flex-col cleen-gap-6 cleen-p-6">

      {/* Performance KPIs */}
      <div className="cleen-grid cleen-grid-cols-3 cleen-gap-4">
        {isLoading ? (
          <><SkeletonCard /><SkeletonCard /><SkeletonCard /></>
        ) : (
          <>
            <Card p={24} gap={12}>
              <p className="cleen-text-sm cleen-text-gray/70">Email Open Rate</p>
              <ProgressCircle percentage={68} size="lg" />
            </Card>
            <Card p={24} gap={12}>
              <p className="cleen-text-sm cleen-text-gray/70">Goal Completion</p>
              <div className="cleen-flex cleen-flex-col cleen-gap-3">
                <ProgressBar title="Q1 Target" percentage={82} showPercentage renderAnimated />
                <ProgressBar title="Q2 Target" percentage={45} showPercentage renderAnimated />
                <ProgressBar title="Q3 Target" percentage={12} showPercentage renderAnimated />
              </div>
            </Card>
            <Card p={24} gap={8}>
              <p className="cleen-text-sm cleen-text-gray/70">Channels</p>
              <Chart
                type="pie"
                series={[41, 28, 19, 12]}
                options={{ labels: ['Email', 'Social', 'Search', 'Direct'] }}
                height={180}
              />
            </Card>
          </>
        )}
      </div>

      {/* Time-series chart */}
      {isLoading ? <SkeletonChart /> : (
        <Card header={{ title: 'Click-Through Rate (Last 90 Days)', hasDivider: true }} p={20}>
          <Chart
            type="line"
            series={[
              { name: 'Email', data: [3.1, 3.4, 3.2, 3.8, 4.1, 3.9, 4.4, 4.6, 4.3, 4.8, 5.0, 4.9] },
              { name: 'Social', data: [1.8, 2.0, 1.9, 2.2, 2.5, 2.3, 2.6, 2.8, 2.7, 3.0, 3.1, 3.3] },
            ]}
            options={{
              xaxis: { categories: ['Jan', 'Feb', 'Mar', 'Apr', 'May', 'Jun', 'Jul', 'Aug', 'Sep', 'Oct', 'Nov', 'Dec'] },
              stroke: { curve: 'smooth', width: 2 },
              yaxis: { labels: { formatter: (v: number) => `${v}%` } },
            }}
            height={280}
          />
        </Card>
      )}

      {/* Campaigns table */}
      <DataGrid
        title="Active Campaigns"
        tableHeaders={campaignHeaders}
        rowData={campaigns}
        isLoading={isLoading}
        withSearch
        spacing="moderate"
        renderRow={(row) => ({
          status: <PillBadge label={row.status} colorByIndex={row.id as number} showDot />,
          ctr: <span className="cleen-font-mono cleen-text-sm">{row.ctr}</span>,
        })}
      />
    </div>
  );
}
```
