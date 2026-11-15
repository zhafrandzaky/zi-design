# DESIGN.md

A reusable UI/UX standard for every project. This file holds the rules and the defaults. It is not edited per project: project-specific values go in `brand.md` (see section 1).

Keywords: **MUST** is mandatory, **SHOULD** is the default unless there is a stated reason, **NEVER** is forbidden.

---

## 0. Instructions for AI agents

1. Before any UI work, read this file and `brand.md` (if it exists in the project root).
2. Never hardcode a hex, rgb, or named color in a component. Use the semantic tokens in section 3.
3. Never invent a new color, font size, spacing value, radius, or shadow outside the scales here. If a design truly needs one, add it as a token and say so in your summary.
4. When changing an existing project, adopt the standard incrementally: map the project's existing tokens to the ones here instead of rewriting every component at once.
5. If `brand.md` contradicts a **MUST** rule here (for example, a palette that fails contrast), keep the rule, apply the closest compliant value, and report the conflict to the owner.
6. Run the checklist in section 16 before declaring UI work done.
7. Rules here prevent noise, not character. A page that only avoids mistakes is not done: landing and marketing pages **MUST** also meet section 14.1 (Expressive moments).

---

## 1. Configuration and precedence

Values are resolved in this order (first match wins):

1. `brand.md` in the project root (owner overrides, copied from `brand.template.md`)
2. Configuration that already exists in the project (installed icon library, existing logo files, existing font setup)
3. Defaults in this file

Any field left empty in `brand.md` falls back to the default.

### Defaults

| Setting | Default |
|---|---|
| Primary (brand) | Royal Blue `#000070` |
| Highlight (CTA accent) | Nitro Orange `#fb5607` |
| Ink (dark neutral) | Blackstone `#222529` |
| Paper (light neutral) | Whiteout `#e9ecef` |
| Sans font | Geist |
| Mono font | Geist Mono |
| Display font | same as sans |
| Icon library | Lucide |
| Styling | Tailwind CSS v4 on top of CSS custom properties |
| Theme | Light and dark, one header toggle (first visit follows the system) |

---

## 2. Principles

- **Content first.** Layout exists to make content readable and actions obvious. Decoration that does not help comprehension is removed.
- **One system.** Every color, size, space, radius, shadow and duration comes from a token. Consistency beats novelty.
- **Flat and quiet by default.** Solid colors, thin borders, restrained motion. Emphasis is scarce so it stays meaningful.
- **Bold where it counts.** Every landing page has deliberate moments of solid brand color and a real product interaction (section 14.1). Quiet is not the same as generic.
- **Accessible always.** WCAG 2.2 AA is the floor, in both themes.
- **Honest.** Real content, real data, real screenshots. Nothing fake.

---

## 3. Color

### 3.1 Roles and proportion

| Role | Token | Used for | Share |
|---|---|---|---|
| Neutrals | `background`, `card`, `muted`, `border`, `foreground` | Page, surfaces, text, dividers | ~60% |
| Primary | `primary` | Brand, header/nav accents, links, main action buttons, focus ring, selected states | ~30% |
| Highlight | `highlight` | The single most important CTA on a screen, key highlights, "new" badges | ~10% |
| Status | `success`, `warning`, `destructive`, `info` | Feedback only, never decoration | as needed |
| Color blocks | `brand-surface`, `stage` | Full-width sections: hero, product demo, CTA band | marketing pages only |

Rules:

- **MUST** keep `highlight` scarce: at most one highlight-filled button per screen. In app/dashboard screens, the main action is usually `primary`, not `highlight`.
- **MUST** use status colors only for status meaning.
- **NEVER** use color as the only carrier of meaning: pair it with an icon, text or shape.

### 3.2 Contrast facts for the default palette

| Pair | Ratio | Consequence |
|---|---|---|
| White on Nitro Orange | 3.3:1 | Fails for normal text. Text on orange uses `highlight-foreground` (Blackstone, 4.7:1). |
| Nitro Orange on light background | 3.2:1 | Orange text on light surfaces uses `highlight-text` (darkened, 4.9:1). |
| Royal Blue on Blackstone | 1.1:1 | Dark theme uses a lightened primary (5.7:1). |
| Royal Blue on light background | 15.9:1 | Safe. |

### 3.3 Tokens

Primitives are the only raw values. Everything else is derived with `color-mix()` and `light-dark()`, so changing the four primitives in `brand.md` re-themes the whole project.

Names follow the shadcn/ui convention so shadcn components work unchanged. Note: in shadcn, `accent` means the **subtle hover surface**, not the brand accent. The brand orange is `highlight`.

```css
/* tokens.css */
:root {
  /* ---- Primitives (overridable in brand.md) ---- */
  --brand-primary:   #000070; /* Royal Blue */
  --brand-highlight: #fb5607; /* Nitro Orange */
  --brand-ink:       #222529; /* Blackstone */
  --brand-paper:     #e9ecef; /* Whiteout */

  --status-success: light-dark(#15803d, #4ade80);
  --status-warning: light-dark(#b45309, #fbbf24);
  --status-danger:  light-dark(#c0262d, #f87171);

  /* ---- Theme switch ---- */
  color-scheme: light dark;

  /* ---- Surfaces and text ---- */
  --background:         light-dark(color-mix(in oklab, var(--brand-paper) 40%, white), var(--brand-ink));
  --foreground:         light-dark(var(--brand-ink), var(--brand-paper));
  --card:               light-dark(white, color-mix(in oklab, white 4%, var(--brand-ink)));
  --card-foreground:    var(--foreground);
  --popover:            light-dark(white, color-mix(in oklab, white 8%, var(--brand-ink)));
  --popover-foreground: var(--foreground);
  --muted:              light-dark(var(--brand-paper), color-mix(in oklab, white 8%, var(--brand-ink)));
  --muted-foreground:   light-dark(color-mix(in oklab, var(--brand-ink) 70%, white),
                                   color-mix(in oklab, var(--brand-paper) 70%, var(--brand-ink)));

  /* ---- Lines ---- */
  --border: light-dark(color-mix(in oklab, var(--brand-ink) 14%, var(--brand-paper)),
                       color-mix(in oklab, white 12%, var(--brand-ink)));   /* decorative dividers */
  --input:  light-dark(color-mix(in oklab, var(--brand-ink) 45%, var(--brand-paper)),
                       color-mix(in oklab, white 42%, var(--brand-ink)));   /* form controls, >= 3:1 */

  /* ---- Primary ---- */
  --primary:            light-dark(var(--brand-primary), color-mix(in oklab, var(--brand-primary) 40%, white));
  --primary-hover:      light-dark(color-mix(in oklab, var(--brand-primary) 85%, white),
                                   color-mix(in oklab, var(--brand-primary) 30%, white));
  --primary-foreground: light-dark(white, var(--brand-ink));
  --primary-soft:       light-dark(color-mix(in oklab, var(--brand-primary) 8%, white),
                                   color-mix(in oklab, var(--brand-primary) 25%, var(--brand-ink)));
  --ring:               var(--primary);

  /* ---- Highlight ---- */
  --highlight:            var(--brand-highlight);
  --highlight-hover:      color-mix(in oklab, var(--brand-highlight) 85%, white);
  --highlight-foreground: var(--brand-ink);
  --highlight-soft:       light-dark(color-mix(in oklab, var(--brand-highlight) 8%, white),
                                     color-mix(in oklab, var(--brand-highlight) 12%, var(--brand-ink)));
  --highlight-text:       light-dark(color-mix(in oklab, var(--brand-highlight) 70%, var(--brand-ink)),
                                     color-mix(in oklab, var(--brand-highlight) 85%, white));

  /* ---- shadcn aliases ---- */
  --secondary:            var(--muted);
  --secondary-foreground: var(--foreground);
  --accent:               var(--primary-soft);
  --accent-foreground:    var(--foreground);

  /* ---- Status ---- */
  --success:                var(--status-success);
  --success-foreground:     light-dark(white, var(--brand-ink));
  --warning:                var(--status-warning);
  --warning-foreground:     light-dark(white, var(--brand-ink));
  --destructive:            var(--status-danger);
  --destructive-foreground: light-dark(white, var(--brand-ink));
  --info:                   var(--primary);
  --info-foreground:        var(--primary-foreground);
  /* Soft status backgrounds: pair with --foreground text and a status-colored icon/border */
  --success-soft:     light-dark(color-mix(in oklab, var(--success) 8%, white), color-mix(in oklab, var(--success) 12%, var(--brand-ink)));
  --warning-soft:     light-dark(color-mix(in oklab, var(--warning) 8%, white), color-mix(in oklab, var(--warning) 12%, var(--brand-ink)));
  --destructive-soft: light-dark(color-mix(in oklab, var(--destructive) 8%, white), color-mix(in oklab, var(--destructive) 12%, var(--brand-ink)));
  --info-soft:        var(--primary-soft);

  /* ---- Expressive surfaces: solid color blocks, same in both themes ---- */
  --brand-surface:            var(--brand-primary);
  --brand-surface-foreground: white;
  --brand-surface-muted:      color-mix(in oklab, white 75%, var(--brand-primary));
  --brand-surface-raised:     color-mix(in oklab, white 8%, var(--brand-primary));  /* hover on brand blocks */
  --brand-surface-border:     color-mix(in oklab, white 18%, var(--brand-primary));
  --stage:            light-dark(var(--brand-ink), color-mix(in oklab, var(--brand-ink) 60%, black)); /* dark demo block */
  --stage-foreground: var(--brand-paper);
  --stage-muted:      color-mix(in oklab, var(--brand-paper) 70%, var(--brand-ink));
  --stage-border:     color-mix(in oklab, white 14%, var(--stage));

  /* ---- Overlay and shadow color ---- */
  --overlay:      color-mix(in oklab, var(--brand-ink) 55%, transparent);
  --shadow-color: light-dark(color-mix(in oklab, var(--brand-ink) 10%, transparent), rgb(0 0 0 / 0.45));
}

:root[data-theme="light"] { color-scheme: light; }
:root[data-theme="dark"]  { color-scheme: dark; }
```

Measured contrast of the default tokens (all pass AA):

| Text / element | Light | Dark |
|---|---|---|
| `foreground` on `background` | 14.4:1 | 13.0:1 |
| `muted-foreground` on `background` | 6.0:1 | 6.6:1 |
| `primary-foreground` on `primary` | 17.0:1 | 5.7:1 |
| `highlight-foreground` on `highlight` | 4.7:1 | 4.7:1 |
| `highlight-text` on `background` | 4.9:1 | 5.8:1 |
| `input` border on `background` | 3.2:1 | 3.5:1 |
| `destructive` text on `background` | 5.5:1 | 5.6:1 |
| `brand-surface-foreground` on `brand-surface` | 17.0:1 | 17.0:1 |
| `brand-surface-muted` on `brand-surface` | 9.5:1 | 9.5:1 |
| `highlight` on `brand-surface` (UI, large text) | 5.2:1 | 5.2:1 |
| `stage-foreground` on `stage` | 13.0:1 | 16.4:1 |
| `stage-muted` on `stage` | 6.6:1 | 8.4:1 |

### 3.4 Changing the palette

When the owner sets new primitives in `brand.md`, the agent **MUST**:

1. Recompute the contrast table above for both themes.
2. If a pair fails, adjust only the mix percentage of the failing token (record the new value in `brand.md` under "Token adjustments"), never the rule.
3. Prefer: dark primary that is still a recognizable tint of the brand color; `highlight-foreground` of ink or white, whichever passes.

---

## 4. Theming (light and dark)

- **MUST** support light and dark. On the first visit the theme follows the system preference; after that the user's choice wins and is stored in `localStorage` under `theme`.
- The control is **one toggle button** (light/dark) in the header, showing a moon icon in light mode and a sun icon in dark mode, with an `aria-label`. No "System" option, no dropdown. In apps, a copy of the setting may also live on the settings page.
- **NEVER** place the theme control in the footer.
- **MUST** prevent the flash of the wrong theme: put this inline in `<head>`, before any stylesheet:

```html
<script>
  (function () {
    var d = document.documentElement;
    d.classList.add("js");
    try {
      var t = localStorage.getItem("theme");
      var dark = t === "dark" || (t !== "light" && matchMedia("(prefers-color-scheme: dark)").matches);
      d.dataset.theme = dark ? "dark" : "light";
    } catch (e) {}
  })();
</script>
```

Toggle handler:

```js
document.querySelector("[data-theme-toggle]").addEventListener("click", () => {
  const next = document.documentElement.dataset.theme === "dark" ? "light" : "dark";
  document.documentElement.dataset.theme = next;
  try { localStorage.setItem("theme", next); } catch (e) {}
});
```

- Until the user has toggled once, follow `matchMedia("(prefers-color-scheme: dark)")` changes.
- Without JavaScript, `light-dark()` still follows the system theme.
- **SHOULD** style colors through tokens, not `dark:` utilities. Use `dark:` only for non-color differences (for example swapping an image).
- Dark theme shows depth with lighter surfaces (`card`, `popover`), not stronger shadows.
- Images and illustrations that are unreadable on dark **MUST** get a dark variant or a neutral backdrop.

---

## 5. Typography

### 5.1 Families

```css
:root {
  --brand-font-sans:    "Geist", ui-sans-serif, system-ui, -apple-system, "Segoe UI", Roboto, sans-serif;
  --brand-font-mono:    "Geist Mono", ui-monospace, "SFMono-Regular", Menlo, Consolas, monospace;
  --brand-font-display: var(--brand-font-sans); /* overridable in brand.md for landing heroes */
}
```

- Load Geist from the `geist` npm package, `next/font` (Next.js), or Google Fonts. Self-host when possible.
- `next/font` and the `geist` package rename the family (hashed name) and expose it as a variable. Point the tokens at that variable, not at `"Geist"`:

```css
:root {
  --brand-font-sans: var(--font-geist-sans), ui-sans-serif, system-ui, sans-serif;
  --brand-font-mono: var(--font-geist-mono), ui-monospace, monospace;
}
```
- `font-display: swap`; preload only the main sans file.
- The display font (if set in `brand.md`) is used only for hero and H1 on marketing pages. App screens always use sans.

### 5.2 Scale

Base size 16px, ratio about 1.25 (matches Tailwind defaults).

| Role | Size | Tailwind | Weight | Line height | Tracking |
|---|---|---|---|---|---|
| Display (hero) | `clamp(2.5rem, 1.6rem + 3.2vw, 3.75rem)` | custom | 700 | 1.05 | -0.03em |
| H1 | 36px | `text-4xl` | 700 | 1.15 | -0.02em |
| H2 | 30px | `text-3xl` | 600 | 1.2 | -0.02em |
| H3 | 24px | `text-2xl` | 600 | 1.3 | -0.01em |
| H4 | 20px | `text-xl` | 600 | 1.4 | -0.01em |
| Body large | 18px | `text-lg` | 400 | 1.6 | 0 |
| Body | 16px | `text-base` | 400 | 1.6 | 0 |
| UI / dense body | 14px | `text-sm` | 400 or 500 | 1.5 | 0 |
| Caption, labels | 12px | `text-xs` | 500 | 1.4 | 0.01em |

Rules:

- **MUST** use only weights 400, 500, 600, 700. **NEVER** use weights below 400 for text under 24px.
- **MUST** keep body text at 16px on content pages; 14px is allowed for dense app UI; 12px is the minimum and only for captions, labels and metadata.
- **MUST** limit reading width to 65 to 75 characters (`max-w-prose` or `max-w-[68ch]`).
- **MUST** use `font-variant-numeric: tabular-nums` (`tabular-nums`) for numbers in tables, stats, prices and timers.
- Headings use sentence case. **NEVER** all-caps paragraphs; uppercase is allowed only for short labels at `text-xs` with `tracking-wider`.
- Hierarchy comes from size and weight, not from color. Do not mix more than 3 sizes in one card.
- Use `text-wrap: balance` for headings and `text-wrap: pretty` for paragraphs.
- Code, keyboard keys, URLs/handles, IDs and hashes use the mono font. **NEVER** use mono for money, prices or statistics: monospace digits (slashed zero, wide separators) make amounts read like code. Use sans with `tabular-nums`.
- Big numbers (step numbers, a headline price, a calculator result) are allowed at `text-5xl` to `text-7xl`, weight 700, tracking `-0.05em`, `tabular-nums`. One per block.

---

## 6. Layout, spacing and shape

### 6.1 Spacing

- 4px base unit. Allowed steps (Tailwind): `1 2 3 4 5 6 8 10 12 16 20 24 32` (4px to 128px).
- Related items are closer than unrelated items. Inside a component: 8 to 16px; between components: 24 to 32px; between page sections: 64px mobile, 96px desktop (`py-16 md:py-24`).
- **NEVER** use arbitrary values like `mt-[13px]`.

### 6.2 Layout

- Mobile first. Breakpoints: Tailwind defaults (`sm 640`, `md 768`, `lg 1024`, `xl 1280`, `2xl 1536`).
- Container: `max-w-6xl` (1152px) for marketing and content, full width with side padding for app/dashboard layouts. Side padding: `px-4 sm:px-6 lg:px-8`.
- 12-column grid for complex layouts; prefer simple flex/grid with `gap`.
- Left-align text by default. Center only short hero text and empty states.
- Sticky header height 64px (`--header-height: 4rem`), solid `background` with a bottom `border`.

### 6.3 Radius

| Token | Value | Used for |
|---|---|---|
| `rounded-sm` | 4px | Checkboxes, small tags, inline code |
| `rounded-md` | 6px | Buttons, inputs, selects, menu items |
| `rounded-lg` | 8px | Cards, popovers, dropdowns |
| `rounded-xl` | 12px | Modals, large panels, image containers |
| `rounded-full` | - | Avatars, status dots, toggle switches only |

- Nested elements: inner radius = outer radius minus padding (or the next step down).
- **NEVER** round everything to `rounded-2xl`/`rounded-3xl`. **NEVER** use pill buttons by default.

### 6.4 Elevation

Three levels only. Most surfaces are flat with a border.

```css
:root {
  --elevation-1: 0 1px 2px 0 var(--shadow-color);                                          /* hover on raised items, sticky bars */
  --elevation-2: 0 4px 12px -2px var(--shadow-color), 0 2px 4px -2px var(--shadow-color);  /* dropdowns, popovers, toasts */
  --elevation-3: 0 16px 40px -8px var(--shadow-color);                                     /* modals, sheets */
}
```

- Cards at rest: border, no shadow.
- **NEVER** colored shadows, glow, stacked heavy shadows, or a shadow on every card.

### 6.5 Z-index

`base 0` / `dropdown 10` / `sticky 20` / `overlay 40` / `modal 50` / `toast 60` / `tooltip 70`. Never use values outside this list.

---

## 7. Icons and imagery

### 7.1 Icon library

1. Use the icon library already installed in the project (check `package.json`: `lucide-*`, `@phosphor-icons/*`, `@tabler/icons-*`, `@heroicons/*`, `@iconify/*`).
2. If none is installed, use **Lucide** (`lucide-react`, `lucide-vue-next`, `lucide-svelte`, or `lucide` / inline SVG for plain HTML).
3. **MUST** use one library per project. Do not mix sets.

Rules:

- Sizes: 16px (dense UI, inline with `text-sm`), 20px (default UI), 24px (navigation, feature lists). One stroke width per project (Lucide default 2; 1.75 allowed if set consistently).
- Icons inherit color via `currentColor`. Icon color follows the text it sits with, or `muted-foreground` for secondary icons.
- Decorative icons get `aria-hidden="true"`. Icon-only buttons **MUST** have `aria-label` and a tooltip.
- Custom SVG icons **MUST** match the library grid (24x24), stroke width, and round caps/joins.
- **NEVER** place icons inside colored circles as decoration.

### 7.2 Emoji and symbols

- **NEVER** use emoji in UI, headings, buttons, labels, toasts, empty states, marketing copy, or project docs.
- **NEVER** use Unicode symbols as icons (check marks, crosses, arrows, stars, bullets). Use the icon library.
- Exception: user-generated content renders whatever the user typed.

### 7.3 Images

- Real product screenshots and real photos over illustrations. No generic stock "team high-five" or "AI brain" imagery.
- Formats: AVIF or WebP with PNG/JPEG fallback only when needed; SVG for logos and diagrams.
- Always set `width` and `height` (or `aspect-ratio`) to avoid layout shift.
- Meaningful images need `alt`; decorative images use `alt=""`.

---

## 8. Logo and favicon

### 8.1 Source

1. If `brand.md` lists asset paths, use them.
2. Otherwise search the project for owner assets: `public/`, `static/`, `assets/`, `src/assets/` for `logo.*`, `logo-mark.*`, `favicon.*`, `icon.*`.
3. If owner assets exist: **NEVER** redesign them. Only generate missing sizes from them.
4. If none exist: create one (8.2).

### 8.2 Creating a logo

- SVG first. Build a wordmark (app name set in the brand font, tuned spacing) plus a simple mark (monogram or geometric symbol derived from the name or the product's purpose).
- Use `--brand-primary` and optionally `--brand-highlight`. Flat fills, no gradients, no shadows, no glow.
- **NEVER** use generic AI motifs (sparkles, brains, robots, rockets, lightning bolts, magic wands) unless the product is about exactly that thing.
- The mark **MUST** stay readable at 16x16.
- If an image-generation tool is available and the owner wants an illustrative logo, it may be used, but the result **MUST** still be delivered as SVG (traced or rebuilt) plus the raster sizes below.

### 8.3 Required outputs

| File | Size | Notes |
|---|---|---|
| `logo.svg` | vector | Full logo (mark + wordmark) |
| `logo-mark.svg` | vector | Mark only |
| `logo.webp` | 512px wide | Raster use (emails, docs) |
| `favicon.svg` | vector | May include a `prefers-color-scheme` style block for dark tabs |
| `favicon.ico` | 16, 32, 48 | Multi-size |
| `apple-touch-icon.png` | 180x180 | Solid background, no transparency |
| `icon-192.png`, `icon-512.png` | 192, 512 | PWA |
| `icon-maskable-512.png` | 512 | Mark inside the central 80% safe zone |
| `og-image.png` (or `.webp`) | 1200x630 | Logo, title, short tagline on brand background |

Convert with `sharp` (Node), `resvg`, or ImageMagick (`magick`). Place files in the framework's public/static folder.

```html
<link rel="icon" href="/favicon.ico" sizes="48x48">
<link rel="icon" href="/favicon.svg" type="image/svg+xml">
<link rel="apple-touch-icon" href="/apple-touch-icon.png">
<link rel="manifest" href="/manifest.webmanifest">
<meta name="theme-color" content="#000070" media="(prefers-color-scheme: light)">
<meta name="theme-color" content="#222529" media="(prefers-color-scheme: dark)">
```

`theme-color` values follow `--brand-primary` / `--brand-ink`.

---

## 9. Motion

### 9.1 Tokens

```css
:root {
  --duration-fast:   150ms; /* color, border, opacity on hover */
  --duration-base:   200ms; /* fills, small movements */
  --duration-slow:   300ms; /* overlays, drawers */
  --duration-reveal: 500ms; /* scroll reveal */
  --motion-ease-out:    cubic-bezier(0.2, 0, 0, 1);
  --motion-ease-in-out: cubic-bezier(0.4, 0, 0.2, 1);
}
```

Rules:

- **MUST** animate only `transform`, `opacity`, `color`, `background-color`, `border-color`, `outline-color`. **NEVER** animate `width`, `height`, `top`, `left`, `margin`, or `box-shadow` on large areas.
- **MUST** respect reduced motion (9.6).
- Hover effects apply only to interactive elements and only on devices that hover: wrap in `@media (hover: hover)`.
- Every hover style has a matching `:focus-visible` style.

### 9.2 Buttons

Default (solid) buttons: background color shifts to the `*-hover` token. Press feedback: `scale(0.98)`.

```css
.btn {
  transition: background-color var(--duration-fast) var(--motion-ease-out),
              color var(--duration-fast) var(--motion-ease-out),
              border-color var(--duration-fast) var(--motion-ease-out),
              transform var(--duration-fast) var(--motion-ease-out);
}
.btn:active:not(:disabled) { transform: scale(0.98); }
@media (hover: hover) {
  .btn-primary:hover   { background-color: var(--primary-hover); }
  .btn-highlight:hover { background-color: var(--highlight-hover); }
}
```

Fill variant (outline buttons, marketing CTAs): color fills from left to right on hover.

```css
.btn-fill {
  position: relative;
  isolation: isolate;
  overflow: hidden;
  color: var(--primary);
  border: 1px solid var(--primary);
  background: transparent;
}
.btn-fill::before {
  content: "";
  position: absolute;
  inset: 0;
  z-index: -1;
  background: var(--primary);
  transform: scaleX(0);
  transform-origin: left;
  transition: transform var(--duration-base) var(--motion-ease-out);
}
.btn-fill:focus-visible { color: var(--primary-foreground); }
.btn-fill:focus-visible::before { transform: scaleX(1); }
@media (hover: hover) {
  .btn-fill:hover { color: var(--primary-foreground); }
  .btn-fill:hover::before { transform: scaleX(1); }
}
```

Use one button hover style per project section: do not mix fill and color-shift buttons side by side.

### 9.3 Cards

Interactive cards show a brand-colored border on hover. Non-interactive cards have no hover effect.

```css
.card { border: 1px solid var(--border); background: var(--card); border-radius: var(--radius-lg, 0.5rem); }
.card-interactive { transition: border-color var(--duration-fast) var(--motion-ease-out); }
.card-interactive:focus-within { border-color: var(--primary); }
@media (hover: hover) {
  .card-interactive:hover { border-color: var(--primary); }
}
```

- The border width never changes on hover (no layout shift).
- No lift, scale or shadow on card hover by default.
- Make the whole card clickable with a stretched link (`::after` on the main link covering the card), not a `div` with `onclick`.

### 9.3a Content swaps (tabs, switchers, carousels)

- The container (card, panel, frame) **MUST** stay still. Only the content inside it changes.
- Animate the incoming content only: `opacity` from about 0.4 to 1 over `--duration-base`. No slide, no fade of the container, no fade from 0 (it reads as a blink).
- Keep the container height stable across variants where possible, so the layout below does not jump.

### 9.4 Smooth scroll

```css
@media (prefers-reduced-motion: no-preference) {
  html { scroll-behavior: smooth; }
}
:target, [id] { scroll-margin-top: calc(var(--header-height, 4rem) + 1rem); }
```

### 9.5 Scroll reveal (marketing and content pages only)

Content below the fold fades in once as it enters the viewport.

```css
.js [data-reveal] {
  opacity: 0;
  transform: translateY(12px);
  transition: opacity var(--duration-reveal) var(--motion-ease-out),
              transform var(--duration-reveal) var(--motion-ease-out);
  transition-delay: calc(var(--reveal-index, 0) * 60ms);
}
.js [data-reveal].is-visible { opacity: 1; transform: none; }
```

```js
const io = new IntersectionObserver((entries) => {
  for (const e of entries) {
    if (e.isIntersecting) { e.target.classList.add("is-visible"); io.unobserve(e.target); }
  }
}, { rootMargin: "0px 0px -10% 0px", threshold: 0.1 });
document.querySelectorAll("[data-reveal]").forEach((el) => io.observe(el));
```

Rules:

- Runs once per element. No re-hiding on scroll up.
- Apply to section blocks, not every paragraph. Stagger at most 4 siblings (`--reveal-index: 0..3`).
- **NEVER** on above-the-fold content, the LCP element, navigation, forms, or app/dashboard screens.
- Content is visible without JavaScript (the `.js` class is set by the head script).

### 9.6 Reduced motion

```css
@media (prefers-reduced-motion: reduce) {
  *, *::before, *::after {
    animation-duration: 0.01ms !important;
    animation-iteration-count: 1 !important;
    transition-duration: 0.01ms !important;
    scroll-behavior: auto !important;
  }
  .js [data-reveal] { opacity: 1; transform: none; }
}
```

---

## 10. Components

### 10.1 Buttons

| Variant | Look | Use |
|---|---|---|
| Primary | `primary` fill, `primary-foreground` text | Main action of a view or form |
| Highlight | `highlight` fill, `highlight-foreground` text | The single top CTA on a screen (mostly marketing) |
| Secondary | `card` fill, `input` border, `foreground` text | Cancel, back, alternative actions |
| Ghost | transparent, `foreground` text, `accent` on hover | Toolbars, tertiary actions |
| Destructive | `destructive` fill, `destructive-foreground` text | Delete, revoke, irreversible actions |
| Link | `primary` text, underline on hover | Inline navigation |

- **Cancel is Secondary or Ghost. NEVER red.** Red is only for the destructive action itself.
- Sizes: sm 32px, md 40px (default), lg 48px height. Touch targets on mobile **MUST** be at least 44px (use md or larger, or extend the hit area).
- Labels are verbs in sentence case: "Simpan perubahan", "Create project". Not "OK", not "Submit".
- Destructive confirmations name the object: "Hapus 3 file", not "Ya".
- Loading: show a spinner in place of the leading icon, keep the button width, set `disabled` and `aria-busy="true"`.
- Disabled: 50% opacity, `cursor: not-allowed`, no hover effect. Prefer explaining why an action is unavailable over silently disabling it.
- Button order in dialogs and forms: secondary first, primary last (primary on the right on desktop; full-width stacked on mobile with primary on top).
- Icon + label: icon 16px for sm, 20px for md/lg, 8px gap.

### 10.2 Forms

- Label always visible above the field. **NEVER** use placeholder as the label; placeholder only shows an example format.
- Field height 40px, `rounded-md`, `input` border, `card` background.
- Focus: `outline: 2px solid var(--ring); outline-offset: 2px;`.
- Helper text below the field in `muted-foreground`, `text-sm`.
- Errors: below the field, `destructive` color with an icon, field border switches to `destructive`, `aria-invalid="true"`, linked via `aria-describedby`. Message says how to fix it: "Email harus berisi @", not "Invalid input".
- Validate on blur; after the first error, validate on input. On submit, focus the first invalid field.
- Mark optional fields "(opsional)" rather than marking every required field, unless most fields are optional.
- Group related fields with `fieldset` and `legend`. One column by default.
- Use the right input `type`, `autocomplete` and `inputmode`.

### 10.3 Cards

- Padding 16px (dense) or 24px (default), `rounded-lg`, `border`, `card` background, no shadow.
- One clear title, supporting text in `muted-foreground`, at most one primary action.
- Hover behavior: section 9.3.

### 10.4 Navigation

- Header: logo left, primary nav, actions right. Solid background with bottom border; **NEVER** a glass/blur header.
- Active item: `primary` text plus an indicator (underline or left bar), not color alone.
- Mobile: menu in a sheet/drawer, focus trapped, closable with Esc.
- App layouts: sidebar 240 to 280px, collapsible; breadcrumbs for depth greater than 2.
- Theme toggle lives in the header (section 4).
- Footer: same background as the page with one top `border`; link columns in `muted-foreground`; the copyright line is centered at the bottom with spacing above it, **no divider line** between the columns and the copyright. No theme control, no color block.

### 10.5 Overlays

- Modal: `rounded-xl`, `elevation-3`, `overlay` backdrop, max width 32rem for confirmations, focus trap, Esc and backdrop click close (except during unsaved destructive flows), focus returns to the trigger.
- Do not put long forms in modals on mobile; use a page or a full-height sheet.
- Dropdown/popover: `rounded-lg`, `popover` background, `border`, `elevation-2`.
- Tooltip: short text only, appears after 300ms delay, never holds essential information.

### 10.6 Feedback

- Toast: bottom-right on desktop, bottom-center on mobile, `elevation-2`, at most 3 stacked, `aria-live="polite"`. Success/info auto-dismiss after 5s; errors stay until dismissed.
- Inline alert: soft status background, `foreground` text, status-colored icon and left border.
- Badges: soft background with `foreground` text, or solid status color for counts. `rounded-sm` or `rounded-md`.

### 10.7 Data display

- Tables: header in `muted` background, `text-sm`, `font-medium`; text left-aligned, numbers right-aligned with `tabular-nums`; row hover `accent`; sticky header for long tables; horizontal scroll or card list on mobile.
- Long lists: pagination by default. Infinite scroll only for feeds, always with a "Muat lebih banyak" / "Load more" fallback.
- Numbers, currency and dates use `Intl` with the project locale (default `id-ID`): `Rp150.000`, `16 Nov 2026`, `1.234,5`.

### 10.8 States

Every data view **MUST** define these states:

| State | Pattern |
|---|---|
| Loading (> 300ms) | Skeleton shaped like the final layout. Spinners only inside buttons or small inline areas. |
| Empty | Icon (from the icon set), one-line title, one sentence of context, one primary action. No cartoon illustration by default. |
| Error | What happened plus what to do next, with a retry action. Never raw error codes or stack traces. |
| Partial / offline | Show cached content with a clear notice. |
| Success | Confirm with a toast or inline message; do not navigate away silently. |

---

## 11. Content and microcopy

- Language follows the project (`brand.md`), default Bahasa Indonesia for Indonesian audiences. Do not mix languages in one interface.
- Sentence case everywhere. Short, active sentences. Address the user directly.
- Be specific: "3 tugas jatuh tempo minggu ini" beats "Tetap produktif".
- Avoid exclamation marks except for genuine celebration moments.
- **NEVER** use filler or hype phrases: "Unlock", "Elevate", "Seamless", "Revolutionize", "Empower", "Supercharge", "Next-level", "Cutting-edge", "Game-changer", "Welcome to [App]", "Solusi terbaik untuk semua kebutuhan Anda", "Tingkatkan produktivitas ke level berikutnya".
- **NEVER** ship lorem ipsum, invented testimonials, fake customer logos, or made-up statistics. Use clearly marked placeholders (`[Testimoni dari pengguna nyata]`) until real content exists.

---

## 12. Accessibility (WCAG 2.2 AA)

- Contrast: 4.5:1 for normal text, 3:1 for large text (24px, or 18.66px bold) and for UI component boundaries and icons.
- Focus: visible on every interactive element using `outline: 2px solid var(--ring); outline-offset: 2px`. **NEVER** `outline: none` without a replacement.
- Semantic HTML first (`button`, `a`, `nav`, `main`, `header`, `label`). ARIA only to fill gaps.
- Everything works with a keyboard; logical tab order; a "Lewati ke konten" / "Skip to content" link.
- Target size at least 24x24px (AA), 44x44px for primary touch targets.
- `lang` attribute on `<html>`; page `<title>` per route.
- Status changes announced with `aria-live`.
- Color is never the only indicator (errors, active states, chart series).

---

## 13. Performance and loading

- Targets: LCP under 2.5s, CLS under 0.1, INP under 200ms.
- Images below the fold: `loading="lazy"` and `decoding="async"`. The LCP image: no lazy loading, `fetchpriority="high"`.
- Iframes and embeds: `loading="lazy"`, or a click-to-load facade for heavy embeds (maps, videos).
- Heavy components (charts, editors, maps) load on demand (dynamic import) when they approach the viewport.
- Fonts: self-hosted, subset, `font-display: swap`, preload one file.
- Reserve space for async content (skeletons with fixed dimensions) to avoid layout shift.

---

## 14. Anti-AI-slop rules

**NEVER** ship any of these unless `brand.md` explicitly asks for it:

Visual
- Neon colors, glow effects, colored `box-shadow` or `text-shadow` halos.
- Gradient text, purple/violet/blue-pink gradients, animated gradients, mesh/aurora/blob backgrounds.
- Glassmorphism (`backdrop-blur` cards or headers), frosted panels.
- Heavy or stacked shadows, shadows on every card, hover lifts on every element.
- Everything rounded (`rounded-2xl`+), pill buttons everywhere.
- Random colors outside the tokens, rainbow icon sets.
- Floating 3D shapes, grain/noise overlays used as decoration, parallax, custom cursors, cursor trails.
- Emoji or Unicode symbols as icons.

Layout
- Three identical feature cards with an icon in a colored circle as the default answer for "features".
- Bento grids, marquee logo walls, and "hero, features, testimonials, pricing, CTA" stacks used as a template rather than because the content needs them.
- Everything centered.
- Decorative sections with no information.

Content
- Hype copy (section 11), fake numbers, fake testimonials, fake logos, lorem ipsum.
- Animated counters for statistics, typewriter hero text.
- Generic AI imagery (sparkles, brains, robots) for non-AI products.

Prefer instead: strong typography, generous whitespace, real product screenshots, clear hierarchy, one accent used with intent, layouts shaped by the actual content.

### 14.1 Expressive moments (landing and marketing pages)

Avoiding slop is not enough: a page built only from safe defaults looks like every template. Every landing page **MUST** have:

1. **At least one solid brand color block**: a full-width section on `brand-surface` (usually the hero) or `stage` (a product demo), and optionally a CTA band on `highlight` with `highlight-foreground` text and a `brand-surface` button. Blocks are flat: no gradients, no patterns.
2. **At least one interactive product demonstration**: something the visitor can operate that shows what the product does (a switcher that swaps a real-looking UI, a calculator, a live preview, a simulated notification feed). Prefer CSS-only (`:has()` with radio inputs) or small vanilla scripts that work without hydration.
3. **One typographic statement**: a display headline with one phrase in `highlight`, or big numbers (section 5.2).

Flat emphasis techniques (use instead of shadows):

- **Offset color block**: a solid `highlight` (or `primary`) rectangle placed 16px down-right behind a featured card, same radius as the card.
- **Color blocking between sections** instead of borders: alternate `background`, `card`, `brand-surface` and `stage`. Never two identical color blocks next to each other.
- **Accent bar**: a short `h-1 w-12` `highlight` bar under a big number or heading.

Limits: at most two `brand-surface`/`stage` blocks plus one `highlight` band per page; app and dashboard screens use none of them.

---

## 15. Tailwind CSS v4 setup

Put tokens (sections 3, 5.1, 6.4, 9.1) in the global stylesheet, then map them:

```css
@import "tailwindcss";

@custom-variant dark (&:where([data-theme="dark"], [data-theme="dark"] *));

@theme inline {
  --color-background: var(--background);
  --color-foreground: var(--foreground);
  --color-card: var(--card);
  --color-card-foreground: var(--card-foreground);
  --color-popover: var(--popover);
  --color-popover-foreground: var(--popover-foreground);
  --color-muted: var(--muted);
  --color-muted-foreground: var(--muted-foreground);
  --color-border: var(--border);
  --color-input: var(--input);
  --color-ring: var(--ring);

  --color-primary: var(--primary);
  --color-primary-hover: var(--primary-hover);
  --color-primary-foreground: var(--primary-foreground);
  --color-primary-soft: var(--primary-soft);

  --color-highlight: var(--highlight);
  --color-highlight-hover: var(--highlight-hover);
  --color-highlight-foreground: var(--highlight-foreground);
  --color-highlight-soft: var(--highlight-soft);
  --color-highlight-text: var(--highlight-text);

  --color-secondary: var(--secondary);
  --color-secondary-foreground: var(--secondary-foreground);
  --color-accent: var(--accent);
  --color-accent-foreground: var(--accent-foreground);

  --color-success: var(--success);
  --color-success-foreground: var(--success-foreground);
  --color-success-soft: var(--success-soft);
  --color-warning: var(--warning);
  --color-warning-foreground: var(--warning-foreground);
  --color-warning-soft: var(--warning-soft);
  --color-destructive: var(--destructive);
  --color-destructive-foreground: var(--destructive-foreground);
  --color-destructive-soft: var(--destructive-soft);
  --color-info: var(--info);
  --color-info-foreground: var(--info-foreground);
  --color-info-soft: var(--info-soft);

  --color-brand: var(--brand-surface);
  --color-brand-foreground: var(--brand-surface-foreground);
  --color-brand-muted: var(--brand-surface-muted);
  --color-brand-raised: var(--brand-surface-raised);
  --color-brand-border: var(--brand-surface-border);
  --color-stage: var(--stage);
  --color-stage-foreground: var(--stage-foreground);
  --color-stage-muted: var(--stage-muted);
  --color-stage-border: var(--stage-border);

  --font-sans: var(--brand-font-sans);
  --font-mono: var(--brand-font-mono);
  --font-display: var(--brand-font-display);

  --shadow-1: var(--elevation-1);
  --shadow-2: var(--elevation-2);
  --shadow-3: var(--elevation-3);

  --ease-out: var(--motion-ease-out);
  --ease-in-out: var(--motion-ease-in-out);
}

@layer base {
  html { background: var(--background); color: var(--foreground); font-family: var(--font-sans); -webkit-font-smoothing: antialiased; }
  :focus-visible { outline: 2px solid var(--ring); outline-offset: 2px; }
  ::selection { background: var(--primary-soft); color: var(--foreground); }
}
```

Usage examples: `bg-brand text-brand-foreground`, `bg-stage text-stage-foreground`, `bg-background text-foreground`, `bg-primary text-primary-foreground hover:bg-primary-hover`, `bg-highlight text-highlight-foreground`, `text-highlight-text`, `border-border`, `border-input`, `shadow-2`.

- Use the Tailwind default radius scale (`rounded-sm/md/lg/xl`) and the default type scale.
- Remove Tailwind's default palette usage (`bg-blue-500`, `text-gray-600`) from components; only token classes are allowed.
- Without Tailwind, the same CSS variables are used directly.

---

## 16. Pre-ship checklist

- [ ] No hardcoded colors in components; only tokens.
- [ ] Light and dark both checked on every changed screen; no flash on load.
- [ ] Contrast passes AA in both themes (recheck if `brand.md` changed the palette).
- [ ] Type uses the scale; body 16px on content pages; numbers use `tabular-nums`.
- [ ] Spacing, radius, shadow, z-index values come from the scales.
- [ ] One icon library; no emoji or Unicode symbols as icons; icon-only buttons have labels.
- [ ] Logo and favicon set present (section 8.3); owner assets untouched.
- [ ] Hover, focus-visible, active, disabled, loading states on every interactive element.
- [ ] Interactive cards: border hover, no layout shift.
- [ ] Reduced motion respected; scroll reveal not on above-the-fold or app screens.
- [ ] Empty, loading, error states exist for every data view.
- [ ] Cancel buttons are neutral; destructive actions are red and specific.
- [ ] Forms: visible labels, inline errors with fixes, correct input types.
- [ ] Images sized, lazy below the fold, modern formats.
- [ ] Nothing from section 14 shipped.
- [ ] Landing pages: a brand color block, an interactive product demo, and a typographic statement (section 14.1).
- [ ] Switchers and tabs swap content only; the container never blinks.
- [ ] Theme toggle is a single header button; footer uses the page background with a centered copyright line.
- [ ] Money and statistics use sans `tabular-nums`, not mono.
- [ ] Copy is specific, consistent in language, free of hype and placeholders.
