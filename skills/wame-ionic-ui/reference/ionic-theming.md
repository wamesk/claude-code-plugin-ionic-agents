# Ionic theming

## Contents

- Mode decision
- Brand colours
- Background, text and step colours
- Dark mode and high contrast
- Typography and Dynamic Type
- Radius and elevation tokens
- Spacing (8 pt)
- Safe areas and system bars

## Mode decision

Ionic has two modes, `ios` and `md`. By default iPhone and iPad get `ios` and everything else
gets `md`. The `<html>` element gets the matching class (`.ios` / `.md`). Set the mode once in the
global config (the call is in the framework file). The config is read at boot and is not reactive.

| App type | Decision |
|---|---|
| Brand-led product app (consumer, own visual identity, one design file) | Set **one mode for both platforms** and theme it. Do not mix modes per component. |
| Utility or enterprise app that should feel native on each platform (field tools, internal apps) | Stay **platform-adaptive**. Theme only the shared tokens and review both modes. |

When you pick one mode, know what each mode gives you:

- **iOS mode only.** Collapsible large titles (`ion-header collapse="condense"`), the header
  `collapse="fade"` effect, card modals (`presentingElement`), and `translucent` headers and tab
  bars.
- **md mode only.** `fill="outline" | "solid"` on `ion-input` (in iOS mode, style the field
  yourself), and the ripple (turn it off with `rippleEffect: false` if the brand is not
  Material).

## Brand colours

Every colour needs six variables. Ionic's own shade is the base mixed with 12 % black, and its
tint is the base mixed with 10 % white.

```css
:root {
  --ion-color-primary: #1f6f5c;
  --ion-color-primary-rgb: 31, 111, 92;
  --ion-color-primary-contrast: #ffffff;
  --ion-color-primary-contrast-rgb: 255, 255, 255;
  --ion-color-primary-shade: #1b6251;
  --ion-color-primary-tint: #357d6c;
}
```

- **Custom colour names.** Add the same six variables plus a `.ion-color-{name}` class that maps
  `--ion-color-base`, `-base-rgb`, `-contrast`, `-contrast-rgb`, `-shade` and `-tint`. Only then
  does `color="{name}"` work.
- **Contrast.** `-contrast` must reach 4.5:1 against the base for text on buttons and badges.
  Check `medium` text on your backgrounds too.
- **Unused named colours.** Override every named colour the UI shows (`primary`, `success`,
  `warning`, `danger`, `medium`, …). One leftover default is enough to read as stock Ionic.
- **Colour is never the only state signal.** Pair it with an icon, text or shape.

## Background, text and step colours

| Variable | Use |
|---|---|
| `--ion-background-color`, `--ion-background-color-rgb` | App background |
| `--ion-text-color`, `--ion-text-color-rgb` | Default text |
| `--ion-background-color-step-50` … `-950` (Ionic 8) | Surfaces, separators, grouped backgrounds |
| `--ion-text-color-step-50` … `-950` (Ionic 8) | Secondary and tertiary text |
| `--ion-color-step-*` (Ionic 7 only) | Both of the above. Migrating to 8: background stays the same number, text number = 1000 − old number |
| `--ion-toolbar-background`, `--ion-toolbar-color`, `--ion-toolbar-border-color` | Headers and footers |
| `--ion-tab-bar-background`, `--ion-tab-bar-color`, `--ion-tab-bar-color-selected`, `--ion-tab-bar-border-color` | Tab bar |
| `--ion-item-background`, `--ion-item-border-color`, `--ion-card-background` | Rows and cards |
| `--ion-border-color`, `--ion-placeholder-color`, `--ion-backdrop-opacity` | Misc |

Set the `-rgb` variants too. Ionic uses them for alpha. For a modern grouped look, use a slightly
tinted page background (a low step), white or near-white rows and cards on top, and a toolbar
that matches the page background. Remove the header hairline with `class="ion-no-border"` on
`ion-header` when the content separates itself.

## Dark mode and high contrast

Ionic 8 ships palettes as CSS files. Import exactly one dark variant, from your framework's
package:

```
@ionic/{vue|react|angular}/css/palettes/dark.system.css   // follows the OS
@ionic/{vue|react|angular}/css/palettes/dark.class.css    // .ion-palette-dark on <html>
@ionic/{vue|react|angular}/css/palettes/dark.always.css   // always dark
```

- **Class toggle.** `.ion-palette-dark` must sit on the `html` element.
- **Gotcha: the dark palette replaces your brand.** It redefines all nine named colours (its
  `primary` is `#4d8dff`) and, per mode, the backgrounds. Its selectors are `:root` for colours,
  and `:root.ios` / `:root.md` for backgrounds, items, toolbars and cards. That means:
  - Import your theme file **after** the palette.
  - Put the dark brand values in the same scope as the palette: inside
    `@media (prefers-color-scheme: dark)` for `dark.system.css`, or under `.ion-palette-dark` for
    `dark.class.css`.
  - Use selectors at least as specific as the palette's for background and surface variables
    (`:root.ios, :root.md` / `.ion-palette-dark.ios, .ion-palette-dark.md`). A plain `:root` loses.
- **Dark surfaces.** Express elevation as a lighter surface (a higher background step), not as a
  shadow. Desaturate the brand colour a little and recheck contrast.
- **System UI.** Add `<meta name="color-scheme" content="light dark" />` so scrollbars and system
  UI follow the theme. It does not affect the keyboard. In Capacitor, set the `@capacitor/keyboard`
  `style` (iOS only) to match.
- **Ionic 7.** There are no palette files. Copy the dark variables into your own media query or
  class.
- **High contrast (Ionic 8).** Use `high-contrast.{always|class|system}.css` and
  `high-contrast-dark.{always|class|system}.css`. The class is `.ion-palette-high-contrast`, and
  the system variant follows `prefers-contrast: more`. Offer it when the brief has accessibility
  requirements beyond AA.

## Typography and Dynamic Type

- **Font.** Set `--ion-font-family` on `:root`. By default Ionic uses the system stack in iOS
  mode and Roboto in md mode. Bundle brand fonts with the app (no runtime CDN, because the app
  must work offline) and keep a system fallback.
- **Dynamic font scaling.** It is on by default in Ionic 8 (opt-in from 7.5). It needs the
  standard `core.css` and `typography.css` imports. On iOS the root font follows the Dynamic Type
  body style, which is 17 px rather than 16 px. The Android WebView scales text by itself. Chrome
  for Android needs "Accessibility Page Zoom".
- **Rules that keep scaling working:**
  - Write every custom `font-size` in `rem`, not `px` or `em`.
  - Never set a font size on `html`.
  - Do not override Ionic component font sizes in `px`.
  - Do not opt out with `--ion-dynamic-font: initial`.
- **Layout at large sizes.** Let labels wrap (`ion-text-wrap` on `ion-label`), stack side-by-side
  values vertically, and never truncate the information the screen exists for.
- **Scale.** Keep it short, for example 0.8125 / 0.9375 / 1 / 1.25 / 1.75 / 2.125 rem. Build
  hierarchy from size and weight, with at most two text colours plus one accent.

## Radius and elevation tokens

Ionic has no global radius or elevation variables. Define app tokens and map them into the
components' own custom properties. `ion-card` only exposes `--background` and `--color`, so
style its host directly. Keep radii in `px` so they do not grow with the text size.

```css
:root {
  --app-radius-sm: 8px;
  --app-radius-md: 12px;
  --app-radius-lg: 20px;
  --app-elevation-1: 0 1px 2px rgb(0 0 0 / 0.06), 0 2px 8px rgb(0 0 0 / 0.06);
}
ion-button    { --border-radius: var(--app-radius-md); --box-shadow: none; }
ion-searchbar { --border-radius: var(--app-radius-md); --box-shadow: none; }
ion-toast     { --border-radius: var(--app-radius-md); }
ion-modal     { --border-radius: var(--app-radius-lg); }
ion-card      { border-radius: var(--app-radius-lg); box-shadow: var(--app-elevation-1); }
```

Use one elevation level for raised content and none for anything else. Mixing shadow depths reads
as a template.

## Spacing (8 pt)

- **Scale.** Use an 8 pt grid with a 4 pt half step: 4 / 8 / 16 / 24 / 32 / 48. Define it as
  `--app-space-*` tokens.
- **Ionic utilities.** `--ion-padding` and `--ion-margin` (default 16 px) drive the
  `ion-padding*` and `ion-margin*` utility classes. Keep 16 px, or raise them to 20–24 px for an
  airier brand. Change the variable rather than hard-coding padding per page.
- **Rhythm.** Inside a group use 8–12. Between groups use 24–32. Page edge is 16–20.
- **Direction.** Use logical properties (`padding-inline`, `margin-block`) so RTL keeps working,
  as Ionic's own start/end slots do.

## Safe areas and system bars

- **Viewport meta.** Keep `viewport-fit=cover` in the viewport meta tag. The starters have it.
- **Ionic components.** The header, toolbar, tab bar, footer, `ion-content fullscreen` and
  overlays already pad with `--ion-safe-area-top/right/bottom/left`. Your own fixed or absolute
  elements must add them, for example
  `padding-bottom: calc(var(--ion-safe-area-bottom) + 16px)`.
- **Android edge-to-edge (Capacitor 8).** Edge-to-edge is enforced from Android 15, and the
  opt-out is gone on Android 16. On Android 15+, `StatusBar.setOverlaysWebView()` and
  `StatusBar.setBackgroundColor()` do nothing. Use the core `SystemBars` plugin
  (`import { SystemBars } from '@capacitor/core'`, since 8.0). For WebViews below 140 it injects
  `--safe-area-inset-*`. Ionic ≥ 8.7.15 reads those automatically. On an older Ionic, map them
  yourself:
  `html { --ion-safe-area-top: var(--safe-area-inset-top, env(safe-area-inset-top)); … }`.
- **Status bar text** must contrast with the header background in both themes. Use
  `@capacitor/status-bar`: `StatusBar.setStyle({ style: Style.Dark })` gives light text for dark
  backgrounds, `Style.Light` gives dark text for light backgrounds, and `Style.Default` follows
  the device. Call it whenever the theme changes.
