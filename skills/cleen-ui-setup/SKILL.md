---
name: cleen-ui-setup
description: Bootstrap, install, and configure Cleen UI packages (@cleen/ui-core, @cleen/ui, @cleen/ui-pro) in a project. ONLY use this skill when the user asks how to install, set up, initialize, create a new React app, scaffold a Vite project, or add Cleen Components to an empty workspace or existing project. DO NOT use this skill for building features, adding components, or styling pages (use cleen-ui-builder and cleen-ui-theme instead).
---

# Cleen UI Setup Skill

This skill handles the one-time installation and wiring of the Cleen UI libraries into a project. Once the app is running and components are rendering, this skill is no longer needed.

## Mandatory Invocation Contract
- **Setup Task?** → Load `cleen-ui-setup` (you are here).
- **Building a Feature/Page/Feedback UI?** → Load `cleen-ui-builder`.
- **Styling/Theming?** → Load `cleen-ui-theme`.

## Pre-Code Checklist (Setup Phase)
1. **Empty Project?** Ensure you scaffold a React + Vite + TypeScript project first, along with Tailwind CSS.
2. Detect the project's package manager before running install commands.
3. Verify exactly React 18 (not 19) and `react-router-dom` v6 are present.
4. Ensure the core CSS and Notification Container are added to the root layout.
5. *Self-Check:* Did you only handle installation/wiring? If the user also asked to build a page, hand off the architecture and component construction to `cleen-ui-builder`.

## Steps Overview

1. **Scaffold the project (if starting from scratch)** using React + Vite + TS.
2. **Detect package manager** from the user's project
3. **Install the required packages** (`@cleen/ui-core`, `@cleen/ui`, and optionally `@cleen/ui-pro`)
4. **Import the root stylesheet** (`@cleen/ui-core/styles.css`)
5. **Apply Tailwind-first styling rules** in the project's global stylesheet (not `App.css`)
6. **Place `<CleenNotificationContainer />`** in the root layout/app file
7. _(Optional)_ **Set up dark mode**

---

## Step 1 — Scaffold the project (If Starting from Scratch)

If the user has an empty workspace or explicitly asks to create a new application, generate a React + Vite project using TypeScript by default (unless they specify otherwise). 

**CRITICAL:** Vite natively scaffolds **React 19** by default, but Cleen UI packages specifically require **React 18**. You MUST explicitly install `react@18` and `react-dom@18` to align package requirements and prevent peer dependency warnings.

```bash
# npm
npm create vite@latest . -- --template react-ts
npm install react@18 react-dom@18
npm install -D tailwindcss @tailwindcss/vite

# bun
bun create vite . --template react-ts
bun add react@18 react-dom@18
bun add -D tailwindcss @tailwindcss/vite
```

The library's internal classes (`cleen-`) are pre-compiled and handled by importing its `styles.css`. Therefore, the consumer's app can safely run **Tailwind v4** without any `tailwind.config.js` bloat. 

To configure Tailwind v4 in the Vite project:

1. Update `vite.config.ts` to include the Tailwind plugin:
```ts
import { defineConfig } from 'vite'
import react from '@vitejs/plugin-react'
import tailwindcss from '@tailwindcss/vite'

export default defineConfig({
  plugins: [react(), tailwindcss()],
})
```

2. Import Tailwind into the project's root CSS (`src/index.css`):
```css
@import "tailwindcss";
```

---

## Step 2 — Detect Package Manager

Read the user's `package.json` to confirm the project exists and grab the package name for context. Then determine which package manager to use by checking for lockfiles in this priority order (`bun.lockb`/`bun.lock`, `pnpm-lock.yaml`, `yarn.lock`, `package-lock.json`). Default to `npm` if none is found.

### Check peer dependencies

Verify exactly React 18 is installed (`react` and `react-dom` must be `^18.x.x`). Do NOT use React 19, as it causes peer dependency warnings. If the project scaffolding installed React 19, downgrade it to 18 (`npm install react@18 react-dom@18`).
Verify `react-router-dom` v6 is installed, as it is used internally. If missing, install it.

---

## Step 3 — Install Packages

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

**CRITICAL (PRO PACKAGE INSTALLS):** `@cleen/ui-pro` is a scoped *private* registry package. Before you ever attempt to run an install command containing `@cleen/ui-pro`, you MUST explicitly ask the user for their NPM auth token so that you can configure the `.npmrc` or `.yarnrc.yml` for them. Alternatively, suggest they run `npm login` in their terminal if their regular NPM account already has access to the pro package. Never attempt to install it blindly without setting up authentication first, as the installation will fail with a 401/404 error. The free packages (`@cleen/ui-core` and `@cleen/ui`) are public and do not require this step.

---

## Step 4 — Import the Stylesheet

The core stylesheet **must** be imported for any components to look correct. Prepend the import to the project's global CSS file (e.g., `src/index.css`, `app/globals.css`).

If both a global CSS file and `App.css` exist, prefer the global CSS entry (`index.css`/`globals.css`) and avoid introducing new component/layout classes in `App.css` when Tailwind is installed.

```css
@import "tailwindcss";
@import '@cleen/ui-core/styles.css';
```

> Put the library import directly beneath `@import "tailwindcss";` to establish the base styles without overriding custom project rules. If the user complains that their Tailwind utility CSS classes are not applying or preflight is broken, switch the simple `@import "tailwindcss";` to explicit v4 module layering:
> 
> ```css
> @import "tailwindcss/theme";
> @import "tailwindcss/preflight";
> @import '@cleen/ui-core/styles.css';
> @import "tailwindcss/utilities";
> ```

---

## Step 5 — Tailwind-First Styling Rules

**CRITICAL:** In consumer projects, use the project's own Tailwind utilities and component classes. Do not add `.cleen` wrapper classes in app code and do not spread `cleen-` prefixed utility classes through the project.

Use this checklist:

- Keep styling in Tailwind utility classes first
- Keep global variable overrides in `index.css` / `globals.css`
- Do not create new layout/component classes in `App.css` when Tailwind already exists

**IMPORTANT — Tailwind Usage in Consumer Projects:**

The `cleen-` prefix and library imports are **for the library's internals and integration only**. Once installed, the user's project should use its **own Tailwind configuration** for styling its custom components — not the library's prefixed utilities.

- ✅ Use the project's default Tailwind config (unprefixed: `flex`, `p-4`, `grid`, etc.) for all consumer project components
- ❌ **Do NOT spread `cleen-` classnames throughout the consumer application** — that's a library concern, not a project concern. 
- ❌ **Do NOT add `.cleen` wrapper classes to app roots in consumer projects**.

---

## Step 6 — Place `<CleenNotificationContainer />`

The `CleenNotificationContainer` renders the global notification portal and must live high in the component tree.

```tsx
import { CleenNotificationContainer } from '@cleen/ui';

export default function App() {
  return (
    <BrowserRouter>
      <Routes>...</Routes>
      <CleenNotificationContainer />
    </BrowserRouter>
  );
}
```

_Note for Next.js:_ If placing this in a Server Component layout, it might require a `"use client"` wrapper component.

---

## Step 7 (Optional) — Set Up Dark Mode

The library uses Tailwind's **class-based dark mode**. In consumer projects, prefer the standard `dark` class strategy from the host app's Tailwind config. Wait for the user to ask for dark mode before configuring this.

---

## Finishing Up

After completing all steps, summarize:

- Packages installed (`@cleen/ui-core`, `@cleen/ui`, etc.)
- Where the CSS (`@cleen/ui-core/styles.css`) was imported
- Confirmation that no `.cleen` wrapper class was introduced in consumer app code
- Where the `<CleenNotificationContainer />` was placed

**STOP: Do not proceed to build features, layouts, forms, or data grids.**
If the user also requested to build a page or component, instruct them that the setup is complete and you will now use the `cleen-ui-builder` skill to construct the app.
