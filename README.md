# zi-design

A reusable UI/UX standard for all projects: design tokens, typography, motion, components, accessibility, and anti-AI-slop rules in one file that never needs editing per project.

| File | Purpose |
|---|---|
| `DESIGN.md` | Rules and defaults. Copied into projects as-is. |
| `brand.template.md` | Template for per-project overrides (palette, fonts, assets, tone). Saved as `brand.md`. |

## Highlights

- Four brand primitives (Royal Blue, Nitro Orange, Blackstone, Whiteout); every other color is derived with `color-mix()` and `light-dark()`, so changing four values re-themes everything.
- Light and dark themes, both verified against WCAG 2.2 AA.
- Geist and Geist Mono, a fixed type scale, and tabular numerals for data.
- Tailwind CSS v4 mapping, compatible with shadcn/ui token names.
- Motion rules: color-shift and fill hovers, border hover on cards, smooth scroll, scroll reveal, reduced-motion support.
- Expressive moments for landing pages: solid brand color blocks, an interactive product demo, and bold typography, so pages stay flat without looking like a template.
- Logo and favicon pipeline: use owner assets, otherwise generate SVG and export the full icon set.
- No emoji, no neon, no glow, no glassmorphism, no hype copy.

## Usage

Copy `DESIGN.md` into the project root:

```sh
curl -fsSLO https://raw.githubusercontent.com/zhafrandzaky/zi-design/main/DESIGN.md
```

Optionally add project overrides:

```sh
curl -fsSL https://raw.githubusercontent.com/zhafrandzaky/zi-design/main/brand.template.md -o brand.md
```

Fill in only the fields that differ from the defaults.

Then point your AI agent to it, for example in `AGENTS.md` or `CLAUDE.md`:

```md
## UI/UX
Follow DESIGN.md for all UI work. Project overrides are in brand.md. Run the checklist in DESIGN.md section 16 before finishing UI tasks.
```

To update a project to the latest standard, run the first command again. `brand.md` is never overwritten.

## License

[MIT](LICENSE)
