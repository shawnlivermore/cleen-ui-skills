---
name: cleen-ui-configure
description: Configure @cleen/ui — currently CSS color variables. Use this skill whenever the user wants to change colors, customize the theme, set a brand color, adjust the palette, override defaults, make it match their design system, or tweak light/dark mode colors. Trigger proactively even for vague requests like "how do I make it blue?" or "can I change the primary color?" or "how do I theme this?".
---

# Library Configure Skill

This skill covers customizing `@cleen/ui` theming via CSS variables. Currently: **color variables only**.

## Non-Negotiable Theming Rules

- In consumer projects, do not add ad-hoc hex literals for UI theming when a library color variable can be used.
- Define custom project color aliases by inheriting from `--cleen-*` variables.
- Keep all variable values as bare RGB triplets (or `var(--cleen-*)` references), never `#hex` or `rgb(...)` values for variable definitions.
- Ensure readable foreground/background contrast. Target at least 4.5:1 for normal text and 3:1 for large text/UI emphasis.

---

## How the Color System Works

All colors are CSS custom properties defined under `:root` (light) and `.dark` (dark mode). They live in the library's compiled `styles.css`, but you can **override any of them** in your own CSS file after the library import.

The values are bare `R, G, B` triplets — **not** wrapped in `rgb()`. This is intentional: the library uses them as `rgba(var(--cleen-primary))`, which allows transparency to work correctly. **Always provide values in RGB triplet format — this is non-negotiable.**

```css
/* ✅ correct — RGB triplet, enables opacity */
--cleen-primary: 99, 102, 241;

/* ❌ wrong — breaks transparency */
--cleen-primary: rgb(99, 102, 241);
```

**Creating custom CSS variables:** If building color variables for a consumer project (not the library), follow the same RGB triplet format so Tailwind's opacity modifiers work seamlessly. Prefer inheriting values from library variables instead of creating entirely new ones.

```css
/* Good — inherit from library, customize if needed */
--project-primary: var(--cleen-primary);  /* use as-is */
--project-secondary: 236, 72, 153;         /* custom but inherits library color space */

/* Avoid — isolated colors lose design system continuity */
--project-quirky: 167, 139, 250;  /* still isolated if unrelated to semantic variables */
```

---

## All Available Variables

### Semantic Colors
These are the most commonly customized — they drive buttons, badges, alerts, notifications, etc.

| Variable | Light default | Dark default | Purpose |
|---|---|---|---|
| `--cleen-primary` | `0, 133, 211` | `0, 157, 248` | Main action color |
| `--cleen-success` | `6, 118, 71` | `34, 197, 94` | Positive states |
| `--cleen-warning` | `181, 71, 8` | `251, 191, 36` | Caution states |
| `--cleen-error` | `180, 35, 24` | `239, 68, 68` | Destructive/error states |

### Layout Colors
Drive structural UI — sidebar, page background, accent elements.

| Variable | Light default | Dark default | Purpose |
|---|---|---|---|
| `--cleen-brand` | `68, 129, 193` | `0, 122, 204` | Brand-specific accents |
| `--cleen-sidebar` | `249, 250, 251` | `17, 24, 39` | Sidebar background |
| `--cleen-background` | `255, 255, 255` | `3, 7, 18` | Page/content background |
| `--cleen-accent` | `65, 70, 81` | `255, 255, 255` | Text/icon on colored surfaces |

### Palette Colors
General-purpose palette, used across components for tints, borders, and text.

| Variable | Light default | Dark default |
|---|---|---|
| `--cleen-white` | `255, 255, 255` | `3, 7, 18` |
| `--cleen-black` | `0, 0, 0` | `243, 244, 246` |
| `--cleen-gray` | `38, 38, 38` | `209, 213, 219` |
| `--cleen-light-gray` | `200, 200, 200` | `100, 100, 100` |
| `--cleen-pink` | `193, 21, 116` | `236, 72, 153` |
| `--cleen-purple` | `89, 37, 220` | `168, 85, 247` |
| `--cleen-indigo` | `53, 56, 205` | `99, 102, 241` |
| `--cleen-blue` | `23, 92, 211` | `59, 130, 246` |

---

## Strategy: Prefer Library Variables, Minimize Custom Ones

Before creating custom color variables in your project, **check if a library variable already covers the use case**. This keeps your design system cohesive and leverages the library's thought-out palette.

- **Need a primary button color?** → Use or override `--cleen-primary`  
- **Need success/warning/error feedback?** → Use `--cleen-success`, `--cleen-warning`, `--cleen-error`  
- **Need a branded accent?** → Use or override `--cleen-brand`  
- **Building a custom alert banner?** → Inherit from `--cleen-error` or another semantic color

**Only create new variables when the library doesn't have a semantic match.** And if you do, inherit from a library color:

```css
:root {
  /* \u2705 Prefer using library variables directly */
  --app-primary: var(--cleen-primary);
  --app-text-strong: var(--cleen-gray);
  --app-surface: var(--cleen-background);
  
  /* ✅ Alias semantic colors for app-specific naming */
  --app-critical: var(--cleen-error);
  
  /* ❌ Avoid creating arbitrary variables disconnected from semantic roles */
  --app-quirky: 167, 139, 250;
}
```

When Tailwind is available, leverage opacity variants with variable-backed utilities (for example, text utilities using RGB variables and classes such as `text-primary/70`) instead of creating separate hard-coded color values.

---

## Contrast Safety Checklist

Before finalizing overrides:

- Verify primary text against background is at least 4.5:1.
- Verify large text, badges, and UI emphasis elements are at least 3:1.
- Avoid reducing text opacity below readable thresholds on light backgrounds (for example `text-*/40` often fails for body text).
- If a color feels too faint, increase contrast by darkening text aliases (`--app-text-strong`) or lightening surface aliases (`--app-surface`) using library-derived variables.

---

## How to Override Colors

Add overrides in your root CSS file, **after** the library import. Overrides in `:root` apply to light mode; overrides inside `.dark` apply to dark mode.

```css
/* your root CSS file */
@import "@cleen/ui-core/dist/styles.css";

/* --- your overrides below --- */

:root {
  --cleen-primary: 99, 102, 241;   /* indigo-ish */
  --cleen-brand: 99, 102, 241;
  --cleen-sidebar: 245, 245, 255;
}

.dark {
  --cleen-primary: 129, 140, 248;
  --cleen-sidebar: 15, 15, 30;
}
```

> Placing overrides after the import ensures your values win the cascade. You don't need to redeclare every variable — only the ones you want to change.

---

## Converting Colors to the Right Format

The user will likely give you a hex or `rgb()` color. Convert it to a bare `R, G, B` triplet:

```
#6366f1   →   99, 102, 241
#0085d3   →   0, 133, 211
rgb(34, 197, 94)  →  34, 197, 94
```

Do this conversion yourself — don't ask the user to do it.

---

## Typical Customization Requests

### "Change the primary/brand color"
Override `--cleen-primary` and `--cleen-brand` together — they're often visually paired.

### "Match our design system / brand palette"
Ask for their brand hex values (or pull from their existing CSS/Tailwind config if it's in the workspace). Then map them:
- Brand color → `--cleen-primary` + `--cleen-brand`
- Background → `--cleen-background`
- Sidebar shade → `--cleen-sidebar`

### "Change dark mode colors only"
Override inside `.dark {}` only — leave `:root` untouched.

### "Make it look less blue"
`--cleen-primary` is the main blue driver. Swap it to whatever hue the user wants.

---

## Runtime Overrides via `useCleenColors` (Advanced)

If the user's project needs to change colors **at runtime** (e.g. per-tenant theming, user preferences), they can use the `useCleenColors` Zustand store that ships with the library:

```tsx
import { useCleenColors } from '@cleen/ui';

// set a single color (expects "R, G, B" string)
const { setColor, setColors, resetColor, resetColors } = useCleenColors();

setColor('primary', '99, 102, 241');

// set multiple at once
setColors({
  primary: '99, 102, 241',
  brand: '99, 102, 241',
  sidebar: '245, 245, 255',
});

// reset one or all back to CSS defaults
resetColor('primary');
resetColors();
```

Available color keys match the variable names without the `--cleen-` prefix: `white`, `black`, `gray`, `light-gray`, `pink`, `purple`, `indigo`, `blue`, `primary`, `success`, `warning`, `error`, `brand`, `sidebar`, `background`, `accent`.

The store uses `localStorage` persistence (`'cleen-colors'` key) by default, so overrides survive page reloads.

> Suggest runtime overrides only when the user's use case actually calls for it (multi-tenant apps, user theme preferences, etc.). For static branding, CSS overrides are simpler and more performant.
