# ionic-agents

Ionic mobile UI skills for Claude Code, by [WAME](https://wame.sk). Part of the `wame` marketplace.

## Installation

```
/plugin marketplace add wamesk/claude-code
/plugin install ionic-agents@wame
```

## Skills

| Skill | What it holds |
|-------|---------------|
| **wame-ionic-ui** | Modern, carefully designed Ionic mobile UI that deliberately avoids the stock Ionic look. It covers detecting the framework and Ionic major, version gates (Ionic 7, 8 and 9), design principles and decision rules, a list of generic "AI look" tells to avoid, and a pre-finish checklist. The `reference/` folder has theming (mode, colours, dark and high-contrast palettes, Dynamic Type, radius and elevation tokens, 8 pt spacing, safe areas), patterns (navigation, sheet modals, overlays, lists, states, forms and keyboard, Capacitor haptics and status bar, motion, accessibility), and one short file per framework. |

## Framework detection

The skill works out the framework from the project and does not ask while the evidence is
enough. The first valid signal wins:

1. A `package.json` dependency on `@ionic/vue`, `@ionic/react` or `@ionic/angular`.
2. `type` in `ionic.config.json`.
3. Other traces: `vue` plus `*.vue` files, `react` plus `*.tsx`/`*.jsx`, or `angular.json` plus
   `@angular/core`.

If the signals conflict or are missing, it asks once. It then loads only that framework's
reference file. It also reads the installed Ionic major and uses APIs for that major only. For
example, split step colours start in Ionic 8, and Angular standalone imports move to
`@ionic/angular` in Ionic 9.

## Status

`0.1.0` ships the skill only. An Ionic developer agent, which will build screens using this
skill, is planned for a later release.

## How it composes with design-agents

[`design-agents`](https://github.com/wamesk/claude-code-plugin-design-agents) holds the general UI/UX
method in `wame-ui-ux`: heuristics, WCAG 2.2 AA, screen states, forms, and the full list of AI
design tells. When that skill is installed, `wame-ionic-ui` loads it first and adds only the Ionic
specifics on top. Without it, `wame-ionic-ui` still works on its own and holds every screen to
WCAG 2.2 AA through its own checklist.

## License

MIT
