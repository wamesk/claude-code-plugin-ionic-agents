# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [0.1.0] - 2026-10-13

### Added

- **Skill `wame-ionic-ui`.** Modern, carefully designed Ionic mobile UI instead of the stock
  Ionic look. It has design principles, decision rules for navigation, overlays, lists, states
  and forms, a list of generic "AI look" tells to avoid on mobile, and a pre-finish checklist.
- **Framework detection.** The first valid signal wins: `@ionic/*` in `package.json`, then
  `type` in `ionic.config.json`, then other project traces. The skill asks only when the signals
  conflict or are missing, and then loads only the matching framework file (Vue, React or
  Angular).
- **Version gates.** The skill reads the installed Ionic major and picks APIs for it. A table
  lists which Ionic 8 minor added which feature, and a summary covers the Ionic 9 breaking
  changes: the Angular import path, React Router 6, Vue Router 5, `ion-img`, and the sheet handle
  default.
- **`reference/ionic-theming.md`.**
  - The mode decision (one mode for brand apps, adaptive for utility apps).
  - Brand colours with shade, tint and contrast.
  - Background, text and step colours.
  - Dark and high-contrast palettes, including the trap where the dark palette overrides brand
    colours.
  - Fonts and Dynamic Type.
  - Radius and elevation tokens.
  - 8 pt spacing.
  - Safe areas with Capacitor 8 edge-to-edge.
- **`reference/ionic-patterns.md`.**
  - Tabs, large titles and the side menu.
  - Sheet and card modals.
  - Toast vs alert vs action sheet.
  - Sliding items, refresher, infinite scroll and virtual lists.
  - Loading, empty, error and offline states.
  - Segment and searchbar.
  - Forms and the keyboard.
  - Capacitor haptics and status bar.
  - Motion and reduced motion.
  - Accessibility.
- **Framework files** `reference/ionic-vue.md`, `ionic-react.md` and `ionic-angular.md`. Each
  covers page structure, router outlet, navigation, lifecycle hooks, where the theme CSS is
  imported, overlay idioms, form validation classes, the keyboard and long lists.
- **Composes with `design-agents`.** The skill loads `design-agents:wame-ui-ux` for the general
  UI/UX method when it is installed, and adds only the Ionic specifics.
