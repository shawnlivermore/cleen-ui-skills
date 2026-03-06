---
name: cleen-ui-setup
description: Set up @cleen/cleen-components in a user's existing project. Use this skill whenever the user asks how to install, set up, integrate, or add Cleen Components to their project, or when they mention setting up the library, getting started with it, adding the notification container, importing the CSS, or implementing dark mode. Trigger this skill proactively even if the user just says something like "how do I use this in my app?" or "can you help me get this working in my project?".
---

# Library Setup Skill

This skill walks through installing and wiring up `@cleen/cleen-components` into an existing project. It's a short, concrete workflow — no faffing around.

## Steps Overview

1. **Detect package manager** from the user's project
2. **Ensure npm authentication**
3. **Install the library**
4. **Place `<CleenNotificationContainer />`** in the root layout/app file
5. **Import the stylesheet** in the root CSS file
6. _(Optional)_ **Set up dark mode**

---

## Step 1 — Detect Package Manager

Read the user's `package.json` to confirm the project exists and grab the package name for context. Then determine which package manager to use by checking for lockfiles in this priority order:

| Lockfile                 | Package Manager |
| ------------------------ | --------------- |
| `bun.lockb` / `bun.lock` | bun             |
| `pnpm-lock.yaml`         | pnpm            |
| `yarn.lock`              | yarn            |
| `package-lock.json`      | npm             |

If no lockfile is found, default to `npm` and let the user know.

Also check if `@cleen/cleen-components` is already in `dependencies` or `devDependencies`. If it is, skip Step 3 and tell the user it's already installed.

### Check peer dependencies

While reading `package.json`, verify the following peer requirements are met. If any aren't, flag them **before** proceeding — installing the library won't work correctly otherwise.

**React 18**
`react` and `react-dom` must both be `^18.x.x`. If they're on v17 or v19, warn the user:

- v17 → needs an upgrade to v18 (`npm install react@18 react-dom@18`)
- v19 → not yet validated against v19; advise caution and suggest pinning to v18

**react-router-dom**
`react-router-dom` must be present in `dependencies` or `devDependencies`. The library uses it internally and it must be resolvable in the target project. If it's missing, install it alongside the library:

```bash
# npm
npm install react-router-dom

# yarn
yarn add react-router-dom

# pnpm
pnpm add react-router-dom

# bun
bun add react-router-dom
```

If `react-router-dom` is already present but is v5, warn the user — the library expects v6.

---

## Step 2 — Ensure npm Authentication

`@cleen/cleen-components` is a scoped private package — npm needs a valid auth token to install it, otherwise the install will fail with a 404 or 401.

### Option A — `.npmrc` file (recommended for projects)

Check if an `.npmrc` file already exists in the project root. If it contains a line like:

```
//registry.npmjs.org/:_authToken=npm_XXXXX
```

then auth is already configured — proceed to Step 3.

If there's no `.npmrc`, create one in the project root with the user's token:

```
//registry.npmjs.org/:_authToken=npm_XXXXX
```

Replace `npm_XXXXX` with the user's actual token. If they don't know their token, direct them to [npmjs.com → Account → Access Tokens](https://www.npmjs.com/settings/~/tokens) to generate one.

> Add `.npmrc` to `.gitignore` if it contains a real token — you don't want that committed.

### Option B — `.yarnrc.yml` file (Yarn Berry / v2+)

For Yarn Berry projects, auth goes in `.yarnrc.yml`:

```yaml
npmRegistries:
  //registry.npmjs.org:
    npmAuthToken: npm_XXXXX
```

### Option C — CLI login

If the user prefers not to manage a token file, they can authenticate interactively:

```bash
npm login
```

This stores a token globally in `~/.npmrc` and works for the current machine. Less portable for CI/CD, but fine for local dev.

---

## Step 3 — Install the Library

Run the appropriate install command in the user's project root:

```bash
# npm
npm install @cleen/cleen-components

# yarn
yarn add @cleen/cleen-components

# pnpm
pnpm add @cleen/cleen-components

# bun
bun add @cleen/cleen-components
```

Confirm success before proceeding.

---

## Step 4 — Place `<CleenNotificationContainer />`

The `CleenNotificationContainer` needs to live high in the component tree — ideally the root layout, `App.tsx`, or equivalent. It renders the global notification portal.

### Finding the right file

Look for the root entry point in this order:

1. Framework-specific root: `app/layout.tsx` (Next.js App Router), `src/App.tsx`, `src/main.tsx`, `src/index.tsx`
2. Any file that renders `<BrowserRouter>`, `<RouterProvider>`, `<Provider>`, or similar root-level wrappers
3. Ask the user if it's ambiguous

### Placement rules

- Place `<CleenNotificationContainer />` **inside** the outermost JSX return, as a direct sibling to the main content — not nested deep
- It should be a top-level sibling, not wrapping anything
- For Next.js App Router `layout.tsx`, place it inside `<body>` alongside `{children}`

### Import to add

```tsx
import { CleenNotificationContainer } from '@cleen/cleen-components';
```

### Example — standard React App.tsx

**Before:**

```tsx
export default function App() {
  return (
    <BrowserRouter>
      <Routes>...</Routes>
    </BrowserRouter>
  );
}
```

**After:**

```tsx
import { CleenNotificationContainer } from '@cleen/cleen-components';

export default function App() {
  return (
    <BrowserRouter>
      <Routes>...</Routes>
      <CleenNotificationContainer />
    </BrowserRouter>
  );
}
```

### Example — Next.js App Router layout.tsx

**Before:**

```tsx
export default function RootLayout({
  children,
}: {
  children: React.ReactNode;
}) {
  return (
    <html lang="en">
      <body>{children}</body>
    </html>
  );
}
```

**After:**

```tsx
import { CleenNotificationContainer } from '@cleen/cleen-components';

export default function RootLayout({
  children,
}: {
  children: React.ReactNode;
}) {
  return (
    <html lang="en">
      <body>
        {children}
        <CleenNotificationContainer />
      </body>
    </html>
  );
}
```

---

## Step 5 — Import the Stylesheet

The stylesheet **must** be imported for components to look correct. Find the root CSS file and prepend the import.

### Finding the right CSS file

Look for the global stylesheet in this order:

1. `src/index.css`, `src/globals.css`, `src/styles.css`, `src/global.css`
2. `app/globals.css` (Next.js)
3. The CSS file imported in `main.tsx` / `index.tsx` / `layout.tsx`

If no CSS file exists, create `src/index.css` and import it in the root entry file.

### Import to prepend

Add this as the **first line** of the root CSS file (before any other imports or rules, so it doesn't accidentally override custom styles):

```css
@import '@cleen/cleen-components/styles.css';
```

> Putting it first matters — it establishes the base styles before any project-level overrides. Custom styles declared after this import will correctly take precedence.

---

## Step 6 (Optional) — Set Up Dark Mode

The library uses Tailwind's **class-based dark mode** — it activates when a `dark` class (or any class containing `"dark"`) is present on `document.documentElement`. If the user wants dark mode support, they need a way to toggle that class.

Only do this step if the user asks for dark mode or mentions it. Don't set it up unprompted.

### The bare minimum

At its simplest, dark mode is just toggling a class on `<html>`:

```ts
// enable dark mode
document.documentElement.classList.add('dark');

// disable dark mode
document.documentElement.classList.remove('dark');

// toggle
document.documentElement.classList.toggle('dark');
```

### React hook (recommended)

For a proper React integration, add a `useDarkMode` hook the user can drop in anywhere:

```tsx
import { useEffect, useState } from 'react';

export function useDarkMode() {
  const [isDark, setIsDark] = useState(
    () => localStorage.getItem('theme') === 'dark'
  );

  useEffect(() => {
    const root = document.documentElement;
    if (isDark) {
      root.classList.add('dark');
      localStorage.setItem('theme', 'dark');
    } else {
      root.classList.remove('dark');
      localStorage.setItem('theme', 'light');
    }
  }, [isDark]);

  return { isDark, setIsDark, toggle: () => setIsDark(prev => !prev) };
}
```

Usage in a toggle button:

```tsx
const { isDark, toggle } = useDarkMode();

<button onClick={toggle}>{isDark ? 'Light mode' : 'Dark mode'}</button>;
```

### Respecting system preference

If the user wants to default to the OS preference instead of always starting in light mode, swap the initial state:

```ts
const [isDark, setIsDark] = useState(
  () =>
    localStorage.getItem('theme') === 'dark' ||
    (!localStorage.getItem('theme') &&
      window.matchMedia('(prefers-color-scheme: dark)').matches)
);
```

### Next.js note

In Next.js App Router, `document` isn't available during SSR. Either:

- Use the hook in a `'use client'` component only
- Or set the class server-side via a cookie and pass it as a `className` prop on `<html>` in `layout.tsx`

---

## Finishing Up

After completing all steps, give a brief summary:

- Which package manager was used
- Which file received `<CleenNotificationContainer />`
- Which CSS file received the `@import`
- Any caveats or things to watch out for (e.g., SSR considerations for Next.js, if the `"use client"` directive is needed, etc.)

### Next.js / SSR note

If the project uses Next.js App Router, mention that `CleenNotificationContainer` may need `"use client"` at the top of the layout file (or wrapped in a separate client component) if the layout is a Server Component. Flag this proactively rather than waiting for the user to hit a runtime error.
