# Design Specification
This document is the technical reference for anyone building or contributing to `cybrcore`. It maps every design decision to a concrete rule. For the *why* behind these decisions, read the [README](./readme.md). This document is the *how*.

## Color System

### Palette

11 base colors, each with a two-letter code:

| Chip | Color | Code | Hex |
|-----|-----|-----|---------|
| <img src="https://raw.githubusercontent.com/cybrcore/cybrcolors/refs/heads/main/assets/palette/re0.png" width="23"/> | Red    | `re` | `#F24848` |
| <img src="https://raw.githubusercontent.com/cybrcore/cybrcolors/refs/heads/main/assets/palette/gr0.png" width="23"/> | Green  | `gr` | `#30F291` |
| <img src="https://raw.githubusercontent.com/cybrcore/cybrcolors/refs/heads/main/assets/palette/ye0.png" width="23"/> | Yellow | `ye` | `#F2D230` |
| <img src="https://raw.githubusercontent.com/cybrcore/cybrcolors/refs/heads/main/assets/palette/bl0.png" width="23"/> | Blue   | `bl` | `#3061F2` |
| <img src="https://raw.githubusercontent.com/cybrcore/cybrcolors/refs/heads/main/assets/palette/vi0.png" width="23"/> | Violet | `vi` | `#A130F2` |
| <img src="https://raw.githubusercontent.com/cybrcore/cybrcolors/refs/heads/main/assets/palette/cy0.png" width="23"/> | Cyan   | `cy` | `#29BECC` |
| <img src="https://raw.githubusercontent.com/cybrcore/cybrcolors/refs/heads/main/assets/palette/or0.png" width="23"/> | Orange | `og` | `#F26118` |
| <img src="https://raw.githubusercontent.com/cybrcore/cybrcolors/refs/heads/main/assets/palette/pi0.png" width="23"/> | Pink   | `pi` | `#F230B2` |
| <img src="https://raw.githubusercontent.com/cybrcore/cybrcolors/refs/heads/main/assets/palette/wh0.png" width="23"/> | White  | `wh` | `#898D99` |
| <img src="https://raw.githubusercontent.com/cybrcore/cybrcolors/refs/heads/main/assets/palette/me0.png" width="23"/> | Metal  | `me` | `#4D5A80` |
| <img src="https://raw.githubusercontent.com/cybrcore/cybrcolors/refs/heads/main/assets/palette/no0.png" width="23"/> | Black  | `no` | `#030408` |

"White" is never pure #FFFFFF white, "black" is never pure #000000 black. They are named for their role, not their value.

`no` stands for "no-signal", `me` stands for "metal".

`no0` is the primary background. `no1` and `no2` exist in current implementations but are under review for replacement with dedicated transparency tokens (`tr0`, `tr1`). Contributors should avoid building dependencies on no1/no2.

### Three-tier shading
Every color except `wh` has three tiers:

- `xy0` -- full color (the base hex value)
- `xy1` -- base color mixed with `no0` (black) at 40%
- `xy2` -- base color mixed with `no0` (black) at 20%

The canonical generation method, defined in [cybrcolors](https://github.com/cybrcore/cybrcolors):

```
xy1 = color-mix(in srgb, xy0 40%, no0)
xy2 = color-mix(in srgb, xy0 20%, no0)
```

This produces concrete hex/HSL values that are the source of truth for the palette. All resulting values are published in cybrcolors.

### Implementation: `color-mix` vs hardcoded hex

How `xy1` and `xy2` are implemented depends on context:

**Use `color-mix` with transparent** when the platform supports it AND the element sits right over the wallpaper (the common case in CSS themes). This lets the wallpaper bleed through, which is the desired effect for the transparency stack:

```css
--cybr-re1: color-mix(in srgb, var(--cybr-re0) 40%, transparent);
--cybr-re2: color-mix(in srgb, var(--cybr-re0) 20%, transparent);
```

**Use hardcoded hex values from cybrcolors** when:
- The platform doesn't support `color-mix`
- The element sits on a colored surface (e.g., a button on a `re0` background -- transparent mix would not register)
- TUI/CLI apps that use ANSI or hex in config files

Both approaches produce the same perceived color when viewed against `no0`. The difference is whether the wallpaper shows through.

### Role tokens

The palette is used through an abstraction layer of configurable roles:

| Role         | Code | Default | Purpose |
|--------------|------|-------|----------------------------------------------|
| Primary fg   | `p0` | `re0` | Main UI and text color, active states |
| Primary bg   | `p2` | `re2` | Main accent background |
| Secondary fg | `s0` | `cy0` | Complementary accent, interactive elements, buttons |
| Secondary bg | `s2` | `cy2` | Complementary accent background |
| Tertiary fg  | `t0` | `gr0` | Third accent, supporting info |
| Tertiary bg  | `t2` | `gr2` | Third accent background |
| Hover fg     | `h0` | `bl` | Foreground on hover |
| Hover bg     | `h2` | `bl2` | Background on hover |
| Link         | `l` | `gr` | Link color |
| Caret        | `c` | `gr` | Text cursor color |

Default logic: red commands attention on dark backgrounds and produces no blue light. Cyan contrasts red and reads better than blue on dark. Green closes the triangle. Blue is reserved for hover because it's distinct from all three defaults.

Users should be able to remap any role to any base color. The planned `cybrswitch`, a system-wide color switcher, will propagate these changes across all themed applications.

### Semantic colors

These do not change regardless of user configuration:

| Meaning  | Color  | Foreground | Background |
|----------|--------|------------|------------|
| Danger   | Red    | `re0`      | `re2`      |
| Success  | Green  | `gr0`      | `gr2`      |
| Warning  | Yellow | `ye0`      | `ye2`      |

When `p0` is red, danger states still use `re0`/`re2`. The visual distinction relies on context (container shape, icon, position) rather than color alone.

### Text colors

| Token | Color  | Role                                          |
|-------|--------|-----------------------------------------------|
| `p0`  | varies | Primary text, main content                    |
| `wh0` | white  | Subdued text, descriptions, secondary info    |
| `me0` | metal  | Metadata, tertiary text, UI chrome            |
| `no0` | black  | Inverted text (on colored backgrounds)        |

### Token mapping across platforms

| Platform          | Method                                         |
|-------------------|------------------------------------------------|
| CSS (themes)      | Hex values → CSS custom properties (`--cybr-re0`) |
| TUI apps (kitty, fish, etc.) | ANSI color codes defined in kitty config |
| Non-CSS GUI configs | Hex values directly in config files           |

Canonical palette source: [cybrcolors](https://github.com/cybrcore/cybrcolors). When the palette updates, all themes should follow. Currently hardcoded per-theme with accepted lag. Contributors should open an issue if they notice a theme using outdated values.

### For contributors

- Always use token names, never hardcoded hex.
- New components must use existing palette tokens.
- Test contrast ratios: WCAG AA minimum (4.5:1 for text).
- If a semantic meaning is needed (error, success, warning), use the fixed colors, not the role tokens.

```css
/* correct */
.error   { color: var(--cybr-re0); background: var(--cybr-re2); }
.focused { border-color: var(--cybr-p0); }

/* wrong */
.error   { color: #ff0000; }
.focused { border-color: #F24848; }
```

## Typography
`Geist Mono Nerd Font` across all applications. No exceptions, no secondary typefaces. Optional proportional override: `Atkinson Hyperlegible Next`, user-configured, for long-form prose contexts only. The default is always monospace.

Text labels are preferred over icons for clarity and information density. Nerd Font icons are used only when space is constrained (status bars, file type glyphs, compact indicators) or when universal recognition makes them faster to parse than text. Icon usage in new contributions requires justification.

Hierarchies are established through weight, size, and color. No font mixing.

### Heading colors

Headings cycle through the role triad:

h1 = `p0`, h2 = `s0`, h3 = `t0`, h4 = `p0`, h5 = `s0`, h6 = `t0`

### For contributors

- Never introduce additional fonts.
- Justify every icon. If text fits, use text.
- Test readability at multiple DPI scales.

## Geometry

### Notch

All corners are sharp. No rounded corners anywhere. `border-radius: 0 !important` is the universal default.

Where possible, elements use notched (beveled) clip-paths: the top-left and bottom-right corners are cut at an 45° angle. This is the signature shape of `cybrcore`.

The orientation is always top-left + bottom-right for content elements. Never top-right + bottom-left within content.

**Exception**: system topbars (waybar and equivalents): the leftmost element gets a bottom-right notch, center elements get bottom-left and bottom-right notches, the rightmost element gets a bottom-left notch.

### Clip-path sizes

Four notch sizes, chosen by element scale:

| Size | Variable | Notch  | Use case                                    |
|------|----------|--------|---------------------------------------------|
| L    | `n-l`    | 15px   | Modals, large containers, overlays          |
| M    | `n-m`    | 10px   | Nav items, buttons, menus, tabs             |
| S    | `n-s`    | 5px    | Small or square buttons (icon-only)         |
| XS   | `n-xs`   | 3px    | Tags, badges, small inline elements         |

### Directional variants

- `clip-[size]` -- full notches (top-left and bottom-right)
- `clip-[size]-t` -- top-left notch only, bottom square
- `clip-[size]-b` -- bottom-right notch only, top square

Use directional variants when elements stack vertically: top element gets `-t`, bottom gets `-b`, creating a continuous notched corner across the group.

### Spacing

- Inner gaps (within components): 4px
- Outer gaps (between components): 8px

### For contributors

- `border-radius` must be 0 in every theme. No exceptions.
- Use the clip-path variables, don't write custom polygon values.
- Match notch size to element scale using the table above.
- When in doubt, use `clip-m`.

## Depth and Layering

### Transparency stack

The depth model is: wallpaper → semi-transparent surface → content. No shadows, no borders for depth. Depth comes from the transparency stack alone.

The goal across all GUI themes: obliterate any default background color and place a [cybrpapers](https://github.com/cybrcore/cybrpapers) wallpaper at the farthest layer. This mirrors how TUI apps look in kitty on Hyprland -- the terminal window with visible desktop wallpaper behind it.

When an app supports transparency:
- Surface opacity: 0.7–0.9 range (test for readability)
- Blur: 6px typical for window surfaces, higher (10–15px) for modals and overlays that need to obscure content behind them
- Grain noise where supported, to prevent flat-glass banding

When an app does not support transparency:
- Fall back to solid `no0` background.
- [cybrpapers](https://github.com/cybrcore/cybrpapers) wallpapers can be injected as a background image where the platform allows (CSS `body::before`, Stylus preprocessor variables, etc.)

### For contributors

- Always provide a non-transparent fallback.
- Test against both dark and light wallpapers.
- Modals should use heavier blur than regular surfaces.
- Never use box-shadow or drop-shadow for depth.

## Interaction States

Three universal states for interactive elements:

| State   | Foreground      | Background         |
|---------|-----------------|--------------------|
| Default | Role color (`p0`, `s0`, or `t0`) | Transparent |
| Hover   | `h0`            | `h2`               |
| Active / Selected | `no0` (inverted) | Role color (`p0`, `s0`, etc.) |

This pattern applies everywhere. Nav items, tabs, buttons, menu items, tags, list items. No exceptions.

Hover color defaults to blue (`bl0`/`bl2`) but is configurable by user through the role token `h0`/`h2`.

### Tags

Tags are a special case: `fg: no0`, `bg: p0`, `clip-xs`. On hover, they follow the standard hover state.

### For contributors

- Apply the three-state pattern to every interactive element.
- Active state always inverts: dark text on colored background.
- Never use opacity changes or underlines as the sole hover indicator.

## Animations

Minimal to none. When used:

- Duration: under 200ms
- Easing: linear or ease-out only
- No bounce, spring, or elastic effects
- Purpose: communicate state changes, never "delight"

Prefer instant feedback. If removing an animation doesn't reduce usability, remove it.

### For contributors

- If animation is necessary, keep it under 200ms with linear or ease-out.
- Disable animations in power-saving mode where the platform supports it.
- Never add animation for decorative purposes.

## Interface Patterns

### Terminal-first

TUI/CLI tools are preferred over GUI wherever practical. GUI is used only when TUI is genuinely impractical (browsers, image editors, note-taking). All GUI apps should be restyled to look and feel like they run in a terminal.

### Information density

Content over decoration. Status bars show metrics, not empty space. Launchers prioritize fast search. Terminals maximize text viewport. Padding is functional (clickability, readability), never decorative.

### Keyboard-driven

All core functions must be accessible via keybinds. Mouse is convenience, not requirement. New features must ship with keyboard shortcuts. Document keybinds in component READMEs.

### Focus mode

A documented pattern for any app with sidebars or panels: a toggle that collapses non-essential UI, with reveal-on-hover as an option. Mapped to a key combo. Implement where the platform supports it.

### Element visibility

Sidebars, titlebars, status bars, ribbons, and similar chrome are visible by default. Users can hide them via toggles. Everything is opt-in removal. No hierarchy -- users decide what they need.

### Scrollbars

Thin, color-matched to `p0`, invisible when not in use. Hideable via toggle where the platform supports it.

### For contributors

- Evaluate if a GUI app has a viable TUI alternative before theming.
- Ask: "Is this decoration or information?" before adding any element.
- Document all keybinds.
- Implement focus mode if the app has collapsible panels.

## Fluff Reduction

No window titlebars in tiling WM contexts. No decorative dividers. Inside borders communicate state (focused, unfocused). Maximize content area.

The filter: if removing an element doesn't reduce usability or information, remove it.

### For contributors

- Remove fluff before adding features.
- Titlebars are wasted vertical space in a tiling WM.
- Decorative borders between panes should communicate state of each pane.

## Wallpaper Integration

Wallpapers are provided by [cybrpapers](https://github.com/cybrcore/cybrpapers). They are named after Tokyo districts:

Samurai, Shibuya, Shinjuku, Chiyoda, Minato, Yoyogi, Roppongi, Taito, Ikebukuro, Asakusa, Harajuku, Akihabara (plus upcoming: Ueno, Meguro, Setagaya).

In Hyprland, wallpapers are managed by swww. In GUI apps, wallpapers are injected behind the transparency stack however the platform allows. The method doesn't matter. The result does: the wallpaper is visible through the semi-transparent UI surface.

## App Customization

The aspiration for every GUI theme is full user customization of the role colors -- primary, secondary, tertiary, hover, link, caret -- through the app's settings system where available (Obsidian's Style Settings, Stylus preprocessor variables, etc.).

A planned system-wide color/flavor switcher will propagate role assignments across all themed apps simultaneously. Until that ships, each theme manages its own defaults.

The in-app theme name in pickers and dropdowns is always `cybrcore`.

## Conventions

### File naming

kebab-case throughout. Lowercase, no spaces, no underscores, no camelCase.

Theme repos prepend `cybr-` before the name of the themed app: `cybr-obsidian`, `cybr-kitty`, `cybr-yazi`.

The in-app theme name is always `cybrcore`.

Core repos drop the hyphen: `cybrcore`, `cybrcolors`, `cybrpapers`, `cybrcursors`.

### Date/time format

ISO 8601 always: `YYYY-MM-DD HH:mm:ss`. 24-hour time. No localized formats.

### Versioning

Semantic: `major.minor.patch` → v1.5.7

- Major: bumped only by author on significant changes, or minor overflow past 9
- Minor: bumped by contributors when functionality is added, or patch overflow past 9
- Patch: bumped by contributors when a bug is fixed

### Color codes

| Color  | Code |
|--------|------|
| Red    | `re` |
| Green  | `gr` |
| Yellow | `ye` |
| Blue   | `bl` |
| Violet | `vi` |
| Cyan   | `cy` |
| White  | `wh` |
| Grey (metal)  | `me` |
| Orange | `og` |
| Pink   | `pi` |
| Black (no-signal)  | `no` |
| Transparent  | `tr` |

### Size codes

| Size             | Code  |
|------------------|-------|
| Extra extra large | `xxl` |
| Extra large      | `xl`  |
| Large            | `l`   |
| Medium           | `m`   |
| Base             | `b`   |
| Small            | `s`   |
| Extra small      | `xs`  |
| Extra extra small | `xxs` |

### Repo structure

```
cybr-[app]/
├── readme.md        # logo, header, showcase, install guide, credits
└── [config files]
```

### README structure for theme repos

1. Banner
2. Showcase (screenshots)
3. Installation guide
   - Prerequisites
      - Geist Mono NF
      - App itself
      - Link to install guide
      - Link to app repo and Archwiki
   - Steps
4. Credits (optional)


## Code Standards

- Inline comments explaining non-obvious choices.
- kebab-case filenames.
- ISO 8601 timestamps.
- Configs should be readable without external documentation.


## Testing Checklist

Before submitting a theme or modification:

- [ ] Readable against every `cybrpaper`
- [ ] Either works with transparency enabled and disabled, or has `cybrpaper` injected
- [ ] All keybinds documented
- [ ] Geist Mono only (no font regressions)
- [ ] Colors use tokens from `cybrcolors`, no hardcoded hex
- [ ] Animations under 200ms or disabled
- [ ] Sharp corners maintained (border-radius: 0), notches applied
- [ ] Three interaction states implemented (default, hover, active)
- [ ] Focus mode implemented (if app has collapsible sidebars and panels)
- [ ] Scrollbars thin, color-matched, invisible when idle
