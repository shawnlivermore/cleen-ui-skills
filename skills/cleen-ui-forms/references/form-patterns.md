# Form Patterns

Concrete, copy-paste-ready form implementations for common scenarios.

---

## Table of Contents

1. [Settings Form](#settings-form)
2. [Edit Modal Form](#edit-modal-form)
3. [Filter Panel (Drawer)](#filter-panel-drawer)
4. [Multi-Step Form (Wizard)](#multi-step-form-wizard)

---

## Settings Form

A vertical stack of `FormGroup` rows — the standard layout for settings pages or profile editors.

```tsx
import { Button, FormGroup, Input, Select, Switch, TextArea } from '@cleen/cleen-components';
import { useForm } from '@cleen/cleen-components';
import { useValidation } from '@cleen/cleen-components';

interface SettingsForm {
  displayName: string;
  email: string;
  bio: string;
  timezone: string;
  emailNotifications: boolean;
}

const TIMEZONE_OPTIONS = [
  { value: 'utc', label: 'UTC' },
  { value: 'us-east', label: 'US/Eastern' },
  { value: 'us-west', label: 'US/Pacific' },
  { value: 'eu-london', label: 'Europe/London' },
  { value: 'eu-paris', label: 'Europe/Paris' },
];

export function AccountSettings() {
  const { form, setField, isDirty, reset } = useForm<SettingsForm>({
    defaultValue: {
      displayName: 'Kallen Stadtfeld',
      email: 'kallen@clover.mil',
      bio: '',
      timezone: 'utc',
      emailNotifications: true,
    },
  });

  const { errors, setError, clearError, clearErrors } = useValidation<SettingsForm>({});

  const validate = () => {
    clearErrors();
    if (!form?.displayName?.trim()) setError('displayName', 'Display name is required');
    if (!form?.email?.includes('@')) setError('email', 'Enter a valid email address');
    // return true if no errors were set
    return !Object.keys(errors).length;
  };

  const handleSave = () => {
    if (!validate()) return;
    // call API here
  };

  return (
    <div className="cleen-flex cleen-flex-col cleen-gap-6">
      <FormGroup title="Display Name" required>
        <Input
          value={form?.displayName}
          onChange={e => {
            setField('displayName', e.target.value);
            clearError('displayName');
          }}
          infoLabels={{ errorMessage: errors.displayName }}
        />
      </FormGroup>

      <FormGroup title="Email Address" required tooltipDescription="Used for login and notifications">
        <Input
          type="email"
          value={form?.email}
          onChange={e => {
            setField('email', e.target.value);
            clearError('email');
          }}
          infoLabels={{ errorMessage: errors.email }}
        />
      </FormGroup>

      <FormGroup title="Bio" subtitle="A short description of yourself">
        <TextArea
          value={form?.bio}
          onChange={e => setField('bio', e.target.value)}
          maxLength={200}
          maxLengthLabel={remaining => `${remaining} characters remaining`}
        />
      </FormGroup>

      <FormGroup title="Timezone">
        <Select
          options={TIMEZONE_OPTIONS}
          value={TIMEZONE_OPTIONS.find(o => o.value === form?.timezone)}
          onChange={opt => setField('timezone', (opt as typeof TIMEZONE_OPTIONS[0]).value)}
        />
      </FormGroup>

      <FormGroup title="Email Notifications" subtitle="Receive updates about your account">
        <Switch
          checked={form?.emailNotifications}
          onChange={e => setField('emailNotifications', e.target.checked)}
        />
      </FormGroup>

      <div className="cleen-flex cleen-justify-end cleen-gap-2">
        <Button variant="secondary" label="Cancel" onClick={reset} disabled={!isDirty} />
        <Button variant="primary" label="Save Changes" onClick={handleSave} disabled={!isDirty} />
      </div>
    </div>
  );
}
```

---

## Edit Modal Form

A form rendered inside a `Modal` — the standard pattern for editing a single record without navigating away. The modal's `footer` prop is used for actions.

```tsx
import {
  Button, FormGroup, Input, Modal, Select,
  useForm, useValidation
} from '@cleen/cleen-components';
import { useState } from 'react';

interface UserForm {
  name: string;
  email: string;
  role: string;
}

const ROLE_OPTIONS = [
  { value: 'admin', label: 'Administrator' },
  { value: 'editor', label: 'Editor' },
  { value: 'viewer', label: 'Viewer' },
];

interface EditUserModalProps {
  user: UserForm;
  isOpen: boolean;
  onClose: () => void;
  onSave: (data: UserForm) => Promise<void>;
}

export function EditUserModal({ user, isOpen, onClose, onSave }: EditUserModalProps) {
  const [isSaving, setIsSaving] = useState(false);
  const { form, setField, isDirty } = useForm<UserForm>({ defaultValue: user });
  const { errors, setError, clearError, clearErrors } = useValidation<UserForm>({});

  const validate = () => {
    clearErrors();
    if (!form?.name?.trim()) setError('name', 'Name is required');
    if (!form?.email?.includes('@')) setError('email', 'Enter a valid email address');
    if (!form?.role) setError('role', 'Select a role');
    return !form?.name?.trim() === false && form?.email?.includes('@') && !!form?.role;
  };

  const handleSave = async () => {
    if (!validate() || !form) return;
    setIsSaving(true);
    await onSave(form);
    setIsSaving(false);
    onClose();
  };

  return (
    <Modal
      isOpen={isOpen}
      onClose={onClose}
      header="Edit User"
      footer={
        <div className="cleen-flex cleen-justify-end cleen-gap-2">
          <Button variant="secondary" label="Cancel" onClick={onClose} />
          <Button
            variant="primary"
            label="Save"
            onClick={handleSave}
            disabled={!isDirty}
            isLoading={isSaving}
          />
        </div>
      }
    >
      <div className="cleen-flex cleen-flex-col cleen-gap-4">
        <FormGroup title="Name" required>
          <Input
            value={form?.name}
            onChange={e => { setField('name', e.target.value); clearError('name'); }}
            infoLabels={{ errorMessage: errors.name }}
          />
        </FormGroup>

        <FormGroup title="Email" required>
          <Input
            type="email"
            value={form?.email}
            onChange={e => { setField('email', e.target.value); clearError('email'); }}
            infoLabels={{ errorMessage: errors.email }}
          />
        </FormGroup>

        <FormGroup title="Role" required>
          <Select
            options={ROLE_OPTIONS}
            value={ROLE_OPTIONS.find(o => o.value === form?.role) ?? null}
            onChange={opt => { setField('role', (opt as typeof ROLE_OPTIONS[0]).value); clearError('role'); }}
            infoLabels={{ errorMessage: errors.role }}
          />
        </FormGroup>
      </div>
    </Modal>
  );
}
```

---

## Filter Panel (Drawer)

A form rendered inside a `Drawer` for filtering a list or table. Typically stateless while open — values are only applied on explicit "Apply" action.

```tsx
import {
  Button, CheckboxGroup, DatePicker, Drawer,
  FormGroup, RangeSlider, Select
} from '@cleen/cleen-components';
import { useForm } from '@cleen/cleen-components';
import type { DateRange } from 'react-day-picker';

interface FilterForm {
  statuses: string[];
  assignee: string | null;
  dateRange: DateRange | undefined;
  budgetRange: [number, number];
}

const STATUS_OPTIONS = [
  { id: 'active', label: 'Active' },
  { id: 'pending', label: 'Pending' },
  { id: 'closed', label: 'Closed' },
];

const ASSIGNEE_OPTIONS = [
  { value: 'ash-lynx', label: 'Ash Lynx' },
  { value: 'eiji-okumura', label: 'Eiji Okumura' },
  { value: 'shorter-wong', label: 'Shorter Wong' },
];

const DEFAULT_FILTERS: FilterForm = {
  statuses: [],
  assignee: null,
  budgetRange: [0, 100000],
  dateRange: undefined,
};

interface FilterDrawerProps {
  isOpen: boolean;
  onClose: () => void;
  onApply: (filters: FilterForm) => void;
}

export function FiltersDrawer({ isOpen, onClose, onApply }: FilterDrawerProps) {
  const { form, setField, reset } = useForm<FilterForm>({ defaultValue: DEFAULT_FILTERS });

  const handleApply = () => {
    if (form) onApply(form);
    onClose();
  };

  return (
    <Drawer
      isOpen={isOpen}
      onClose={onClose}
      title="Filters"
      footer={
        <div className="cleen-flex cleen-justify-end cleen-gap-2">
          <Button variant="borderless" label="Clear All" onClick={reset} />
          <Button variant="primary" label="Apply Filters" onClick={handleApply} />
        </div>
      }
    >
      <div className="cleen-flex cleen-flex-col cleen-gap-6">
        <FormGroup title="Status">
          <CheckboxGroup
            checkboxes={STATUS_OPTIONS.map(s => ({
              id: s.id,
              label: s.label,
              checked: form?.statuses.includes(s.id) ?? false,
            }))}
            onChange={updated =>
              setField('statuses', updated.filter(c => c.checked).map(c => c.id as string))
            }
          />
        </FormGroup>

        <FormGroup title="Assignee">
          <Select
            options={ASSIGNEE_OPTIONS}
            value={ASSIGNEE_OPTIONS.find(o => o.value === form?.assignee) ?? null}
            onChange={opt => setField('assignee', opt ? (opt as typeof ASSIGNEE_OPTIONS[0]).value : null)}
            isClearable
          />
        </FormGroup>

        <FormGroup title="Date Range">
          <DatePicker
            mode="range"
            selected={form?.dateRange}
            onSelect={range => setField('dateRange', range as DateRange)}
          />
        </FormGroup>

        <FormGroup title="Budget" subtitle="Drag to set your range">
          <RangeSlider
            value={form?.budgetRange as [number, number]}
            minValue={0}
            maxValue={100000}
            step={1000}
            onChange={val => setField('budgetRange', val)}
          />
        </FormGroup>
      </div>
    </Drawer>
  );
}
```

---

## Multi-Step Form (Wizard)

A form split into sequential steps using the `Wizard` component. Each step maintains form state from the shared `useForm` instance.

```tsx
import {
  Button, FormGroup, Input, RadioButtonGroup,
  Select, TextArea, Wizard
} from '@cleen/cleen-components';
import { useForm, useValidation } from '@cleen/cleen-components';
import { useState } from 'react';

interface OnboardingForm {
  // Step 1
  firstName: string;
  lastName: string;
  email: string;
  // Step 2
  companyName: string;
  companySize: string;
  role: string;
  // Step 3
  goals: string;
  plan: string;
}

const COMPANY_SIZE_OPTIONS = [
  { value: '1-10', label: '1–10 employees' },
  { value: '11-50', label: '11–50 employees' },
  { value: '51-200', label: '51–200 employees' },
  { value: '200+', label: '200+ employees' },
];

const PLAN_OPTIONS = [
  { value: 'starter', title: 'Starter', subTitle: 'Free forever' },
  { value: 'pro', title: 'Pro', subTitle: '$9/mo per seat' },
  { value: 'enterprise', title: 'Enterprise', subTitle: 'Contact sales' },
];

export function OnboardingWizard() {
  const [currentStep, setCurrentStep] = useState(0);
  const { form, setField } = useForm<OnboardingForm>({
    defaultValue: {
      firstName: '', lastName: '', email: '',
      companyName: '', companySize: '', role: '',
      goals: '', plan: 'starter',
    },
  });
  const { errors, setError, clearError, clearErrors } = useValidation<OnboardingForm>({});

  const validateStep = (step: number): boolean => {
    clearErrors();
    if (step === 0) {
      if (!form?.firstName) setError('firstName', 'Required');
      if (!form?.lastName) setError('lastName', 'Required');
      if (!form?.email?.includes('@')) setError('email', 'Enter a valid email');
      return !!form?.firstName && !!form?.lastName && !!form?.email?.includes('@');
    }
    if (step === 1) {
      if (!form?.companyName) setError('companyName', 'Required');
      return !!form?.companyName;
    }
    return true;
  };

  const handleStepChange = (nextStep: number) => {
    // only validate when advancing forward
    if (nextStep > currentStep && !validateStep(currentStep)) return;
    setCurrentStep(nextStep);
  };

  const handleFinish = () => {
    if (!validateStep(currentStep)) return;
    // submit the full form
    console.log('Submitted:', form);
  };

  const steps = [
    {
      title: 'Personal Info',
      content: (
        <div className="cleen-flex cleen-flex-col cleen-gap-4">
          <FormGroup title="First Name" required>
            <Input
              value={form?.firstName}
              onChange={e => { setField('firstName', e.target.value); clearError('firstName'); }}
              infoLabels={{ errorMessage: errors.firstName }}
            />
          </FormGroup>
          <FormGroup title="Last Name" required>
            <Input
              value={form?.lastName}
              onChange={e => { setField('lastName', e.target.value); clearError('lastName'); }}
              infoLabels={{ errorMessage: errors.lastName }}
            />
          </FormGroup>
          <FormGroup title="Email" required>
            <Input
              type="email"
              value={form?.email}
              onChange={e => { setField('email', e.target.value); clearError('email'); }}
              infoLabels={{ errorMessage: errors.email }}
            />
          </FormGroup>
        </div>
      ),
    },
    {
      title: 'Your Company',
      content: (
        <div className="cleen-flex cleen-flex-col cleen-gap-4">
          <FormGroup title="Company Name" required>
            <Input
              value={form?.companyName}
              onChange={e => { setField('companyName', e.target.value); clearError('companyName'); }}
              infoLabels={{ errorMessage: errors.companyName }}
            />
          </FormGroup>
          <FormGroup title="Company Size">
            <Select
              options={COMPANY_SIZE_OPTIONS}
              value={COMPANY_SIZE_OPTIONS.find(o => o.value === form?.companySize) ?? null}
              onChange={opt => setField('companySize', (opt as typeof COMPANY_SIZE_OPTIONS[0])?.value ?? '')}
            />
          </FormGroup>
        </div>
      ),
    },
    {
      title: 'Your Plan',
      content: (
        <div className="cleen-flex cleen-flex-col cleen-gap-4">
          <FormGroup title="Goals" subtitle="What are you hoping to achieve?">
            <TextArea
              value={form?.goals}
              onChange={e => setField('goals', e.target.value)}
              maxLength={300}
              maxLengthLabel={r => `${r} remaining`}
            />
          </FormGroup>
          <FormGroup title="Choose a Plan" required>
            <RadioButtonGroup
              radios={PLAN_OPTIONS}
              value={form?.plan}
              onChange={val => setField('plan', val as string)}
            />
          </FormGroup>
        </div>
      ),
    },
  ];

  return (
    <Wizard
      steps={steps.map(s => ({ title: s.title }))}
      currentStepIndex={currentStep}
      onStepChange={handleStepChange}
      onFinish={handleFinish}
    >
      {steps[currentStep].content}
    </Wizard>
  );
}
```
