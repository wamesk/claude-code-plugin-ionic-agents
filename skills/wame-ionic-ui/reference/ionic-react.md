# Ionic React

## Setup and theme CSS

Call `setupIonicReact()` at the top of `App.tsx`, before any Ionic component (even `IonApp`) renders:

```tsx
setupIonicReact({
  mode: 'ios', // optional: only a brand app forces one mode; utility apps omit it (platform-adaptive)
  animated: !matchMedia('(prefers-reduced-motion: reduce)').matches,
});
```

The CSS imports live in `App.tsx`, in this order:

1. `@ionic/react/css/core.css`
2. `normalize`, `structure`, `typography`
3. the utility files you use
4. one `@ionic/react/css/palettes/dark.*.css`
5. `./theme/variables.css` (last)

## Router and pages

**Ionic 8: React Router 5.** `IonReactRouter` (from `@ionic/react-router`) wraps `IonRouterOutlet`,
which contains only `Route` and `Redirect`:

```tsx
<IonReactRouter>
  <IonRouterOutlet>
    <Route exact path="/home" component={Home} />
    <Redirect exact from="/" to="/home" />
  </IonRouterOutlet>
</IonReactRouter>
```

**Ionic 9: React Router 6.4+.**

- `component` / `render` become `element={<Home />}`.
- `Redirect` becomes `Navigate`, and `exact` is gone.
- `useHistory` becomes `useNavigate`, and `RouteComponentProps` is gone (use `useParams`).
- `IonReactRouter` no longer takes a `history` prop.

**Rules for both versions:**

- **Pages.** Every routed component has `IonPage` at its root.
- **Inside the outlet.** A `Switch` has no effect inside `IonRouterOutlet`. Render anything that
  is not a route outside the outlet.
- **Tabs.** Use `IonTabs`, then `IonRouterOutlet` with the tab routes, then
  `IonTabBar slot="bottom"` with `IonTabButton tab=… href=…`.
- **Icons.** `import { add } from 'ionicons/icons'`, then
  `<IonIcon icon={add} aria-hidden="true" />`.

## Navigation

- **Declarative.** Use `routerLink` and `routerDirection` on Ionic components.
- **Programmatic.** `useIonRouter()` returns `push(path, direction?)`, `goBack()` and
  `canGoBack()`, and controls the page transition.

## Lifecycle

`useEffect(…, [])` runs once, because the page stays mounted. Cached pages can also see
`useLocation` change while they are hidden.

- `useIonViewWillEnter`: refresh data.
- `useIonViewDidEnter`: set focus.
- `useIonViewWillLeave`: pause work.

These hooks fire only in components rendered directly by a route.

## Overlays

- **Inline:**

  ```tsx
  <IonModal isOpen={open} onDidDismiss={() => setOpen(false)}> … </IonModal>
  ```

- **Hooks.** `useIonToast`, `useIonAlert`, `useIonActionSheet`, `useIonLoading`, `useIonModal`
  and `useIonPopover` each return `[present, dismiss]`. `useIonModal(Component, props)` takes the
  component to render.
- **Event props.** `onIonRefresh`, `onIonInfinite`, `onIonInput`, `onDidDismiss`.

## Forms and keyboard

- **Validation classes.** Set `ion-touched` and `ion-invalid` through `className` yourself (on
  `onIonBlur` and on validation), and pass `errorText`.
- **Keyboard.** There is no keyboard hook. Listen to `ionKeyboardDidShow` and
  `ionKeyboardDidHide` on `window` inside `useEffect`, and clean up.

## Long lists

- Use `react-virtuoso`: `<IonContent scrollY={false}>` containing
  `<Virtuoso className="ion-content-scroll-host" … />`.
- Wrap each row in a `div` with a fixed height. While lazy-loaded Ionic styles are still missing,
  rows can measure 0 px, and Virtuoso throws.

## Ionic 9

- Requires React 18+ and React Router 6.4+.
- `useIonModal` and `useIonPopover` type `componentProps` against the component.
