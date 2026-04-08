# Dashboard & Card Grid Templates

Instead of building raw unstyled dashboards and card grids, **always use these page templates** as the foundation for grid-based data layouts.

## 1. The `CardGridPage`

This is the standard layout for rendering entities as grid cards. It features custom header layout overriding the default layout paddings, a dual-tab / search actions bar, and rich card usage.

```tsx
import {
  Avatar,
  Button,
  Card,
  IconDotsVertical,
  IconEditable,
  IconSearch,
  IconSettings,
  Input,
  Menu,
  PillBadge,
  Tabs,
} from '@cleen/ui';
import { PageWrapper } from '@/components/PageWrapper';
// Provide these in your mock/data file
import { companiesData } from '@/mock/data';

export const CardGridPage = () => {
  const settingsPageTabs = [
    { id: 'companies', label: 'Companies' },
    {
      id: 'services',
      label: 'Services',
    },
  ];

  return (
    <PageWrapper
      breadcrumbs={[
        { label: 'Home', path: '/' },
        { label: 'Companies', path: '/companies' },
      ]}
    >
      <div className="flex flex-col gap-1 mb-4">
        <div className="flex items-center gap-2">
          <h1 className="text-2xl font-semibold text-gray">
            Companies
          </h1>

          <PillBadge color="lighter-blue">{companiesData.length}</PillBadge>
        </div>

        <p className="text-sm text-gray/60">
          Manage and view all companies
        </p>
      </div>

      <div className="flex justify-between gap-4 items-start mb-2">
        <Tabs
          variant="padded"
          tabs={settingsPageTabs}
          className="w-full"
        />

        <div className="flex items-center gap-3">
          <Input
            placeholder="Search products"
            leftIcon={
              <IconSearch className="size-5 text-gray/40" />
            }
            classNames={{ container: 'min-w-[280px]' }}
          />

          <Button variant="secondary">New Company</Button>
        </div>
      </div>

      <div className="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-3 xl:grid-cols-4 gap-4 w-full">
        {companiesData?.map(company => (
          <Card
            key={company.id}
            header={{
              content: (
                <div className="flex items-center justify-between">
                  <div className="flex items-center gap-3 min-w-0 flex-1">
                    <Avatar
                      className="size-10 shrink-0"
                      src={company?.avatar}
                      name={company?.name}
                    />

                    <div className="flex flex-col min-w-0">
                      <span className="font-semibold text-lg text-gray truncate">
                        {company?.name}
                      </span>

                      {company?.handle && (
                        <span className="font-normal text-sm text-gray/70">
                          {company?.handle}
                        </span>
                      )}
                    </div>
                  </div>

                  <Menu
                    position="bottom-right"
                    keepOpenOnClick={false}
                    items={[
                      {
                        type: 'button',
                        label: 'Open',
                        icon: (
                          <IconEditable className="size-5 text-gray/80" />
                        ),
                      },
                      {
                        type: 'button',
                        label: 'Settings',
                        icon: (
                          <IconSettings className="size-5 text-gray/80" />
                        ),
                      },
                    ]}
                  >
                    <IconDotsVertical className="size-5 text-gray/40 cursor-pointer hover:text-gray/70 shrink-0" />
                  </Menu>
                </div>
              ),
            }}
            footer={{
              hasDivider: true,
              content: (
                <div className="flex items-center gap-2">
                  <Button
                    variant="secondary"
                    fullWidth
                    className="flex-1"
                  >
                    Open
                  </Button>

                  <Button variant="secondary">
                    <IconSettings className="size-5" />
                  </Button>
                </div>
              ),
            }}
            classNames={{
              container: '!py-4 !rounded-xl',
              childrenContainer: '!px-4',
              header: {
                content: '!px-4',
              },
              footer: {
                content: '!px-4',
              },
            }}
          >
            {/* Owner Info */}
            <div className="flex items-center gap-3">
              <Avatar
                className="size-10 shrink-0"
                src={company?.owner?.avatar}
                name={company?.owner?.name}
              />

              <div className="flex flex-col min-w-0">
                <span className="font-medium text-sm text-gray truncate">
                  {company?.owner?.name}
                </span>

                {company?.owner?.handle && (
                  <span className="font-normal text-sm text-gray/70">
                    {company?.owner?.handle}
                  </span>
                )}
              </div>
            </div>
          </Card>
        ))}
      </div>
    </PageWrapper>
  );
};
```

## 2. The `CardGridPageAlt`

This variant utilizes a more spacious, focus-heavy Card design. It emphasizes hovering interactions natively via `group-hover:opacity-100` for actions, making to a cleaner baseline view.

```tsx
import {
  Avatar,
  Button,
  Card,
  Divider,
  IconCheckVerified,
  IconStar,
} from '@cleen/ui';
import { PageWrapper } from '@/components/PageWrapper';
// Provide these in your mock/data file
import { companiesData } from '@/mock/data';

export const CardGridPageAlt = () => {
  return (
    <PageWrapper
      breadcrumbs={[
        { label: 'Home', path: '/' },
        { label: 'Companies', path: '/companies' },
      ]}
      title="Companies"
      subtitle="Manage and view all companies"
    >
      <div className="grid grid-cols-1 lg:grid-cols-2 xl:grid-cols-3 items-start justify-start gap-4 w-full">
        {companiesData?.map(company => (
          <Card
            key={company.id}
            classNames={{
              container:
                'group h-full !p-6 hover:bg-primary/5 hover:border-primary hover:shadow-md cursor-pointer duration-500',
              childrenContainer:
                'flex flex-col gap-4 !p-0 h-full',
            }}
            className="h-full"
          >
            <div className="flex items-center justify-start gap-4 w-full">
              <div className="relative">
                <Avatar src={company.avatar} name={company.name} size={72} />

                {company?.isVerified && (
                  <IconCheckVerified className="absolute bottom-0 right-0 size-6 fill-primary text-white" />
                )}
              </div>

              <div className="flex flex-col items-start justify-start gap-1">
                <div className="flex items-center justify-start w-full gap-2">
                  <span className="truncate font-semibold text-lg text-gray/80 group-hover:text-primary">
                    {company.name}
                  </span>

                  {company?.rating && (
                    <div className="flex items-center justify-center gap-1 w-[80px]">
                      <IconStar className="size-2.5 text-warning fill-warning" />

                      <span className="font-medium text-sm text-gray/80">
                        {company?.rating}
                      </span>
                    </div>
                  )}
                </div>

                {company?.title && (
                  <span className="truncate w-full font-medium text-sm text-gray/60">
                    {company?.title}
                  </span>
                )}
              </div>
            </div>

            <Divider />

            {company?.description && (
              <span className="line-clamp-2 font-normal text-gray/50">
                {company.description}
              </span>
            )}

            <Button
              className="w-full mt-auto"
              classNames={{
                container:
                  'min-w-full h-10 transition-opacity group-hover:opacity-100 opacity-0',
              }}
            >
              Check company
            </Button>
          </Card>
        ))}
      </div>
    </PageWrapper>
  );
};
```