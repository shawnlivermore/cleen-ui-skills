# Overlay Patterns

Reusable, copy-paste-ready patterns for common overlay scenarios. These combine multiple components and hooks into complete, working flows.

---

## Table of Contents

1. [Confirmation Modal](#confirmation-modal)
2. [Edit Drawer with Form](#edit-drawer-with-form)
3. [Filter Drawer wired to DataGrid](#filter-drawer-wired-to-datagrid)
4. [Notify-on-submit (toast after form save)](#notify-on-submit)
5. [Kebab / Context Menu on table rows](#kebab--context-menu-on-table-rows)
6. [Info Popover on a custom trigger](#info-popover-on-a-custom-trigger)
7. [Dropdown panel (inline search)](#dropdown-panel-inline-search)
8. [Collapsible Settings Sections](#collapsible-settings-sections)
9. [Stacked Overlays (modal over drawer)](#stacked-overlays-modal-over-drawer)

---

## Confirmation Modal

Classic destructive-action confirmation. Blocks full interaction until dismissed.

```tsx
import { Modal, Button, useDisclosure } from '@cleen/cleen-components';

function DeleteOperatorButton({
  operatorName,
  onConfirm,
}: {
  operatorName: string;
  onConfirm: () => Promise<void>;
}) {
  const { isOpen, open, close } = useDisclosure();
  const [isDeleting, setIsDeleting] = useState(false);

  const handleConfirm = async () => {
    setIsDeleting(true);
    await onConfirm();
    setIsDeleting(false);
    close();
  };

  return (
    <>
      <Button variant="danger" label="Delete operator" onClick={open} />

      <Modal
        isOpen={isOpen}
        onClose={close}
        size="sm"
        closeOnOverlayClick={!isDeleting}
        header={{ title: 'Delete operator', hasDivider: true }}
        footer={{
          hasDivider: true,
          content: (
            <div className="cleen-flex cleen-justify-end cleen-gap-2">
              <Button
                variant="secondary"
                label="Cancel"
                onClick={close}
                disabled={isDeleting}
              />
              <Button
                variant="danger"
                label="Delete"
                onClick={handleConfirm}
                isLoading={isDeleting}
              />
            </div>
          ),
        }}
      >
        <p className="cleen-text-sm cleen-text-gray-600">
          Are you sure you want to delete <strong>{operatorName}</strong>? This
          action cannot be undone.
        </p>
      </Modal>
    </>
  );
}
```

**Pattern notes:**

- Disable `closeOnOverlayClick` while the async action is in progress (prevents accidental dismissal mid-request).
- `isLoading` on the confirm button gives feedback during the operation.
- Keep modal `size="sm"` — confirmation dialogs don't need much space.

---

## Edit Drawer with Form

Full edit panel with form state, validation, and save/cancel footer. Common for editing any record from a table row click.

```tsx
import {
  Drawer,
  Button,
  FormGroup,
  Input,
  Select,
  useDisclosure,
  useForm,
  useValidation,
  showNotification,
} from '@cleen/cleen-components';

interface Wolf {
  id: number;
  name: string;
  rank: string;
  territory: string;
}

interface WolfForm {
  name: string;
  rank: string;
  territory: string;
}

const rankOptions = [
  { value: 'alpha', label: 'Alpha' },
  { value: 'beta', label: 'Beta' },
  { value: 'omega', label: 'Omega' },
];

function EditWolfDrawer({
  wolf,
  onSave,
}: {
  wolf: Wolf;
  onSave: (data: WolfForm) => Promise<void>;
}) {
  const { isOpen, open, close } = useDisclosure();
  const { form, setField, isDirty, reset } = useForm<WolfForm>({
    defaultValue: {
      name: wolf.name,
      rank: wolf.rank,
      territory: wolf.territory,
    },
  });
  const { errors, setError, clearError, clearErrors } = useValidation<WolfForm>(
    {}
  );
  const [isSaving, setIsSaving] = useState(false);

  const validate = (): boolean => {
    clearErrors();
    if (!form.name.trim()) setError('name', 'Name is required');
    if (!form.rank) setError('rank', 'Rank is required');
    return !errors.name && !errors.rank;
  };

  const handleSave = async () => {
    if (!validate()) return;
    setIsSaving(true);
    try {
      await onSave(form);
      showNotification({ message: 'Wolf profile updated', variant: 'success' });
      close();
    } catch {
      showNotification({ message: 'Failed to save changes', variant: 'error' });
    } finally {
      setIsSaving(false);
    }
  };

  const handleClose = () => {
    reset();
    clearErrors();
    close();
  };

  return (
    <>
      <Button label="Edit" variant="secondary" onClick={open} />

      <Drawer
        isOpen={isOpen}
        onClose={handleClose}
        title="Edit wolf profile"
        subtitle="Update rank and territory assignment"
        size="md"
        footer={
          <div className="cleen-flex cleen-justify-end cleen-gap-2">
            <Button variant="secondary" label="Cancel" onClick={handleClose} />
            <Button
              variant="primary"
              label="Save changes"
              onClick={handleSave}
              isLoading={isSaving}
              disabled={!isDirty}
            />
          </div>
        }
      >
        <FormGroup title="Name" required>
          <Input
            value={form.name}
            onChange={e => {
              setField('name', e.target.value);
              clearError('name');
            }}
            infoLabels={{ errorMessage: errors.name }}
          />
        </FormGroup>

        <FormGroup title="Rank" required>
          <Select
            options={rankOptions}
            value={rankOptions.find(o => o.value === form.rank) ?? null}
            onChange={opt => {
              setField('rank', opt?.value ?? '');
              clearError('rank');
            }}
            infoLabels={{ errorMessage: errors.rank }}
          />
        </FormGroup>

        <FormGroup title="Territory">
          <Input
            value={form.territory}
            onChange={e => setField('territory', e.target.value)}
          />
        </FormGroup>
      </Drawer>
    </>
  );
}
```

**Pattern notes:**

- `reset()` + `clearErrors()` on close keeps the drawer clean for re-open.
- `disabled={!isDirty}` on Save prevents accidental no-op saves.
- Always `showNotification` after save/error — don't leave the user without feedback.

---

## Filter Drawer wired to DataGrid

Full filter flow with applied state, saved filters, and DataGrid. This is the standard pattern for filterable tables.

```tsx
import {
  DataGridWithFilters,
  FilterDrawer,
  FormGroup,
  Select,
  DatePicker,
  useDisclosure,
} from '@cleen/cleen-components';
import type { TableHeaderProps } from '@cleen/cleen-components';

interface MissionFilter {
  status: string[];
  operativeId: string | null;
  dateRange: [Date, Date] | null;
}

const defaultFilter: MissionFilter = {
  status: [],
  operativeId: null,
  dateRange: null,
};

function MissionsPage() {
  const filterDrawer = useDisclosure();
  const [appliedFilter, setAppliedFilter] =
    useState<MissionFilter>(defaultFilter);
  const [pendingFilter, setPendingFilter] =
    useState<MissionFilter>(defaultFilter);
  const [savedFilters, setSavedFilters] = useState<SavedFilter[]>([]);

  const { data: missions, isLoading } = useFetchMissions(appliedFilter);

  const headers: TableHeaderProps<Mission>[] = [
    { field: 'code', headerElement: 'Code', sortable: true },
    { field: 'operative', headerElement: 'Operative' },
    { field: 'status', headerElement: 'Status', width: 120 },
    { field: 'date', headerElement: 'Date', width: 140 },
  ];

  const handleApply = () => {
    setAppliedFilter(pendingFilter);
    filterDrawer.close();
  };

  const handleClear = () => {
    setPendingFilter(defaultFilter);
  };

  const handleSaveFilter = async (newFilter: NewFilter) => {
    const saved = await api.saveFilter(newFilter);
    setSavedFilters(prev => [...prev, saved]);
  };

  return (
    <>
      <DataGridWithFilters
        title="Missions"
        tableHeaders={headers}
        rowData={missions ?? []}
        isLoading={isLoading}
        onFilterClick={filterDrawer.open}
        withSearch
        withFooter
      />

      <FilterDrawer
        isOpen={filterDrawer.isOpen}
        onClose={filterDrawer.close}
        onApply={handleApply}
        onClear={handleClear}
        onCancel={filterDrawer.close}
        savedFilters={savedFilters}
        onSave={handleSaveFilter}
      >
        <FormGroup title="Status">
          <Select
            isMulti
            options={[
              { value: 'active', label: 'Active' },
              { value: 'completed', label: 'Completed' },
              { value: 'aborted', label: 'Aborted' },
            ]}
            value={pendingFilter.status.map(s => ({ value: s, label: s }))}
            onChange={opts =>
              setPendingFilter(f => ({ ...f, status: opts.map(o => o.value) }))
            }
          />
        </FormGroup>

        <FormGroup title="Date range">
          <DatePicker
            isRange
            value={pendingFilter.dateRange}
            onChange={v => setPendingFilter(f => ({ ...f, dateRange: v }))}
          />
        </FormGroup>
      </FilterDrawer>
    </>
  );
}
```

**Pattern notes:**

- Keep `pendingFilter` separate from `appliedFilter` so "Cancel" discards uncommitted changes.
- `onClear` resets `pendingFilter` to default — don't call `setAppliedFilter` here (only on Apply).
- Use `DataGridWithFilters` (not plain `DataGrid`) — it has a built-in filter button that opens the drawer.

---

## Notify-on-submit

Standard form save + toast feedback. Works anywhere you have an async submit.

```tsx
import { showNotification } from '@cleen/cleen-components';

const handleSubmit = async () => {
  if (!validate()) return;

  setIsSubmitting(true);
  try {
    await api.updateProfile(form);
    showNotification({ message: 'Profile saved', variant: 'success' });
  } catch (error) {
    const message =
      error instanceof ApiError ? error.message : 'Something went wrong';
    showNotification({ message, variant: 'error', autoClose: false });
  } finally {
    setIsSubmitting(false);
  }
};
```

**Pattern notes:**

- Use `autoClose: false` on errors so the user can read the message without it disappearing.
- Never swallow `catch` blocks silently — always notify the user.

---

## Kebab / Context Menu on Table Rows

Row-level action menu (edit, view, delete). Wire to `onRowContextMenu` or a kebab column.

```tsx
import { DataGrid, Menu, Button } from '@cleen/cleen-components';
import { MdMoreVert, MdEdit, MdVisibility, MdDelete } from 'react-icons/md';

function AgentTable({ agents, onEdit, onView, onDelete }: AgentTableProps) {
  const headers = [
    { field: 'name', headerElement: 'Name', sortable: true },
    { field: 'status', headerElement: 'Status' },
    {
      field: 'actions',
      headerElement: '',
      width: 56,
      align: 'center' as const,
      cellRenderer: (row: Agent) => (
        <Menu
          position="bottom-right"
          items={[
            {
              type: 'button',
              label: 'View details',
              icon: <MdVisibility />,
              onClick: () => onView(row),
            },
            {
              type: 'button',
              label: 'Edit',
              icon: <MdEdit />,
              onClick: () => onEdit(row),
            },
            {
              type: 'button',
              label: 'Deactivate',
              danger: true,
              icon: <MdDelete />,
              onClick: () => onDelete(row),
            },
          ]}
        >
          <button className="cleen-p-1 cleen-rounded hover:cleen-bg-gray-100">
            <MdMoreVert size={18} />
          </button>
        </Menu>
      ),
    },
  ];

  return <DataGrid tableHeaders={headers} rowData={agents} withSearch />;
}
```

**Pattern notes:**

- Put `onClick` on the `Menu`'s `items` level, not the row — this gives individual action handlers.
- Use `position="bottom-right"` for table row menus — prevents the menu going off-screen on the right side.
- Group destructive items (deactivate, delete) with `danger: true` at the bottom of the items list.

---

## Info Popover on a Custom Trigger

Show rich content (not just text) on hover or click, attached to any element.

```tsx
import { Popover } from '@cleen/cleen-components';
import { MdInfoOutline } from 'react-icons/md';

function MetricCard({ label, value, detail }: MetricCardProps) {
  return (
    <div className="cleen-flex cleen-items-center cleen-gap-1">
      <span className="cleen-font-semibold">{label}</span>
      <Popover
        content={
          <div className="cleen-p-3 cleen-w-56 cleen-text-sm cleen-text-gray-600">
            {detail}
          </div>
        }
        position="top"
      >
        <button className="cleen-text-gray-400 hover:cleen-text-gray-600">
          <MdInfoOutline size={16} />
        </button>
      </Popover>
    </div>
  );
}
```

**Pattern notes:**

- `Popover` is click-based. For hover, use `Tooltip` instead.
- Give the popover panel a sensible `cleen-w-*` — unconstrained width looks broken.

---

## Dropdown Panel (Inline Search)

Custom dropdown with a search input and a results list. Use `Dropdown` since the content isn't predefined item rows.

```tsx
import { Dropdown, Input } from '@cleen/cleen-components';
import { useDebounce } from '@cleen/cleen-components';

function AssigneeDropdown({ onSelect }: { onSelect: (user: User) => void }) {
  const [query, setQuery] = useState('');
  const debouncedQuery = useDebounce(query, 300);
  const { data: users } = useSearchUsers(debouncedQuery);

  return (
    <Dropdown
      label="Assign to..."
      fullWidth={false}
      fullWidthDropdown
      keepOpenOnClickContent={false}
    >
      <div className="cleen-p-2 cleen-flex cleen-flex-col cleen-gap-1 cleen-w-64">
        <Input
          placeholder="Search users..."
          value={query}
          onChange={e => setQuery(e.target.value)}
        />
        <div className="cleen-max-h-48 cleen-overflow-y-auto">
          {users?.map(user => (
            <button
              key={user.id}
              className="cleen-w-full cleen-text-left cleen-px-3 cleen-py-2 cleen-rounded hover:cleen-bg-gray-100 cleen-text-sm"
              onClick={() => onSelect(user)}
            >
              {user.name}
            </button>
          ))}
        </div>
      </div>
    </Dropdown>
  );
}
```

**Pattern notes:**

- Set `keepOpenOnClickContent={false}` so clicking a result actually closes the dropdown.
- Use `useDebounce` from the library — don't install a separate package.
- Cap the results list height (`cleen-max-h-48`) and scroll — long lists overflow otherwise.

---

## Collapsible Settings Sections

Multi-section settings panel with expandable categories. Common for settings pages or admin configuration panels.

```tsx
import { Collapsible } from '@cleen/cleen-components';

function SettingsPanel() {
  return (
    <Collapsible
      defaultOpenIndex={0}
      sections={[
        {
          title: 'General',
          items: [
            {
              content: <SettingRow label="Display name" control={<Input />} />,
            },
            {
              content: (
                <SettingRow
                  label="Language"
                  control={<Select options={langs} />}
                />
              ),
            },
            {
              content: (
                <SettingRow
                  label="Timezone"
                  control={<Select options={timezones} />}
                />
              ),
            },
          ],
        },
        {
          title: 'Notifications',
          items: [
            {
              content: <SettingRow label="Email alerts" control={<Switch />} />,
            },
            {
              content: (
                <SettingRow label="In-app alerts" control={<Switch />} />
              ),
            },
          ],
        },
        {
          title: 'Danger zone',
          items: [
            {
              content: (
                <SettingRow
                  label="Delete account"
                  control={<Button variant="danger" label="Delete" />}
                />
              ),
            },
          ],
        },
      ]}
    />
  );
}
```

**Pattern notes:**

- `defaultOpenIndex={0}` opens the first section — good for "General" which is the most common starting point.
- Items are fully flexible — wrap your setting rows in whatever layout component fits.

---

## Stacked Overlays (Modal over Drawer)

A confirm dialog on top of an edit drawer — open the modal from inside the drawer.

```tsx
function EditAndDeleteDrawer({ record }: { record: Record }) {
  const drawer = useDisclosure();
  const confirmModal = useDisclosure();

  return (
    <>
      <Button label="Edit" onClick={drawer.open} />

      <Drawer
        isOpen={drawer.isOpen}
        onClose={drawer.close}
        title="Edit record"
        zIndex={100}
        footer={
          <div className="cleen-flex cleen-justify-between">
            <Button
              variant="danger"
              label="Delete"
              onClick={confirmModal.open}
            />
            <Button variant="primary" label="Save" onClick={handleSave} />
          </div>
        }
      >
        {/* form content */}
      </Drawer>

      <Modal
        isOpen={confirmModal.isOpen}
        onClose={confirmModal.close}
        zIndex={200} // higher than the drawer (¬_¬)
        size="sm"
        header={{ title: 'Confirm deletion' }}
        footer={{
          content: (
            <div className="cleen-flex cleen-justify-end cleen-gap-2">
              <Button
                variant="secondary"
                label="Cancel"
                onClick={confirmModal.close}
              />
              <Button variant="danger" label="Delete" onClick={handleDelete} />
            </div>
          ),
        }}
      >
        <p>This will permanently delete the record.</p>
      </Modal>
    </>
  );
}
```

**Pattern notes:**

- Increment `zIndex` for each layer (e.g. drawer at 100, modal at 200). Default `zIndex` for both is 50 — stacking without overrides won't work.
- Each overlay gets its own `useDisclosure` — they're independent.
- Closing the confirm modal should NOT close the drawer — keep them decoupled.
