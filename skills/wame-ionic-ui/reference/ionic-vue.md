# Ionic Vue

## Setup and theme CSS

The setup and the CSS imports both live in `main.ts`, in this order:

1. `@ionic/vue/css/core.css`
2. `normalize`, `structure`, `typography` (required for Dynamic Type)
3. the utility files you use
4. one `@ionic/vue/css/palettes/dark.*.css`
5. `./theme/variables.css` (last, so your theme wins)

```ts
createApp(App)
  .use(IonicVue, {
    mode: 'ios', // optional: only a brand app forces one mode; utility apps omit it (platform-adaptive)
    animated: !matchMedia('(prefers-reduced-motion: reduce)').matches,
  })
  .use(router);
```

## Router and pages

- **Router import.** `createRouter` and `createWebHistory` come from `@ionic/vue-router`, not from
  `vue-router`.
- **Outlet.** `App.vue` is `<ion-app><ion-router-outlet /></ion-app>`. Never use `<router-view>`,
  `<keep-alive>` or `<transition>`, because Ionic keeps pages in the DOM itself.
- **Pages.** Every routed view has `<ion-page>` as its root. Transitions, lifecycle hooks and
  layout depend on it. Components shown in a modal do not need it.
- **Component imports.** Import each Ionic component locally from `@ionic/vue` in the SFC that
  uses it. Icons come from `ionicons/icons`:
  `<ion-icon :icon="add" aria-hidden="true" />`.
- **Tabs.** `Tabs.vue` contains `<ion-page><ion-tabs><ion-router-outlet />` plus
  `<ion-tab-bar slot="bottom">`. Child pages of a tab are **sibling** routes with the tab prefix
  (`tab1`, `tab1/view`), not nested children.

## Navigation

- **Declarative.** Use the `router-link` attribute, plus `router-direction`, on Ionic components.
- **Programmatic.** Use `useIonRouter()`, called inside `setup`. It has `push`, `replace`,
  `back`, `forward` and `navigate` (with a custom transition), and controls the page transition.
- **Route params.** Pass them as props (`props: true` on the route), so the page does not depend
  on the router.

## Lifecycle

`onMounted` runs only the first time, because the page stays mounted when you navigate away.

- `onIonViewWillEnter`: refresh data on every visit.
- `onIonViewDidEnter`: set focus (`setFocus()`).
- `onIonViewWillLeave`: pause work.

These hooks fire only in components mapped directly by the router.

## Overlays

- **Inline (preferred).** Reset the state on dismiss:

  ```vue
  <ion-modal :is-open="open" @didDismiss="open = false"> … </ion-modal>
  ```

- **From logic.** Use `modalController`, `toastController`, `alertController` and
  `actionSheetController` from `@ionic/vue`.
- **Event names.** `@ionRefresh`, `@ionInfinite`, `@ionInput`, `@didDismiss`.

## Forms

Vue must add the validation classes itself:

```vue
<ion-input label="Email" label-placement="stacked" v-model="email"
  :class="{ 'ion-touched': touched, 'ion-invalid': !!error }"
  :error-text="error" @ionBlur="touched = true" />
```

## Composables

- `useKeyboard()` returns the refs `{ isOpen, keyboardHeight }`.
- `useBackButton(priority, handler)` handles the Android hardware back button.

## Long lists

1. Install `vue-virtual-scroller@next`. The `next` tag is the Vue 3 build.
2. Import `vue-virtual-scroller/dist/vue-virtual-scroller.css`.
3. Use `RecycleScroller` for a fixed row height, or `DynamicScroller` for an unknown one.
4. Wrap it in `<ion-content :scroll-y="false">` and give it `class="ion-content-scroll-host"`.

## Ionic 9

- Requires Vue 3.5+ and Vue Router 5.
- In navigation guards, return a value instead of calling `next()`.
