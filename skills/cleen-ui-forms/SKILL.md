---
name: cleen-ui-forms
description: Build forms using @cleen/cleen-components. Trigger this skill whenever implementing any form, settings panel, filter panel, edit dialog, onboarding step, or any UI that collects user input. This includes both simple inline forms and complex multi-field configurations. Do NOT write raw HTML form elements, custom input wrappers, or roll your own validation state — the library has all of that. Trigger proactively when a user asks to "create a form", "add a settings page", "build an edit modal", "make a filter panel", "add an input for X", or any similar request involving user data entry.
---

# Forms Skill

This skill covers building forms with the cleen-components library. The library provides everything needed — layout, inputs, state management, and validation. Don't reach for raw `<input>`, `<select>`, or custom wrappers.

---

## Core Building Blocks

| Need | Component / Hook |
|---|---|
| Form layout rows | `FormGroup` |
| Text input | `Input` |
| Multi-line text | `TextArea` |
| Dropdown (static options) | `Select` |
| Dropdown (async search) | `Lookup` |
| Single checkbox | `Checkbox` |
| Multiple checkboxes | `CheckboxGroup` |
| On/off toggle | `Switch` |
| Single choice (text labels) | `RadioButtonGroup` |
| Single choice (rich cards) | `RadioBoxGroup` |
| Date / date range | `DatePicker` |
| Numeric range | `RangeSlider` |
| Single numeric value | `Slider` |
| Field error / info messages | `InfoLabels` |
| Form state management | `useForm` |
| Validation state | `useValidation` |
| Submit / cancel | `Button` (`primary` / `secondary`) |
| AI-assisted text field | `AiInput` / `AiTextArea` |

---

## Layout: FormGroup

`FormGroup` is the standard row layout for forms. It puts the field label on the left (~300px column) and the controls on the right (flex, wrapping).

```tsx
<FormGroup
  title="Full Name"
  subtitle="As it appears on your ID"
  required
  tooltipDescription="This is used for verification"
>
  <Input value={form.name} onChange={e => setField('name', e.target.value)} />
</FormGroup>
```

- `title` is the field label — always set it.
- `subtitle` adds secondary context below the title.
- `required` shows a red `*` mark — it's presentational only, combine with `useValidation` for actual validation.
- `tooltipDescription` adds a `?` icon with a hover tooltip (use for non-obvious fields).
- Multiple controls in `children` flex and wrap naturally.

---

## State: useForm

`useForm` manages the form object and tracks dirtiness.

```tsx
interface ProfileForm {
  name: string;
  email: string;
  role: string;
}

const { form, setField, isDirty, reset } = useForm<ProfileForm>({
  defaultValue: { name: '', email: '', role: '' },
});

// Then in the JSX:
<Input value={form.name} onChange={e => setField('name', e.target.value)} />
```

- `setField(key, value)` — update a single field.
- `setForm(newForm)` — replace the entire form state.
- `isDirty` — true when form differs from `defaultValue` (use to enable/disable Save).
- `reset()` — reverts to `defaultValue`.
- Pass `resetOnDefaultValueChange: false` if you don't want the form to reset when the parent re-renders with new defaults (e.g., after a save).

---

## Validation: useValidation

`useValidation` tracks per-field error strings. Pair it with `InfoLabels` to display them.

```tsx
interface ProfileErrors {
  name?: string;
  email?: string;
}

const { errors, setError, clearError, clearErrors } = useValidation<ProfileErrors>({});

const validate = () => {
  clearErrors();
  if (!form?.name) setError('name', 'Name is required');
  if (!form?.email?.includes('@')) setError('email', 'Enter a valid email address');
  return !errors.name && !errors.email;
};

// Wired to a field:
<Input
  value={form.name}
  onChange={e => {
    setField('name', e.target.value);
    clearError('name');
  }}
  infoLabels={{ errorMessage: errors.name }}
/>
```

- `setError(field, message)` — set an error string on a field.
- `clearError(field)` — clear a single field's error (call on change to show live feedback).
- `clearErrors()` — wipe all errors (call at the start of each validate run).
- Pass error messages into `infoLabels.errorMessage` on Input, Select, DatePicker, Checkbox, etc.

---

## Submit Pattern

Always run validation before submitting. Disable the submit button when not dirty or when loading.

```tsx
const handleSubmit = () => {
  if (!validate()) return;
  // call API...
};

<div className="cleen-flex cleen-justify-end cleen-gap-2">
  <Button variant="secondary" label="Cancel" onClick={reset} />
  <Button
    variant="primary"
    label="Save"
    onClick={handleSubmit}
    disabled={!isDirty}
    isLoading={isSaving}
  />
</div>
```

---

## Field Patterns

### Text inputs

```tsx
// Basic
<Input
  label="Username"
  value={form.username}
  onChange={e => setField('username', e.target.value)}
  infoLabels={{ errorMessage: errors.username }}
/>

// With character limit
<Input
  label="Bio"
  maxLength={160}
  maxLengthLabel={remaining => `${remaining} characters remaining`}
  value={form.bio}
  onChange={e => setField('bio', e.target.value)}
/>

// Multi-line
<TextArea
  label="Description"
  value={form.description}
  onChange={e => setField('description', e.target.value)}
  infoLabels={{ errorMessage: errors.description }}
/>
```

### Dropdowns & async search

```tsx
// Static options
<Select
  label="Role"
  options={[
    { value: 'admin', label: 'Admin' },
    { value: 'editor', label: 'Editor' },
  ]}
  value={{ value: form.role, label: form.role }}
  onChange={opt => setField('role', (opt as { value: string }).value)}
  infoLabels={{ errorMessage: errors.role }}
/>

// Async search
<Lookup
  label="Assign to"
  options={userOptions}
  isLoading={isSearching}
  onInputChange={query => searchUsers(query)}
  value={form.assignee}
  onChange={opt => setField('assignee', opt)}
/>
```

### Boolean toggles

```tsx
// Setting toggle
<Switch
  title="Email notifications"
  checked={form.emailNotifications}
  onChange={e => setField('emailNotifications', e.target.checked)}
/>

// Agreement checkbox
<Checkbox
  label="I agree to the terms and conditions"
  checked={form.agreed}
  onChange={e => setField('agreed', e.target.checked)}
  infoLabels={{ errorMessage: errors.agreed }}
/>
```

### Mutually exclusive choices

```tsx
// Simple list
<RadioButtonGroup
  radios={[
    { value: 'monthly', title: 'Monthly' },
    { value: 'yearly', title: 'Yearly', postTitle: 'Save 20%' },
  ]}
  value={form.billing}
  onChange={val => setField('billing', val)}
/>

// Rich card-style
<RadioBoxGroup
  boxes={[
    { value: 'starter', label: 'Starter', rightElement: <PillBadge label="Free" /> },
    { value: 'pro', label: 'Pro', rightElement: <PillBadge label="$9/mo" /> },
  ]}
  selectedBox={form.plan}
  onBoxClick={val => setField('plan', val)}
/>
```

### Date selection

```tsx
// Single date
<DatePicker
  label="Start Date"
  mode="single"
  selected={form.startDate}
  onSelect={date => setField('startDate', date)}
  infoLabels={{ errorMessage: errors.startDate }}
/>

// Date range
<DatePicker
  label="Project Period"
  mode="range"
  selected={form.period}
  onSelect={range => setField('period', range)}
/>
```

---

## Complete Form Example

See `references/form-patterns.md` for:
- Full settings form with multiple `FormGroup` rows
- Edit modal pattern (form inside `Modal`)
- Filter panel pattern (form inside `Drawer` / `FilterDrawer`)
- Multi-step form pattern (form inside `Wizard`)
