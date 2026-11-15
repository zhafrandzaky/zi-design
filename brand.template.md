# brand.md

Project-specific overrides for `DESIGN.md`. Copy this file to the project root as `brand.md` and fill in only what differs from the defaults. Empty fields use the defaults in `DESIGN.md`.

## Identity

```yaml
name:          # App name, e.g. "JadwalKu"
tagline:       # One short line, used in og-image and meta description
description:   # One or two sentences: what the product does and for whom
audience:      # e.g. "Indonesian university students"
tone:          # e.g. "friendly, clear, not formal"
language:      # default: id (Bahasa Indonesia)
locale:        # default: id-ID (numbers, currency, dates)
```

## Palette

Hex values. Leave empty to keep the default.

```yaml
primary:       # default: "#000070" (Royal Blue)
highlight:     # default: "#fb5607" (Nitro Orange)
ink:           # default: "#222529" (Blackstone)
paper:         # default: "#e9ecef" (Whiteout)

# Optional status overrides, as light/dark pairs
success:       # default: ["#15803d", "#4ade80"]
warning:       # default: ["#b45309", "#fbbf24"]
danger:        # default: ["#c0262d", "#f87171"]
```

### Token adjustments

Filled in by the agent when a new palette fails contrast (DESIGN.md section 3.4). Record token, theme, old value, new value, and the resulting ratio.

```yaml
# - token: --primary (dark)
#   from: color-mix(in oklab, var(--brand-primary) 40%, white)
#   to:   color-mix(in oklab, var(--brand-primary) 30%, white)
#   ratio: 5.2
```

## Typography

```yaml
font_sans:     # default: Geist
font_mono:     # default: Geist Mono
font_display:  # default: same as font_sans (used only for marketing hero/H1)
```

## Icons

```yaml
icon_library:  # default: installed library, otherwise Lucide
icon_stroke:   # default: 2
```

## Assets

Paths relative to the project root. Leave empty to let the agent search the project or generate assets (DESIGN.md section 8).

```yaml
logo:          # full logo, SVG preferred
logo_mark:     # symbol only
logo_dark:     # optional variant for dark backgrounds
favicon:       # optional, otherwise generated from logo_mark
og_image:      # optional
```

## Motion

```yaml
scroll_reveal:      # default: true (marketing/content pages only)
button_hover_style: # default: color-shift. Options: color-shift, fill
```

## Project rules

Exceptions or additions specific to this project. Each rule that breaks a DESIGN.md rule must state why.

- 
