# Data Grid Page Templates

Instead of building raw unstyled tables and lists, **always use these page templates** as the foundation for data-heavy application screens.

## 1. The `DataGrid` Page Template

This is the standard layout for list/table screens. It features Breadcrumbs, Page Header, optional Tabs (for mode switching), an Action Button (Add Entry), and the `DataGrid` (or `DataGridWithFilters`).

```tsx
import { useMemo, useState } from 'react';
import { Button, IconBoxLines, IconTrash, IconUser, Tabs } from '@cleen/ui';
import { DataGrid } from '@cleen/ui-pro';
import { MainLayout } from '@/components/MainLayout';
// Provide these in your mock/data file
import { assessmentsData, pageTabs, tableHeaders, tabs } from '@/mock/data';

export const DataGridPage = () => {
  const [page, setPage] = useState(1);
  const [pageSize, setPageSize] = useState(10);
  const [searchText, setSearchText] = useState('');
  const [statusTabKey, setStatusTabKey] = useState(0);

  const filteredRows = useMemo(() => {
    const tabId = tabs[statusTabKey]?.id ?? 'all';

    // Adapt this filtering logic based on your mock data structure
    return assessmentsData.data.filter(row => {
      const matchesSearch =
        searchText.trim().length === 0 ||
        row.company.toLowerCase().includes(searchText.toLowerCase()) ||
        row.owner.toLowerCase().includes(searchText.toLowerCase()) ||
        row.industry.toLowerCase().includes(searchText.toLowerCase());

      const matchesTab = tabId === 'all' || row.status === tabId;

      return matchesSearch && matchesTab;
    });
  }, [searchText, statusTabKey]);

  const paginatedRows = useMemo(() => {
    const start = (page - 1) * pageSize;
    return filteredRows.slice(start, start + pageSize);
  }, [filteredRows, page, pageSize]);

  const totalPages = Math.max(1, Math.ceil(filteredRows.length / pageSize));

  const renderRow = (row: (typeof assessmentsData.data)[number]) => ({
    ...row,
    status: (
      <span
        className={`inline-flex items-center rounded-full px-2 py-1 text-xs font-semibold ${
          row.status === 'approved'
            ? 'bg-success/10 text-success'
            : row.status === 'archived'
              ? 'bg-gray/15 text-gray/80'
              : 'bg-warning/10 text-warning'
        }`}
      >
        {row.status}
      </span>
    ),
  });

  return (
    <MainLayout
      breadcrumbs={[
        { label: 'Home', path: '/' },
        { label: 'Data', path: '/data' },
      ]}
      title="Data Management"
      subtitle="View and manage system records and operational metrics."
    >
      <div className="flex justify-between gap-4 mb-4">
        <Tabs variant="padded" tabs={pageTabs} className="w-full" />

        <Button variant="secondary">Add Entry</Button>
      </div>

      <DataGrid
        tableHeaders={tableHeaders}
        rowData={paginatedRows}
        renderRow={renderRow}
        totalPages={totalPages}
        activePage={page}
        onPageChange={setPage}
        pageSize={pageSize}
        setPageSize={setPageSize}
        onSortClick={() => {}}
        searchInputValue={searchText}
        onSearchChange={setSearchText}
        isSearchDebounced
        withRefreshButton
        onRefreshButtonClick={() => {}}
        withSearch
        withFooter
        customFilterElements={
          <Tabs
            variant="padded"
            tabs={tabs}
            currentTabIndex={statusTabKey}
            onTabChange={setStatusTabKey}
            classNames={{
              list: '!mb-0',
            }}
          />
        }
        threeDotContextMenuOptions={[
          {
            label: 'View Profile',
            icon: <IconUser className="size-5 text-gray/70" />,
            onClick: () => {},
          },
          {
            label: 'Archive',
            icon: <IconBoxLines className="size-5 text-gray/70" />,
            onClick: () => {},
          },
          {
            label: 'Delete',
            icon: <IconTrash className="size-5 text-gray/70" />,
            onClick: () => {},
          },
        ]}
      />
    </MainLayout>
  );
};
```

## 2. The `DataGridPageAlt` (Sidebar Filters Layout)

This template places vertical tabs on the left edge serving as quick filters for the `DataGrid` on the right side. It is extremely effective for displaying varied types or statuses without polluting the standard Table header filter actions row.

```tsx
import { useMemo, useState } from 'react';
import { IconBoxLines, IconTrash, Tabs } from '@cleen/ui';
import { cn } from '@cleen/ui-core';
import { DataGrid } from '@cleen/ui-pro';
import { MainLayout } from '@/components/MainLayout';
// Provide these in your mock/data file
import { dataGridData, statusTabs, tableHeaders, typeTabs } from '@/mock/data';

export const DataGridPageAlt = () => {
  const [page, setPage] = useState(1);
  const [pageSize, setPageSize] = useState(10);
  const [searchText, setSearchText] = useState('');

  const [statusTabKey, setStatusTabKey] = useState(0);
  const [typeTabKey, setTypeTabKey] = useState(0);

  const filteredRows = useMemo(() => {
    const statusTabId = statusTabs[statusTabKey]?.id ?? 'all';
    const typeTabId = typeTabs[typeTabKey]?.id ?? 'all';

    return dataGridData.data.filter(row => {
      const matchesSearch =
        searchText.trim().length === 0 ||
        row.name.toLowerCase().includes(searchText.toLowerCase());

      const matchesStatusTab =
        statusTabId === 'all' || row.status === statusTabId;
      const matchesTypeTab = typeTabId === 'all' || row.type === typeTabId;

      return matchesSearch && matchesStatusTab && matchesTypeTab;
    });
  }, [searchText, statusTabKey, typeTabKey]);

  const paginatedRows = useMemo(() => {
    const start = (page - 1) * pageSize;
    return filteredRows.slice(start, start + pageSize);
  }, [filteredRows, page, pageSize]);

  const totalPages = Math.max(1, Math.ceil(filteredRows.length / pageSize));

  const renderRow = (row: (typeof dataGridData.data)[number]) => ({
    ...row,
    status: (
      <span
        className={cn(
          'inline-flex items-center rounded-full px-2 py-1 text-xs font-semibold',
          {
            'bg-success/10 text-success': row.status === 'active',
            'bg-gray/15 text-gray/80': row.status === 'archived',
          }
        )}
      >
        {row.status}
      </span>
    ),
  });

  return (
    <MainLayout
      breadcrumbs={[
        { label: 'Home', path: '/' },
        { label: 'Data', path: '/data' },
      ]}
      title="Data Management"
      subtitle="View and manage system records and operational metrics."
    >
      <div className="flex items-start justify-start gap-5">
        <div className="flex flex-col gap-6">
          <Tabs
            direction="vertical"
            tabs={statusTabs}
            className="w-[200px]"
            onTabChange={setStatusTabKey}
          />

          <Tabs
            direction="vertical"
            tabs={typeTabs}
            className="w-[200px]"
            onTabChange={setTypeTabKey}
          />
        </div>

        <DataGrid
          tableHeaders={tableHeaders}
          rowData={paginatedRows}
          renderRow={renderRow}
          totalPages={totalPages}
          activePage={page}
          onPageChange={setPage}
          pageSize={pageSize}
          setPageSize={setPageSize}
          onSortClick={() => {}}
          searchInputValue={searchText}
          onSearchChange={setSearchText}
          isSearchDebounced
          withRefreshButton
          onRefreshButtonClick={() => {}}
          withSearch
          withFooter
          threeDotContextMenuOptions={[
            {
              label: 'Archive',
              icon: (
                <IconBoxLines className="size-5 text-gray/70" />
              ),
              onClick: () => {},
            },
            {
              label: 'Delete',
              icon: <IconTrash className="size-5 text-gray/70" />,
              onClick: () => {},
            },
          ]}
        />
      </div>
    </MainLayout>
  );
};
```

## 3. The `DoubleDataGridPage` (Master-Detail Side-by-Side)

This layout displays a narrower list/grid on the left pane and expands its relationship children directly into a larger, parallel `DataGrid` adjacent on the right.

```tsx
import { useMemo, useState } from 'react';
import { Button, IconPlus, Tabs } from '@cleen/ui';
import { cn } from '@cleen/ui-core';
import { DataGrid } from '@cleen/ui-pro';
import { MainLayout } from '@/components/MainLayout';
// Provide these in your mock/data file
import { dataGridData, roleTableHeaders, tabs, usersTableHeaders } from '@/mock/data';

export const DoubleDataGridPage = () => {
  const [page, setPage] = useState(1);

  const [selectedRoleID, setSelectedRoleID] = useState<number | null>(null);
  const [roleSearch, setRoleSearch] = useState('');

  const selectedRole = useMemo(() => {
    return dataGridData?.data?.find(role => role?.id === selectedRoleID);
  }, [selectedRoleID]);

  return (
    <MainLayout
      breadcrumbs={[
        { label: 'Home', path: '/' },
        { label: 'Roles', path: '/roles' },
      ]}
      title="Data Management"
      subtitle="View and manage system records and operational metrics."
    >
      <Tabs variant="padded" tabs={tabs} className="w-full" />

      <div className="flex items-start justify-start gap-6 w-full mt-4">
        <DataGrid
          isScrollable
          tableHeaders={roleTableHeaders}
          rowData={dataGridData.data}
          title="Roles"
          titleBadgeOptions={{
            label: dataGridData?.data?.length.toString() ?? '0',
            color: 'blue',
          }}
          customTitleElements={
            <Button
              variant="secondary-blue"
              label="Add Role"
              leftIcon={<IconPlus className="text-primary" />}
            />
          }
          activeRowIndex={dataGridData?.data?.findIndex(
            role => role?.id === selectedRoleID
          )}
          onRowClick={(_, rowIndex) => {
            setSelectedRoleID(dataGridData?.data?.[+rowIndex]?.id);
          }}
          withSearch
          isSearchDebounced
          searchInputValue={roleSearch}
          onSearchChange={setRoleSearch}
          style={{
            width: 'unset',
          }}
          className="flex-1"
          classNames={{
            searchAndFilters: '[&>.cleen]:w-full',
            searchInput: 'w-full',
            row: 'hover:bg-primary/10 cursor-pointer',
            activeRow: 'bg-primary/10',
          }}
        />

        <div
          className={cn(
            'flex-1 flex flex-col gap-3 w-full',
            {
              'invisible': !selectedRoleID,
            }
          )}
        >
          <Tabs
            tabs={[
              {
                id: 'role-assignments',
                label: 'Role assignments',
                badge: {
                  label: selectedRole?.assignees?.length.toString() ?? '0',
                },
              },
              {
                id: 'history',
                label: 'History',
              },
            ]}
          />

          <DataGrid
            tableHeaders={usersTableHeaders}
            rowData={selectedRole?.assignees ?? []}
            title="Assigned Users"
            subtitle="These users are assigned to the role"
            titleBadgeOptions={{
              label: selectedRole?.assignees?.length.toString() ?? '0',
              color: 'blue',
            }}
            customTitleElements={
              <Button
                variant="secondary-blue"
                label="Assign User"
                leftIcon={<IconPlus className="text-primary" />}
              />
            }
            withFooter
            activePage={page}
            onPageChange={setPage}
          />
        </div>
      </div>
    </MainLayout>
  );
};
```

## 4. The `TripleDataGridPage` (Cascading Hierarchy)

This handles a 3-tier relationship natively. Selection rolls from left-to-right (e.g. Department -> Team -> Employee) utilizing the `invisible` utility selectively on unpopulated columns to reserve structural widths smoothly.

```tsx
import { useState } from 'react';
import { Button, IconEditable, IconSearch, IconTrash, Input, Tabs } from '@cleen/ui';
import { cn } from '@cleen/ui-core';
import { DataGrid } from '@cleen/ui-pro';
import { MainLayout } from '@/components/MainLayout';
// Provide these in your mock file
import { departments, departmentsTableHeaders, employeesTableHeaders, tabs, teamsTableHeaders, type Department, type Team } from '@/mock/data';

export const TripleDataGridPage = () => {
  const [selectedDepartment, setSelectedDepartment] = useState<Department | null>(null);
  const [selectedTeam, setSelectedTeam] = useState<Team | null>(null);

  const renderDepartmentRow = (department: Department) => ({
    name: (
      <div>
        <p>{department.name}</p>
        <p className="text-gray/50 text-xs">{department.teams.length} Teams</p>
      </div>
    ),
  });

  const renderTeamRow = (team: Team) => ({
    name: (
      <div>
        <p>{team.name}</p>
        <p className="text-gray/50 text-xs">{team.employees.length} Employees</p>
      </div>
    ),
  });

  return (
    <MainLayout
      breadcrumbs={[
        { label: 'Home', path: '/' },
        { label: 'HR', path: '/human-resources' },
      ]}
      title="Data Management"
      subtitle="View and manage system records and operational metrics."
    >
      <div className="flex justify-between gap-4 mb-4">
        <Tabs variant="padded" tabs={tabs} className="w-full" />

        <Input
          leftIcon={<IconSearch className="text-gray/70" />}
          placeholder="Search..."
          className="w-full max-w-96"
        />
      </div>

      <div className="flex items-start justify-start gap-3 w-full">
        <DataGrid
          isDraggable
          isScrollable
          title="Departments"
          tableHeaders={departmentsTableHeaders}
          rowData={departments}
          renderRow={renderDepartmentRow}
          customTitleElements={<Button variant="secondary" label="New" />}
          activeRowIndex={departments?.findIndex(
            role => role?.id === selectedDepartment?.id
          )}
          onRowClick={row => setSelectedDepartment(row)}
          withSearch
          threeDotContextMenuOptions={[
            {
              label: 'Edit',
              icon: <IconEditable className="size-5 text-gray/70" />,
              onClick: () => {},
            },
            {
              label: 'Delete',
              icon: <IconTrash className="size-5 text-gray/70" />,
              onClick: () => {},
            },
          ]}
          className="flex-1"
          classNames={{
            searchAndFilters: '[&>.cleen]:w-full',
            searchInput: 'w-full',
            row: 'hover:bg-primary/10 cursor-pointer',
            activeRow: 'bg-primary/10',
          }}
        />

        <DataGrid
          isDraggable
          isScrollable
          title="Teams"
          tableHeaders={teamsTableHeaders}
          rowData={selectedDepartment?.teams || []}
          renderRow={renderTeamRow}
          customTitleElements={<Button variant="secondary" label="New" />}
          activeRowIndex={selectedDepartment?.teams?.findIndex(
            team => team?.id === selectedTeam?.id
          )}
          onRowClick={row => setSelectedTeam(row)}
          withSearch
          threeDotContextMenuOptions={[
            {
              label: 'Edit',
              icon: <IconEditable className="size-5 text-gray/70" />,
              onClick: () => {},
            },
            {
              label: 'Delete',
              icon: <IconTrash className="size-5 text-gray/70" />,
              onClick: () => {},
            },
          ]}
          className={cn('flex-1', {
            'invisible': !selectedDepartment,
          })}
          classNames={{
            searchAndFilters: '[&>.cleen]:w-full',
            searchInput: 'w-full',
            row: 'hover:bg-primary/10 cursor-pointer',
            activeRow: 'bg-primary/10',
          }}
        />

        <DataGrid
          isDraggable
          isScrollable
          title="Employees"
          tableHeaders={employeesTableHeaders}
          rowData={selectedTeam?.employees || []}
          customTitleElements={<Button variant="secondary" label="New" />}
          withSearch
          threeDotContextMenuOptions={[
            {
              label: 'Edit',
              icon: <IconEditable className="size-5 text-gray/70" />,
              onClick: () => {},
            },
            {
              label: 'Delete',
              icon: <IconTrash className="size-5 text-gray/70" />,
              onClick: () => {},
            },
          ]}
          className={cn('flex-1', {
            'invisible': !selectedTeam,
          })}
          classNames={{
            searchAndFilters: '[&>.cleen]:w-full',
            searchInput: 'w-full',
            row: 'hover:bg-primary/10 cursor-pointer',
            activeRow: 'bg-primary/10',
          }}
        />
      </div>
    </MainLayout>
  );
};
```
