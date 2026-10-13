# Ionic patterns

## Contents

- Navigation
- Sheet and card modals
- Toast, alert, action sheet
- Lists: sliding items, refresher, infinite scroll, long lists
- Loading, empty, error and offline states
- Segment and searchbar
- Forms and the keyboard
- Haptics (Capacitor)
- Motion and reduced motion
- Accessibility

Snippets use plain web-component markup. Bind properties and events the way your framework file
shows (`:prop` / `@event` in Vue, `prop={}` / `onEvent` in React, `[prop]` / `(event)` in Angular).

## Navigation

**Tabs.** Use `ion-tabs`, then `ion-tab-bar slot="bottom"`, then `ion-tab-button tab="…"`.

- Have 3–5 tabs, each with an icon and a short label. Icon-only tabs are not recognisable.
- Each tab keeps its own navigation stack, so never navigate from one tab into another tab.
- For a view shared between tabs (an item detail, for example), give it a route under each tab
  (`/tabs/home/item/:id`, `/tabs/search/item/:id`) and reuse the component.
- Something many tabs need (account, settings) opens as a modal.
- For full-screen flows (onboarding, checkout), route outside the tabs. Do not hide the tab bar
  with CSS.

**Large titles (iOS mode only):**

```html
<ion-header translucent="true">
  <ion-toolbar><ion-title>Inbox</ion-title></ion-toolbar>
</ion-header>
<ion-content fullscreen="true">
  <ion-header collapse="condense">
    <ion-toolbar><ion-title size="large">Inbox</ion-title></ion-toolbar>
  </ion-header>
  …
</ion-content>
```

`ion-buttons collapse="true"` shows those buttons only in the collapsed bar. In md mode use a
normal toolbar title. A large heading inside the content does not collapse.

**Side menu.** `ion-menu` with `contentId` holds secondary destinations only: account switch,
settings, help. It is never the primary navigation on phones. On tablets, use `ion-split-pane` to
keep it visible.

**Back.** Every page that can be deep-linked gets
`ion-back-button` with `defaultHref`, so the back button still works on a cold start.

## Sheet and card modals

```html
<ion-modal breakpoints="[0, 0.5, 1]" initial-breakpoint="0.5" backdrop-breakpoint="0.5">
```

(Bind `breakpoints` as an array in your framework.)

- **Breakpoints.** `initialBreakpoint` must be one of the `breakpoints`. Including `0` allows a
  swipe to dismiss. Below `backdropBreakpoint` the page behind stays interactive, which suits a
  map with a details sheet.
- **Handle.** `handle` shows the grabber. `handleBehavior="cycle"` (the default from Ionic 9)
  lets a tap on the handle step through the breakpoints, an alternative to dragging.
- **Scrolling.** `expandToScroll` set to `false` (8.5+) lets the content scroll at every
  breakpoint.
- **Initial breakpoint content.** The primary action must be visible at the initial breakpoint
  without expanding the sheet.
- **Card modal (iOS mode).** Set `presentingElement` to the page or router outlet element.
- **Unsaved changes.** Guard them with `canDismiss` as an async function that returns
  `true`/`false`.
- **Inline modals.** `isOpen` is one-way. Set your state back to `false` on `didDismiss`, or the
  modal will not open a second time.
- **Labels and stacking.** Label the modal (`aria-labelledby` pointing at its title). Do not
  stack more than one modal level.

## Toast, alert, action sheet

| Need | Use | Not |
|---|---|---|
| Confirm a finished action (saved, sent, archived) | `ion-toast`, auto-dismissing. Optionally add an Undo button, but the action must also exist elsewhere. | Alert |
| A blocking or irreversible decision | `ion-alert` with two verb buttons ("Delete draft" / "Keep"), the destructive one marked `role: 'destructive'` | Toast |
| Several actions on one object | `ion-action-sheet`, `role: 'destructive'` / `'cancel'` | Alert with 3+ buttons |
| Pick one value from many | `ion-select interface="modal"` (8.4+) or a sheet modal with a searchbar | Alert radio list |

- **Toast position.** Use `position="top"`, or keep `bottom` and set `positionAnchor` to the tab
  bar or footer so the toast does not cover them. `swipeGesture="vertical"` (7.6+) lets users
  flick it away.
- **Toast behaviour.** Toasts announce politely (`role="status"`). Never make one assertive, and
  never require a tap to dismiss it. Size `duration` to the length of the message.
- **Toasts inside modals.** Do not show a toast with buttons from inside a modal. The modal's
  focus trap keeps screen readers from reaching the buttons.
- **Labelling.** Give action sheets a `header`, or pass `htmlAttributes` with an `aria-label`.

## Lists: sliding items, refresher, infinite scroll, long lists

- **Sliding items.** Nest `ion-item-sliding` → `ion-item` + `ion-item-options side="end"` →
  `ion-item-option`. Use at most 2–3 options. Mark the primary one `expandable` for a full swipe,
  and give destructive ones `color="danger"`. Swipe is invisible and unavailable to some users,
  so offer the same actions on the detail screen or in an overflow action sheet.
- **Refresher.** Use `<ion-refresher slot="fixed">` with `<ion-refresher-content>` inside
  `ion-content`, and listen to `ionRefresh`. Call `complete()` on the refresher in a `finally`
  block, so it also runs on error. Otherwise the spinner never stops.
- **Infinite scroll.** Listen to `ionInfinite` and call `complete()` on its target after each
  page. Set `disabled` once the last page has arrived, or it keeps firing. `threshold` defaults
  to `15%`. Put `role="feed"` on the scroll container and `role="article"` on each item.
- **Long lists.** `ion-virtual-scroll` was removed in Ionic 7. Use the framework's virtual list
  (see the framework file) inside an `ion-content` with `scrollY` set to `false`, and put the
  `ion-content-scroll-host` class on the virtual scroller. That keeps the refresher, infinite
  scroll and large titles working.

## Loading, empty, error and offline states

- **Loading.** Use `ion-skeleton-text animated` shaped like the final rows (an avatar circle, two
  lines at about 60 % and 40 % width). Put `aria-busy="true"` on the container. Use the skeleton
  for the first load, the refresher for reloads, and an `ion-spinner` inside a disabled button for
  an action in progress. Use `ion-loading` only for a short wait that must block, never for a
  page load.
- **Empty.** Show an icon (`aria-hidden`), one sentence and one primary action. Tell "nothing
  here yet" apart from "no results for this filter", and offer to clear the filter in the second
  case.
- **Error.** Show it inline, in place of the content, with a Retry action and a human
  explanation. If a refresh fails, keep the stale data visible with a notice. Use a toast only for
  failures in background work.
- **Offline.**
  - Check the connection with `@capacitor/network`: `Network.getStatus()` returns
    `{ connected, connectionType }`, and `Network.addListener('networkStatusChange', …)` reports
    changes.
  - Show a persistent, non-blocking banner under the header.
  - Explain any action that needs the network, or queue it. Do not just disable it.

## Segment and searchbar

- **Segment.** Use `ion-segment` for 2–4 views of the same data (filters, status). It is not
  navigation between unrelated areas; that is what tabs are for. Add `scrollable` for more
  options or long labels. From 8.4, `ion-segment-button contentId="x"` with `ion-segment-view` /
  `ion-segment-content id="x"` gives swipeable panes. Put a segment that belongs to a header in a
  second `ion-toolbar`.
- **Searchbar.**
  - For live filtering, use `ionInput` with `debounce` (for example 300 ms). `ionChange` only
    fires on commit.
  - `showCancelButton` takes `"never" | "focus" | "always"`. `"focus"` matches iOS.
  - Place the searchbar in a second header toolbar, and set `enterkeyhint="search"` and
    `inputmode="search"`.

## Forms and the keyboard

```html
<ion-input label="Email" label-placement="stacked" type="email" autocomplete="email"
           enterkeyhint="next" helper-text="We send the receipt here"
           error-text="Enter a valid email"></ion-input>
```

- **Labels.** The `label` prop is required from Ionic 8, when the legacy syntax was removed.
  `labelPlacement` is `start | end | fixed | stacked | floating`. Use `aria-label` only when a
  visible label is genuinely redundant. A placeholder is never the label.
- **Errors.** `errorText` shows only while the input has the `ion-invalid` and `ion-touched`
  classes. Angular forms add them automatically. In Vue and React, add them yourself, on blur.
- **Styling.** `fill="outline" | "solid"` works in md mode only. In iOS mode, style fields with
  the item or input CSS variables.
- **Extras.**
  - `counter` needs `maxlength`.
  - `clearInput` suits search-like fields.
  - For input masks, Ionic recommends Maskito.
  - Use `ion-input-password-toggle` in `slot="end"` for passwords.
  - Use `ion-input-otp` (8.6+) for codes. It sets `autocomplete="one-time-code"` itself.
- **Keyboard type.** Use `type` when the data must be that type. Otherwise give an `inputmode`
  hint. Set `enterkeyhint` to `next`, `go`, `send`, `search` or `done`.
- **Focus.** Set focus with the component's `setFocus()` in the view-enter hook or the overlay's
  `didPresent`, not with `autofocus`. Android needs a prior user interaction, and iOS only
  focuses from a user gesture.
- **Keyboard events.** Ionic fires `ionKeyboardDidShow` (with `keyboardHeight`) and
  `ionKeyboardDidHide` on `window`. The framework files show the idiomatic wrapper.
- **`@capacitor/keyboard` config:**
  - `resize` (iOS only): `native` (default), `body`, `ionic`, `none`
  - `style` (iOS only): `KeyboardStyle.Dark | Light | Default`. Set it to match a forced theme.
  - `resizeOnFullScreen` (Android)
  - `Keyboard.setAccessoryBarVisible()` (iPhone only)

## Haptics (Capacitor)

```ts
import { Haptics, ImpactStyle, NotificationType } from '@capacitor/haptics';
await Haptics.impact({ style: ImpactStyle.Light });               // toggle, sheet snap
await Haptics.notification({ type: NotificationType.Success });   // meaningful completion
// Haptics.selectionStart() / selectionChanged() / selectionEnd()  for pickers and drags
```

- **When to use haptics.** On meaningful events only, never on every tap, and never as the only
  feedback. Guard calls with `Capacitor.isNativePlatform()` (from `@capacitor/core`), or catch the
  rejection on the web.

## Motion and reduced motion

- **Ionic's own motion.** Rely on Ionic's page transitions and overlay animations; they match
  the mode.
- **Custom motion.** Use `createAnimation()` (exported by `@ionic/vue`, `@ionic/react` and the
  Angular standalone entry). Run 150–250 ms and animate only `transform` and `opacity`.
- **No decorative motion.** Do not add entrance animations to list rows, cards or headings.
- **Reduced motion.** Ionic does not turn off page transitions for `prefers-reduced-motion`. Set
  the global config at boot, since the config is not reactive:

  ```ts
  { animated: !window.matchMedia('(prefers-reduced-motion: reduce)').matches }
  ```

  Gate your own animations behind the same media query.

## Accessibility

- **Icons.** An icon-only `ion-button` needs an `aria-label`. An `ion-icon` inside a labelled
  control, and any decorative icon, gets `aria-hidden="true"`. An `ion-icon` that is itself
  interactive needs an `aria-label`.
- **Page heading.** Give the primary `ion-title` of every view `role="heading"` and
  `aria-level="1"`. Ionic does not add them.
- **Focus on route change.** Use `focusManagerPriority: ['heading', 'banner']` in the config
  (8.1+, experimental), or call `setFocus()` in the view-enter hook. With the focus manager on,
  going back returns focus to the element that opened the view.
- **Touch targets.** At least 44 × 44 pt on iOS and 48 × 48 dp on Android. `size="small"`
  buttons and dense icon rows fall below this; enlarge the hit area, not the icon.
- **Screen readers.**
  - Test VoiceOver (iOS) and TalkBack (Android) on a device.
  - Reading order follows the DOM, so do not reorder visually with CSS.
  - Announce async results through a toast (polite) or an `aria-live` region.
- **Text.** Write font sizes in `rem` so Dynamic Type works, keep text contrast ≥ 4.5:1, and never
  carry information by colour alone.
