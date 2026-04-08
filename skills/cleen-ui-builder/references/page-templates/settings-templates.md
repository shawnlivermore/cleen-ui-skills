# Settings Page Templates

Instead of building raw unstyled settings forms, **always use these page templates** as the foundation for horizontal and vertical settings pages.

## 1. The `SettingsPage` (Horizontal Tabs)

This is the standard layout for settings screens utilizing horizontal Tabs to separate distinct configuration categories. It demonstrates proper usage of `useForm`, `FormGroup`, and alignment of save/cancel actions.

```tsx
import {
  Button,
  Divider,
  FormGroup,
  IconMail,
  Input,
  Select,
  Tabs,
} from '@cleen/ui';
import { useForm } from '@cleen/ui-core';
import { PageWrapper } from '@/components/PageWrapper';
// Provide mock data or API integrations
import { countryOptions } from '@/mock/data';

const defaultValue = {
  firstName: 'Jane',
  lastName: 'Doe',
  handle: 'janedoe',
  email: 'janedoe@example.com',
  country: 'ua',
  website: 'https://janedoe.com',
  createdAt: '2026-04-06T12:00:00Z',
};

export const ProfileTab = () => {
  const { reset, isDirty, form, setField } = useForm({
    defaultValue,
  });

  return (
    <div className="flex flex-col gap-5 w-full h-full">
      <div className="flex flex-wrap items-center justify-between gap-3 w-full">
        <div className="flex flex-col items-start justify-start min-w-max">
          <span className="font-semibold text-lg text-gray">
            Profile Settings
          </span>

          <span className="font-normal text-sm text-gray/70">
            Adjust profile information
          </span>
        </div>

        <div className="flex items-center justify-end gap-3 w-max">
          <Button variant="secondary" onClick={reset} disabled={!isDirty}>
            Cancel
          </Button>

          <Button
            // Logic to save changes for API can go here
            onClick={() => {}}
            disabled={!isDirty}
          >
            Save
          </Button>
        </div>
      </div>

      <Divider />

      <form className="flex flex-col gap-5 w-full">
        <FormGroup title="Name" required>
          <div className="flex gap-4 w-full">
            <Input
              placeholder="First Name"
              value={form?.['firstName']}
              onChange={e => {
                setField('firstName', e.target.value);
              }}
              className="flex-1"
            />

            <Input
              placeholder="Last Name"
              value={form?.['lastName']}
              onChange={e => {
                setField('lastName', e.target.value);
              }}
              className="flex-1"
            />
          </div>
        </FormGroup>

        <Divider />

        <FormGroup title="Handle" required>
          <Input
            type="text"
            leftIcon={<span className="-mr-2 text-gray/30">@</span>}
            value={form?.['handle']}
            onChange={e => {
              setField('handle', e.target.value);
            }}
          />
        </FormGroup>

        <Divider />

        <FormGroup title="Email Address" required>
          <Input
            type="email"
            leftIcon={
              <IconMail className="w-5 h-5 text-gray/30" />
            }
            value={form?.['email']}
            onChange={e => {
              setField('email', e.target.value);
            }}
          />
        </FormGroup>

        <Divider />

        <FormGroup title="Country">
          <Select
            options={countryOptions}
            value={countryOptions?.find(
              country => country?.value === form?.['country']
            )}
            onChange={option => {
              setField(
                'country',
                (option as (typeof countryOptions)[0])?.value
              );
            }}
            className="w-full"
          />
        </FormGroup>

        <Divider />

        <FormGroup title="Featured Website">
          <Input
            value={form?.['website']}
            onChange={e => {
              setField('website', e.target.value);
            }}
            className="w-full"
          />
        </FormGroup>

        <Divider />

        <FormGroup title="Created On">
          <span className="font-normal text-sm text-gray">
            {new Date(form?.['createdAt'] || '').toLocaleDateString('en-US', {
              year: 'numeric',
              month: 'long',
              day: 'numeric',
              hour: '2-digit',
              minute: '2-digit',
            })}
          </span>
        </FormGroup>
      </form>
    </div>
  );
};

export const SettingsPage = () => {
  const settingsPageTabs = [
    { id: 'profile', label: 'Profile', content: <ProfileTab /> },
    { id: 'account', label: 'Account', content: 'Account Settings Content' },
    {
      id: 'notifications',
      label: 'Notifications',
      content: 'Notifications Settings Content',
    },
    { id: 'privacy', label: 'Privacy', content: 'Privacy Settings Content' },
  ];

  return (
    <PageWrapper title="Jane Doe" subtitle="@janedoe">
      <Tabs tabs={settingsPageTabs} className="w-full" />
    </PageWrapper>
  );
};
```

## 2. The `SettingsPageAlt` (Vertical Tabs)

This variant utilizes a vertical tab layout to organize settings categories on the left side, optimizing vertical space for dense forms.

```tsx
import { Divider, FormGroup, IconMail, Input, Select, Tabs } from '@cleen/ui';
import { useForm } from '@cleen/ui-core';
import { PageWrapper } from '@/components/PageWrapper';
// Provide mock data or API integrations
import { countryOptions, horizontalTabs } from '@/mock/data';

const defaultValue = {
  firstName: 'Jane',
  lastName: 'Doe',
  handle: 'janedoe',
  email: 'janedoe@example.com',
  country: 'ua',
  website: 'https://janedoe.com',
  createdAt: '2026-04-06T12:00:00Z',
};

export const ProfileTab = () => {
  const { form, setField } = useForm({
    defaultValue,
  });

  return (
    <div className="flex flex-col gap-5 w-full">
      <Tabs variant="stretched" tabs={horizontalTabs} className="w-full" />

      <form className="flex flex-col gap-5 w-full">
        <FormGroup title="Name" required>
          <div className="flex gap-4 w-full">
            <Input
              placeholder="First Name"
              value={form?.['firstName']}
              onChange={e => {
                setField('firstName', e.target.value);
              }}
              className="flex-1"
            />

            <Input
              placeholder="Last Name"
              value={form?.['lastName']}
              onChange={e => {
                setField('lastName', e.target.value);
              }}
              className="flex-1"
            />
          </div>
        </FormGroup>

        <Divider />

        <FormGroup title="Handle" required>
          <Input
            type="text"
            leftIcon={<span className="-mr-2 text-gray/30">@</span>}
            value={form?.['handle']}
            onChange={e => {
              setField('handle', e.target.value);
            }}
          />
        </FormGroup>

        <Divider />

        <FormGroup title="Email Address" required>
          <Input
            type="email"
            leftIcon={
              <IconMail className="w-5 h-5 text-gray/30" />
            }
            value={form?.['email']}
            onChange={e => {
              setField('email', e.target.value);
            }}
          />
        </FormGroup>

        <Divider />

        <FormGroup title="Country">
          <Select
            options={countryOptions}
            value={countryOptions?.find(
              country => country?.value === form?.['country']
            )}
            onChange={option => {
              setField(
                'country',
                (option as (typeof countryOptions)[0])?.value
              );
            }}
            className="w-full"
          />
        </FormGroup>

        <Divider />

        <FormGroup title="Featured Website">
          <Input
            value={form?.['website']}
            onChange={e => {
              setField('website', e.target.value);
            }}
            className="w-full"
          />
        </FormGroup>

        <Divider />

        <FormGroup title="Created On">
          <span className="font-normal text-sm text-gray">
            {new Date(form?.['createdAt'] || '').toLocaleDateString('en-US', {
              year: 'numeric',
              month: 'long',
              day: 'numeric',
              hour: '2-digit',
              minute: '2-digit',
            })}
          </span>
        </FormGroup>
      </form>
    </div>
  );
};

export const SettingsPageAlt = () => {
  const settingsPageTabs = [
    { id: 'profile', label: 'Profile', content: <ProfileTab /> },
    { id: 'account', label: 'Account', content: 'Account Settings Content' },
    {
      id: 'company',
      label: 'Company',
      content: 'Company Settings Content',
    },
  ];

  return (
    <PageWrapper title="Jane Doe" subtitle="@janedoe">
      <Tabs
        direction="vertical"
        tabs={settingsPageTabs}
        classNames={{
          list: 'w-[200px]',
        }}
      />
    </PageWrapper>
  );
};
```

## 3. The `DataGridSettingsPage`

This pattern merges the concept of a settings page with an interactable `DataGridWithFilters`. Upon selecting a row from the vertical Data Grid, the user can modify and save properties of the item in the right pane form.

```tsx
import { useMemo, useState } from 'react';
import { Button, FormGroup, IconEditable, IconMail, IconTrash, Input, Select, Tabs } from '@cleen/ui';
import { cn, useForm } from '@cleen/ui-core';
import { DataGridWithFilters } from '@cleen/ui-pro';
import { PageWrapper } from '@/components/PageWrapper';
// Provide mock data or API integrations
import { Department, departmentOptions, employees, stretchedTabs, verticalTabs, type Employee } from '@/mock/data';
import { employeesTableHeaders } from '@/mock/data';

export const UsersTab = () => {
  const [selectedVerticalTabIndex, setActiveUsageTypeIndex] = useState<number>(0);
  const [selectedEmployee, setSelectedEmployee] = useState<Employee | null>(null);

  const { reset, isDirty, form, setField, setForm } = useForm({
    defaultValue: selectedEmployee,
  });

  const filteredEmployees = useMemo(
    () =>
      selectedVerticalTabIndex
        ? employees.filter(
            employee =>
              employee.department === verticalTabs[selectedVerticalTabIndex].id
          )
        : employees,
    [selectedVerticalTabIndex]
  );

  return (
    <div className="flex items-start justify-start gap-5">
      <Tabs
        tabs={verticalTabs}
        direction="vertical"
        tabIndex={selectedVerticalTabIndex}
        onTabChange={setActiveUsageTypeIndex}
        className="w-[200px]"
      />

      <div className="flex-1">
        <DataGridWithFilters
          isScrollable
          rowData={filteredEmployees}
          tableHeaders={employeesTableHeaders}
          withSearch
          classNames={{
            row: 'hover:bg-primary/10 cursor-pointer',
            activeRow: 'bg-primary/10',
            searchAndFilters: '[&>.cleen]:w-full',
            searchInput: 'w-full',
          }}
          activeRowIndex={employees?.findIndex(
            employee => employee.id === selectedEmployee?.id
          )}
          onRowClick={row => {
            setSelectedEmployee(row);
            setForm(row);
          }}
          threeDotContextMenuOptions={[
            {
              label: 'Edit',
              icon: (
                <IconEditable className="size-5 text-gray/70" />
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

      <div
        className={cn('flex flex-col gap-3 flex-[2]', {
          'invisible': !selectedEmployee,
        })}
      >
        <Tabs variant="stretched" tabs={stretchedTabs} />

        <div className="flex flex-col gap-5 w-full">
          <form className="flex flex-col gap-5 w-full">
            <FormGroup title="Name" required>
              <div className="flex gap-4 w-full">
                <Input
                  placeholder="First Name"
                  value={form?.['firstName']}
                  onChange={e => {
                    setField('firstName', e.target.value);
                  }}
                  className="flex-1"
                />

                <Input
                  placeholder="Last Name"
                  value={form?.['lastName']}
                  onChange={e => {
                    setField('lastName', e.target.value);
                  }}
                  className="flex-1"
                />
              </div>
            </FormGroup>

            <FormGroup title="Email Address" required>
              <Input
                type="email"
                leftIcon={
                  <IconMail className="w-5 h-5 text-gray/30" />
                }
                value={form?.['email']}
                onChange={e => {
                  setField('email', e.target.value);
                }}
              />
            </FormGroup>

            <FormGroup title="Department" required>
              <Select
                options={departmentOptions}
                value={departmentOptions.find(
                  option => option.value === form?.['department']
                )}
                onChange={value => {
                  setField(
                    'department',
                    (value as { value: Department }).value
                  );
                }}
              />
            </FormGroup>
          </form>

          <div className="flex items-center justify-between w-full">
            <Button variant="secondary" onClick={reset}>
              Cancel
            </Button>

            <Button disabled={!isDirty}>Save</Button>
          </div>
        </div>
      </div>
    </div>
  );
};

export const DataGridSettingsPage = () => {
  const settingsPageTabs = [
    { id: 'users', label: 'Users', content: <UsersTab /> },
    {
      id: 'departments',
      label: 'Departments',
      content: 'Department Settings Content',
    },
  ];

  return (
    <PageWrapper
      breadcrumbs={[
        { label: 'Home', path: '/' },
        { label: 'HR', path: '/human-resources' },
      ]}
      title="User Management"
    >
      <Tabs tabs={settingsPageTabs} className="w-full" />
    </PageWrapper>
  );
};
```