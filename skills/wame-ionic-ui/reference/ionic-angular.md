# Ionic Angular

## Import path by version (check first)

| Ionic | Standalone components, providers, controllers, types | NgModule / lazy |
|---|---|---|
| 7.5 – 8.x | `@ionic/angular/standalone`. Importing from `@ionic/angular` pulls in the lazy bundle and breaks tree-shaking. | `@ionic/angular` (`IonicModule`) |
| 9.x | `@ionic/angular` (the `/standalone` subpath is gone) | `@ionic/angular/lazy`. `IonicModule` is deprecated. |

Follow the style the project already uses, and never mix the two in one app.

## Setup and theme CSS

```ts
bootstrapApplication(AppComponent, {
  providers: [
    { provide: RouteReuseStrategy, useClass: IonicRouteStrategy },
    provideIonicAngular({
      mode: 'ios', // optional: only a brand app forces one mode; utility apps omit it (platform-adaptive)
      animated: !matchMedia('(prefers-reduced-motion: reduce)').matches,
    }),
    provideRouter(routes),
  ],
});
```

- **Global styles.** `src/global.scss` `@import`s the Ionic CSS (`@ionic/angular/css/core.css`,
  `typography.css`, …, one `palettes/dark.*.css`). `src/theme/variables.scss` holds the theme.
- **Style order.** In `angular.json` `styles`, list `global.scss` **before**
  `theme/variables.scss`.
- **Ionic 9.** Drop the `~` prefix from CSS `@import`s.

## Router and pages

- **App shell.** `AppComponent` imports `IonApp` and `IonRouterOutlet`. Its template is
  `<ion-app><ion-router-outlet /></ion-app>`.
- **Component imports.** Every component lists each Ionic component it uses in `imports`
  (`IonHeader`, `IonToolbar`, `IonContent`, …).
- **Router links.** `routerLink` on an Ionic component needs `RouterLink` and `IonRouterLink` in
  `imports`. On an `<a>` element, use `IonRouterLinkWithHref` instead.
- **Icons.** Register them with `addIcons({ add })` (from `ionicons`) in the component
  constructor, then use `<ion-icon name="add" aria-hidden="true">`.

## Navigation

- **`NavController`.** `navigateForward`, `navigateBack` and `navigateRoot` take a URL and set the
  transition direction. `back()` goes back.
- **Declarative.** Use `routerDirection` on Ionic elements.

## Lifecycle and change detection

- **Hooks.** `ngOnInit` runs once per created page, so it does not run again when the user comes
  back. Implement `ViewWillEnter` (`ionViewWillEnter`) to refresh data, and `ViewDidEnter` to set
  focus.
- **Ionic 8.** Components hosting `ion-router-outlet` or `ion-nav` must not use `OnPush`.
- **Ionic 9.** Angular 21 defaults to zoneless and Angular 22 defaults to `OnPush`. Keep page
  state in signals, or call `markForCheck()` in the Ionic hooks. If pages keep state in plain
  fields, the component hosting `ion-router-outlet` or `ion-tabs`, and every component between it
  and the root, need eager change detection.

## Overlays

- **Inline:**

  ```html
  <ion-modal [isOpen]="open" (didDismiss)="open = false">
    <ng-template> … </ng-template>
  </ion-modal>
  ```

  The `ng-template` makes the content render lazily.
- **Controllers.** Inject `ModalController`, `ToastController`, `AlertController` or
  `ActionSheetController` from the standalone entry for your version.
- **Event bindings.** `(ionRefresh)`, `(ionInfinite)`, `(ionInput)`, `(didDismiss)`.

## Forms and keyboard

- **Validation.** Angular forms add `ion-touched` and `ion-invalid` automatically, so `errorText`
  shows once a control is touched and invalid.
- **Keyboard.** `Platform.keyboardDidShow` and `Platform.keyboardDidHide` are observables
  (`keyboardHeight` is in the payload).

## Long lists

- Use `@angular/cdk/scrolling` (`ScrollingModule`):
  `<ion-content [scrollY]="false">` containing
  `<cdk-virtual-scroll-viewport class="ion-content-scroll-host" itemSize="56">`, with
  `*cdkVirtualFor` on the rows.

## Ionic 9

- Supports Angular 18–22.
- Use `moduleResolution: "bundler"` in `tsconfig.json`, so subpaths such as
  `@ionic/angular/lazy` resolve.
