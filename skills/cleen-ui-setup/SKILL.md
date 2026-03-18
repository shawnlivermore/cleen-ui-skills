---
name: cleen-ui-setup
description: Set up the Cleen UI monorepo packages (@cleen/ui-core, @cleen/ui, @cleen/ui-pro) in a user's existing project. Use this skill whenever the user asks how to install, set up, integrate, or add Cleen Components to their project, or when they mention setting up the library, getting started with it, importing the CSS, wrapper classes, or Tailwind prefixes. Trigger this skill proactively even if the user just says something like "how do I use this in my app?" or "can you help me get this working in my project?".
---

# Cleen UI Setup Skill

This skill walks through installing and wiring up the Cleen UI libraries into an existing project. The architecture consists of three packages:

- `@cleen/ui-core`: Shared hooks, utilities, types, stores, and Tailwind preset/CSS.
- `@cleen/ui`: Main free component library + charts and icons.
- `@cleen/ui-pro`: Premium components built on top of the free and core layers.

## Steps Overview

1. **Detect package manager** from the user's project
2. **Install the required packages** (`@cleen/ui-core`, `@cleen/ui`, and optionally `@cleen/ui-pro`)
3. **Import the root stylesheet** (`@cleen/ui-core/dist/styles.css`)
4. **Apply the `.cleen` wrapper class** to the app root
5. **Place `<CleenNotificationContainer />`** in the root layout/app file
6. _(Optional)_ **Set up dark mode**

---

## Step 1 — Detect Package Manager

Read the user's `package.json` to confirm the project exists and grab the package name for context. Then determine which package manager to use by checking for lockfiles in this priority order (`bun.lockb`/`bun.lock`, `pnpm-lock.yaml`, `yarn.lock`, `package-lock.json`). Default to `npm` if none is found.

### Check peer dependencies

Verify React 18 is installed (`react` and `react-dom` must be `^18.x.x`).
Verify `react-router-dom` v6 is installed, as it is used internally. If missing, install it.

---

## Step 2 — Install Packages

Install the correct packages. Everyone needs `@cleen/ui-core` and `@cleen/ui`. Pro users also need `@cleen/ui-pro`.

```bash
# npm
npm install @cleen/ui-core @cleen/ui

# yarn
yarn add @cleen/ui-core @cleen/ui

# pnpm
pnpm add @cleen/ui-core @cleen/ui

# bun
bun add @cleen/ui-core @cleen/ui
```

_(Add `@cleen/ui-pro` to the command if the user has pro access or requests it)_

**Note on authentication:** `@cleen/ui-pro` is a scoped _private_ registry package, whereas `@cleen/ui` and `@cleen/ui-core` are public. If installation fails with 401/404 when trying to install the pro package, instruct the user to configure their `.npmrc` or `.yarnrc.yml` to authenticate with a valid auth token. No `.npmrc` is needed if downloading only the free packages.

---

## Step 3 — Import the Stylesheet

The core stylesheet **must** be imported for any components to look correct. Prepend the import to the project's global CSS file (e.g., `src/index.css`, `app/globals.css`).

```css
@import '@cleen/ui-core/dist/styles.css';
```

> Put this at the very top of the CSS file before other styles to establish base styles without overriding custom project styles.

---

## Step 4 — Apply the Stylesheet Scope Wrapper

**CRITICAL:** All Cleen UI components expect to be rendered inside a `.cleen` CSS scope. The library uses the `cleen-` prefix for all its generated Tailwind utility classes.

You must wrap the application's root in a `div` or `main` (or the `body` tag) with the `cleen` class.

### Example — standard React App.tsx

```tsx
export default function App() {
  return (
    <div className="cleen">
      <BrowserRouter>
        <Routes>...</Routes>
      </BrowserRouter>
    </div>
  );
}
```

### Example — Next.js App Router layout.tsx

```tsx
export default function RootLayout({
  children,
}: {
  children: React.ReactNode;
}) {
  return (
    <html lang="en">
      <body className="cleen">{children}</body>
    </html>
  );
}
```

**IMPORTANT — Tailwind Usage in Consumer Projects:**

The `cleen-` prefix and library imports are **for the library's internals and integration only**. Once installed, the user's project should use its **own Tailwind configuration** for styling its custom components — not the library's prefixed utilities.

- ✅ Use prefixed utilities only when working inside the library source itself
- ✅ Use the project's default Tailwind config (unprefixed: `flex`, `p-4`, `grid`, etc.) for all consumer project components
- ❌ **Do NOT spread `cleen-` classnames throughout the consumer application** — that's a library concern, not a project concern. 

---

## Step 5 — Place `<CleenNotificationContainer />`

The `CleenNotificationContainer` renders the global notification portal and must live high in the component tree, _inside_ the `.cleen` wrapper.

```tsx
import { CleenNotificationContainer } from '@cleen/ui';

export default function App() {
  return (
    <div className="cleen">
      <BrowserRouter>
        <Routes>...</Routes>
        <CleenNotificationContainer />
      </BrowserRouter>
    </div>
  );
}
```

_Note for Next.js:_ If placing this in a Server Component layout, it might require a `"use client"` wrapper component.

---

## Step 6 (Optional) — Set Up Dark Mode

The library uses Tailwind's **class-based dark mode**. It activates when the `cleen-dark` class (or standard `dark` depending on preset config) is present. Wait for the user to ask for dark mode before configuring this.

---

## Finishing Up

After completing all steps, summarize:

- Packages installed (`@cleen/ui-core`, `@cleen/ui`, etc.)
- Where the CSS (`@cleen/ui-core/dist/styles.css`) was imported
- Where the `.cleen` wrapper class was applied
- Where the `<CleenNotificationContainer />` was placed
