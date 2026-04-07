# Page Templates & Layout Patterns

Instead of building raw unstyled dashboards, **always use these page templates** as the foundation for main application screens. These templates ensure consistent navigation, correct layout spacing, and proper usage of library components.

## Global Layout Rules

1. **Always use a `MainLayout` component** that wraps the page content.
2. **The `MainLayout` must always include a `Sidebar`** on the left side of the main content.
3. **The `Sidebar` must handle basic shell requirements:**
   - Displaying the brand logo.
   - Showing user identity and handling the logout sequence.
   - Presenting a theme toggle (Dark Mode / Light Mode) inside a settings or bottom drawer context if applicable.
4. **Child pages** should focus strictly on local content (Breadcrumbs, Page Title, Tabs, Actions, and the Content itself).
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
  pageActions?: React.ReactNode;
};

// Example shell wrapper
export function MainLayout({ children, breadcrumbs, title, subtitle, pageActions }: MainLayoutProps) {
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
          {(breadcrumbs || title || subtitle || pageActions) && (
            <div className="h-fit flex w-full flex-col gap-5">
              {breadcrumbs && <Breadcrumb segments={breadcrumbs} />}

              {(title || subtitle || pageActions) && (
                <div className="flex w-full items-start justify-between gap-4">
                  <div className="flex flex-col items-start justify-start">
                    {title && <p className="text-nowrap font-bold text-gray">{title}</p>}
                    {subtitle && <p className="text-sm text-gray/70">{subtitle}</p>}
                  </div>
                  
                  {/* Optional actions rendered to the right of the title */}
                  {pageActions && <div>{pageActions}</div>}
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
