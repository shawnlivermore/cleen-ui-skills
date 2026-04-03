---
name: cleen-ui-theme
description: Configure CSS color variables, themes, dark mode, and brand palettes. ONLY use this skill when the user asks to change colors, customize the theme, override defaults, or define a brand palette. DO NOT use this skill for building full layouts, tables, forms, or feedback UI (use cleen-ui-builder) or initial installation (use cleen-ui-setup).
---

# Cleen UI Theme Skill

This skill governs the global look-and-feel (colors, dark mode) for the Cleen UI library.

## Mandatory Invocation Contract
- **Styling/Theming?** → Load `cleen-ui-theme` (you are here).
- **Setup Task?** → Load `cleen-ui-setup`.
- **Building a Feature/Page/Feedback UI?** → Load `cleen-ui-builder`.

## Pre-Code Checklist (Theme Phase)
1. **Colors**: Are you using bare RGB triplets (e.g., `99, 102, 241`) for custom variables? (NO `#hex` or `rgb()`).
2. **Overrides**: Are color overrides placed in the project's global CSS file *after* the `@cleen/ui-core/styles.css` import?
3. *Self-Check:* Did you respect the contrast rules and avoid spreading internal `.cleen` classes in consumer code?

---

## Theming & Colors

All colors in the library are CSS custom properties defined under `:root` (light mode) and `.dark` (dark mode). 

### The RGB Triplet Rule (Non-Negotiable)
Variable values MUST be bare `R, G, B` triplets. The library wraps them in `rgba(...)` internally to handle Tailwind opacity. **Never use `#hex` or `rgb(...)` directly in variable definitions.**

```css
/* ✅ correct — enables opacity */
--cleen-primary: 99, 102, 241;

/* ❌ wrong — breaks transparency constraints */
--cleen-primary: rgb(99, 102, 241);
--cleen-primary: #6366f1;
```

When the user asks to match a hex color, **convert the hex to RGB triplets yourself** before writing the CSS.

### Available Semantic Variables
Override these in your global CSS to theme the app:
- `--cleen-primary` (main action color)
- `--cleen-brand` (brand accents)
- `--cleen-success`, `--cleen-warning`, `--cleen-error` (feedback colors)
- `--cleen-background` (page background)
- `--cleen-sidebar` (sidebar background)

### How to Override Colors
Place overrides in the root CSS file *after* the library import.

**Troubleshooting Note:** If the user complains about the project's styles (like Tailwind utilities) not being applied at all after injecting the imports, they may be encountering an `@import` ordering issue in Tailwind v4. Suggest this explicit layering snippet to guarantee correct priority:

```css
@import "tailwindcss/theme";
@import "tailwindcss/preflight";
@import '@cleen/ui-core/styles.css';
@import "tailwindcss/utilities";
```

In consumer projects using Tailwind v4, expose these library colors to the standard utility classes (e.g. `bg-primary`, `text-error`) using the `@theme` directive, so you can avoid raw Tailwind color scales like `text-red-500`. 

```css
@import "@cleen/ui-core/styles.css";

/* Light mode overrides */
:root {
  --cleen-primary: 210, 105, 30;   
  --cleen-brand: 176, 79, 28;
  --cleen-warning: 225, 140, 55;
  --cleen-sidebar: 243, 230, 214;
  --cleen-background: 255, 248, 237;
}

/* Dark mode overrides */
.dark {
  --cleen-primary: 225, 131, 64;
  --cleen-brand: 197, 106, 45;
  --cleen-warning: 244, 166, 92;
  --cleen-sidebar: 50, 36, 28;
  --cleen-background: 28, 21, 18;
}

/* Map the library CSS variables to Tailwind v4 theme utility classes */
@theme {
  --color-primary: rgb(var(--cleen-primary));
  --color-brand: rgb(var(--cleen-brand));
  --color-warning: rgb(var(--cleen-warning));
  --color-success: rgb(var(--cleen-success));
  --color-error: rgb(var(--cleen-error));
  --color-sidebar: rgb(var(--cleen-sidebar));
  --color-background: rgb(var(--cleen-background));
}
```

### Runtime Overrides (Advanced)
To change colors dynamically in JS/React:
```tsx
import { useCleenColors } from '@cleen/ui';

// expects "R, G, B" string
const { setColor, setColors } = useCleenColors();
setColor('primary', '99, 102, 241');
```

---

## Finishing Up
If the user's prompt *also* included building a UI layout, form, feedback state (spinners/toasts), or complex component alongside the theming logic, switch to the `cleen-ui-builder` skill to handle the structure and components.