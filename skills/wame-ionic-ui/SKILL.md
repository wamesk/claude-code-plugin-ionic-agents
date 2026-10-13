---
name: wame-ionic-ui
description: "Designs and reviews Ionic mobile app screens with a modern, deliberate look instead of the stock Ionic style. Use for 'design an Ionic screen', 'review an Ionic screen', 'make this Ionic app look less default', 'Ionic téma alebo dark mode', 'navrhni obrazovku v Ionicu', 'skontroluj Ionic obrazovku', 'moderný dizajn Ionic aplikácie'."
---

# WAME Ionic UI

Ionic-specific layer for designing and reviewing mobile screens. Order: detect the framework
first (Step 1), then load `design-agents:wame-ui-ux` when it is installed, for the general method
(heuristics, WCAG 2.2 AA, states, forms, the AI-look list). This skill adds the Ionic specifics
only. If that skill is not installed, still hold the screen to WCAG 2.2 AA and the checklist below.

## Step 1: Detect the framework (always first)

Work the framework out from the project. Never assume it, and do not ask while the evidence is
enough.

1. **Find the Ionic app root.** It is the folder with `ionic.config.json`, `capacitor.config.*`
   or an `@ionic/*` dependency in its `package.json`. Search the whole repo: the app may be a
   subfolder such as `mobile/` inside a Laravel repo.
2. **Read the signals inside that root only**, in this order. The first valid signal wins:
   1. `package.json` has the dependency `@ionic/vue`, `@ionic/react` or `@ionic/angular`.
   2. `type` in `ionic.config.json` is `vue`, `react` or `angular`.
   3. Other traces: `vue` plus `*.vue` files ⇒ Vue; `react` plus `*.tsx`/`*.jsx` ⇒ React;
      `angular.json` plus `@angular/core` ⇒ Angular.
3. **Ask once, and only when needed.** Ask when there are 0 or 2+ candidate roots, or the root's
   signals disagree, and only if the task writes code. For a design-only task, continue
   framework-agnostic, say so, and read no framework file.

Then read **only** that framework's reference file (Vue → `reference/ionic-vue.md`,
React → `reference/ionic-react.md`, Angular → `reference/ionic-angular.md`).

Also read the Ionic major from the `@ionic/*` version (the lockfile has the exact installed
version) and the Capacitor major from `@capacitor/core`. Pick APIs for that major only.

Before designing, read the existing theme (`theme/variables.*` and the `mode` passed at setup)
and two existing pages. Change the global `mode` and palette only in a redesign, never for a
one-screen task.

## Step 2: Version gates

The default docs at ionicframework.com/docs are now v9; v8 lives under `/docs/v8/`. Do not use a
feature the installed version lacks. Name the fallback instead.

| Feature | Since |
|---|---|
| `--ion-background-color-step-*` / `--ion-text-color-step-*` (v7: `--ion-color-step-*` only) | 8.0 |
| Dark and high-contrast palette files `css/palettes/*.css` | 8.0 |
| Dynamic font scaling on by default (opt-in since 7.5) | 8.0 |
| Legacy form syntax removed, `label` prop required on inputs | 8.0 |
| `focusManagerPriority` (experimental) | 8.1 |
| `ion-segment-view` / `ion-segment-content`, `ion-select interface="modal"` | 8.4 |
| Modal `expandToScroll`, `ion-select` `helperText` / `errorText` | 8.5 |
| `ion-input-otp` | 8.6 |
| Capacitor `--safe-area-inset-*` read into `--ion-safe-area-*` automatically | 8.7.15 |

Ionic 9 breaks code written for 8. Angular standalone imports move from `@ionic/angular/standalone`
to `@ionic/angular`. React needs React Router 6.4+. Vue needs Vue Router 5 and Vue 3.5. `ion-img`
is deprecated (use `<img>`). The legacy picker is gone. Sheet modal `handleBehavior` now defaults
to `"cycle"`.

## Design principles for a modern look

- **Choose the mode on purpose.** A brand app gets one mode on both platforms plus a custom theme.
  A utility app stays platform-adaptive. See `reference/ionic-theming.md`.
- **Own the palette.** Set the brand colours with all six variables each, in both the light and
  the dark scope. The dark palette redefines every named colour, so a light-only override brings
  Ionic blue back in dark mode.
- **Get hierarchy from type and space, not colour.** Use one type family with a clear scale in
  `rem`, and weights 400, 600 and 700. Keep generous section spacing on an 8 pt grid.
- **Use quiet surfaces.** Grouped background from the step colours, raised rows or cards on it,
  one elevation level, and one radius scale shared by buttons, inputs, cards and modals.
- **Keep content first.** Use a large title (iOS mode) or a plain toolbar. Give each screen one
  primary action, placed in thumb reach.
- **Give native feedback.** Skeletons while loading, pull-to-refresh where data is live, haptics
  only on meaningful events, and Ionic's own transitions with reduced motion honoured.

## Decision rules

- **Navigation.** Tabs for 3–5 top-level peers, each with an icon and a label. A side menu only
  for secondary destinations. Never route from one tab into another tab. Give a shared view a
  route in each tab's stack. Account or settings reached from several tabs opens as a modal.
- **Overlays.** A toast confirms something passive. An alert is only for a decision that has to
  block. An action sheet chooses among the actions on one object. A sheet modal is for a
  contextual task that keeps the page visible. A full modal is for a multi-step task.
- **Lists.** A swipe action (`ion-item-sliding`) is a shortcut only. The same action must also
  be reachable without swiping. For lists of hundreds of rows, use a framework virtual list.
- **States.** Every data screen designs loading, empty, error and offline states. Never a blank
  screen.
- **Forms.** One column. A visible `label` on every control (a placeholder is not a label). The
  right `type`, `inputmode`, `autocomplete` and `enterkeyhint`. Errors shown inline after the
  field is touched.

Details and verified API shapes are in `reference/ionic-patterns.md`.

## Generic / AI look on mobile

Avoid these. They signal "generated", not "designed":

- Default Ionic blue (`#0054e9` in v8, `#3880ff` before) and the untouched stock `md`/`ios` look
- A floating, blurred glass tab bar with a giant FAB on top of it
- Gradient headers or toolbars
- Emoji used as icons
- A row of KPI tiles as the first thing on the home screen
- Cards with coloured left borders
- A fade-in or slide-in animation on every element

For the full list, see `design-agents:wame-ui-ux` → `reference/ai-design-tells.md` (when
installed).

## Pre-finish checklist

- [ ] The app root, framework and Ionic major were detected (or a design-only task said it stayed
      framework-agnostic). Every API used exists in that major.
- [ ] The mode was chosen explicitly and is used the same way across the app.
- [ ] Brand colours are defined in the light and the dark scope. Text contrast is ≥ 4.5:1 and UI
      contrast ≥ 3:1, including medium-coloured text on step backgrounds.
- [ ] Every routed view has a page root (`IonPage` in Vue/React). Data refreshes in the
      view-enter hook, not the mount hook.
- [ ] Custom fixed elements add `--ion-safe-area-*`. The status bar style follows the header
      background in light and dark mode.
- [ ] Custom font sizes are in `rem`. The screen still works at the largest text size.
- [ ] Loading, empty, error and offline states exist for every data screen.
- [ ] Icon-only buttons have an `aria-label`. Decorative `ion-icon`s are `aria-hidden="true"`.
      The primary `ion-title` has `role="heading"` and `aria-level="1"`.
- [ ] Touch targets are ≥ 44 × 44 pt on iOS and ≥ 48 × 48 dp on Android.
- [ ] Inline overlays reset `isOpen` on `didDismiss`. Modals and action sheets are labelled.
- [ ] Reduced motion is honoured. None of the generic tells above remain.

## Reference map

| File | Read when |
|---|---|
| `reference/ionic-theming.md` | Mode, colours, dark mode, fonts and Dynamic Type, radius, elevation, spacing, safe areas, status bar |
| `reference/ionic-patterns.md` | Navigation, modals, overlays, lists, states, segment, search, forms, keyboard, haptics, motion, accessibility |
| `reference/ionic-vue.md` | Vue was detected |
| `reference/ionic-react.md` | React was detected |
| `reference/ionic-angular.md` | Angular was detected |
