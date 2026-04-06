# Page Templates & Layout Patterns

Instead of building raw unstyled dashboards, **always use these page templates** as the foundation for main application screens. These templates ensure consistent navigation, correct layout spacing, and proper usage of library components.

## Global Layout Rules

1. **Always use a `MainLayout` component** that wraps the page content.
2. **The `MainLayout` must always include a `Sidebar`** on the left side of the main content.
3. **The `Sidebar` must handle basic shell requirements:**
   - Displaying the brand logo.
   - Showing user identity and handling the logout sequence.
   - Presenting a theme toggle (Dark Mode / Light Mode) inside a settings or bottom drawer context if applicable.
4. **Child pages** (like the `DataGridPage` below) should focus strictly on local content (Breadcrumbs, Page Title, Tabs, Actions, and the Content itself).
5. **No `cleen-` prefixes in app code:** When adapting these snippets, ensure you use the consumer project's standard Tailwind utilities (e.g., `flex`, `p-6`) instead of library-internal `cleen-` prefixed classes. Note: `cleen-no-scrollbar` is a custom CSS class that must be either imported without changes or globally defined in consumer's project.
6. Snippets contain classes with custom tailwind colours. Please don't forget to create CSS variables that inherit the colours from CleenUI variables by default.

---

## 1. The `MainLayout` Shell

Create this reusable shell once per project. It sets up the `Sidebar` and the main `<main>` scrolling canvas.

```tsx
import { 
  Sidebar, 
  Button, 
  Breadcrumb, 
  IconLogOut2, 
  IconUser, 
  Tooltip,
  DrawerContainer,
  DrawerContentTitle,
  type BreadcrumbProps 
} from '@cleen/ui';
import { useState } from 'react';

type MainLayoutProps = {
  children: React.ReactNode;
  breadcrumbs?: BreadcrumbProps['segments'];
  title?: string;
  subtitle?: string;
};

// Example shell wrapper
export function MainLayout({ children, breadcrumbs, title, subtitle }: MainLayoutProps) {
  const [activeMenu, setActiveMenu] = useState<string | null>('dashboard');
  const [isDark, setIsDark] = useState(false);

  // You can adapt this based on project requirements (use react-router, etc.)
  const mainNavigation = [
    { id: 'dashboard', label: 'Dashboard', iconName: 'HouseLine' },
    { id: 'data', label: 'Data Grid', iconName: 'Table' },
  ];

  const bottomNavigation = [
    { id: 'settings', label: 'Settings', iconName: 'Settings' },
  ];

  const drawerContent = {
    settings: (
      <DrawerContainer title="Theme Settings">
        <div className="flex flex-col gap-2">
          <DrawerContentTitle
            title={isDark ? 'Light mode' : 'Dark mode'}
            iconName={isDark ? 'Sun' : 'MoonCircle'}
            onClick={() => setIsDark(!isDark)}
          />
        </div>
      </DrawerContainer>
    ),
  };

  const drawerFooter = (
    <div className="flex justify-between items-start gap-4 w-full">
      <div className="flex flex-col w-[150px]">
        <span className="overflow-hidden whitespace-nowrap text-ellipsis w-full font-semibold text-sm text-gray">Jane Doe</span>

        <span className="overflow-hidden whitespace-nowrap text-ellipsis w-full font-normal text-sm text-gray/60">jane@example.com</span>
      </div>

      <Button
        variant="secondary"
        label="Logout"
        leftIcon={<IconLogOut2 className="mr-1" />}
        classNames={{ container: 'h-10' }}
        onClick={() => console.log('Handle logout')}
      />
    </div>
  );

  return (
    <div className="flex h-screen overflow-hidden">
      {/* 1. Global Left Navigation */}
      <Sidebar
        navigationItems={mainNavigation}
        bottomNavigationItems={bottomNavigation}
        drawerContent={drawerContent}
        drawerFooter={drawerFooter}
        activeId={activeMenu}
        onActiveChange={setActiveMenu}
        logo={<div className="flex size-10 items-center justify-center rounded-lg bg-primary/10 font-bold text-primary">CC</div>}
        userAvatar={
          <Tooltip
            hasArrow
            placement="right"
            color="rgb(var(--color-black), 0.8)"
            label="Profile"
          >
            <div
              className="flex items-center justify-center size-10 border-2 border-gray/40 rounded-full cursor-pointer hover:border-primary"
              onClick={() => setActiveMenu('user')}
            >
              <IconUser className="w-5 h-5 text-gray/50" />
            </div>
          </Tooltip>
        }
      />

      {/* 2. Main Content Canvas */}
      {/* Note: `cleen-no-scrollbar` is a custom utility. Ensure it is defined in your global CSS. */}
      <div className="relative flex h-full flex-1 flex-col overflow-x-hidden overflow-y-auto bg-background cleen-no-scrollbar">
        {/* Max-width constraint with responsive padding */}
        <main className="mx-auto flex h-full w-full max-w-[1500px] flex-col gap-8 p-4 pt-0 sm:pt-[unset] md:p-6 2xl:p-10">
          
          {/* Header Section: Breadcrumbs + Titles (Rendered if provided) */}
          {(breadcrumbs || title || subtitle) && (
            <div className="h-fit flex w-full flex-col gap-5">
              {breadcrumbs && <Breadcrumb segments={breadcrumbs} />}

              {title && (
                <div className="flex w-full flex-col items-start justify-start">
                  <p className="w-full text-nowrap font-bold text-gray">{title}</p>
                  {subtitle && <p className="text-sm text-gray/70">{subtitle}</p>}
                </div>
              )}
            </div>
          )}

          <div className="h-fit pb-12">
            {children}
          </div>
        </main>
      </div>
    </div>
  );
}
```

---

## 2. The `DataGrid` Page Template

This is the standard layout for list/table screens. It features Breadcrumbs, Page Header, optional Tabs (for mode switching), an Action Button (Add Entry), and the `DataGrid` (or `DataGridWithFilters`).

```tsx
import { useState } from 'react';
import { Tabs, Button } from '@cleen/ui';
import { DataGrid } from '@cleen/ui-pro';
import { MainLayout } from '@/components/MainLayout'; // import your shell
import { mockData, tableHeaders } from '@/mock/data';

export function DataGridPage() {
  const [activeTab, setActiveTab] = useState(0);
  const [searchText, setSearchText] = useState('');
  const [page, setPage] = useState(1);
  
  const pageTabs = [
    { id: 'all-entries', label: 'All Entries' },
    { id: 'configure', label: 'Configure' },
  ];

  const dataGridTabs = [
    { id: 'all', label: 'All' },
    { id: 'in-review', label: 'In Review' },
    { id: 'approved', label: 'Approved' },
    { id: 'archived', label: 'Archived' },
  ]

  return (
    <MainLayout
      breadcrumbs={[{ label: 'Home', path: '/' }, { label: 'Data', path: '/data' }]}
      title="Data Management"
      subtitle="View and manage system records and operational metrics."
    >
      {/* 1. Controls Section: Tabs + Primary Action */}
      <div className="flex justify-between gap-4 mb-4">
        <Tabs 
          variant="padded" 
          tabs={pageTabs} 
          className="w-full"
          currentTabIndex={activeTab} 
          onTabChange={setActiveTab} 
        />
          
        <Button variant="secondary">
          Add New Entry
        </Button>
      </div>

      {/* 2. Main Data View */}
      <DataGrid
        tableHeaders={tableHeaders}
        rowData={mockData}
        activePage={page}
        onPageChange={setPage}
        withSearch
        searchInputValue={searchText}
        onSearchChange={setSearchText}
        isSearchDebounced
        withRefreshButton
        onRefreshButtonClick={() => console.log('Refreshing...')}
        customFilterElements={
          <Tabs
            variant="padded"
            tabs={dataGridTabs}
            classNames={{
              list: '!cleen-mb-0',
            }}
          />
        }
      />      
    </MainLayout>
  );
}
```