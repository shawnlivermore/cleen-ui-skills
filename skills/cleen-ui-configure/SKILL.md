---
name: cleen-ui-configure
description: Configure @cleen/cleen-components — currently CSS color variables. Use this skill whenever the user wants to change colors, customize the theme, set a brand color, adjust the palette, override defaults, make it match their design system, or tweak light/dark mode colors. Trigger proactively even for vague requests like "how do I make it blue?" or "can I change the primary color?" or "how do I theme this?".
---

# Library Configure Skill

This skill covers customizing `@cleen/cleen-components` theming via CSS variables. Currently: **color variables only**.

---

## How the Color System Works

All colors are CSS custom properties defined under `:root` (light) and `.dark` (dark mode). They live in the library's compiled `styles.css`, but you can **override any of them** in your own CSS file after the library import.

The values are bare `R, G, B` triplets — **not** wrapped in `rgb()`. This is intentional: the library uses them as `rgba(var(--cleen-primary))`, which allows transparency to work correctly. Always provide values in this format.

```css
/* correct */
--cleen-primary: 99, 102, 241;

/* wrong — will break transparency */
--cleen-primary: rgb(99, 102, 241);
--cleen-primary: #6366f1;
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

## How to Override Colors

Add overrides in your root CSS file, **after** the library import. Overrides in `:root` apply to light mode; overrides inside `.dark` apply to dark mode.

```css
/* your root CSS file */
@import "@cleen/cleen-components/styles.css";

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
import { useCleenColors } from '@cleen/cleen-components';

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
