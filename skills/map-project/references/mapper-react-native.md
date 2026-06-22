# Mapper React Native Reference

Use this reference when `map-project` is triggered for a React Native or Expo application. The mapper observes and documents only; it never edits source code.

## Privacy Boundary

User-provided audit documents may be read as inspiration during skill creation only. Do not copy customer data into skill resources. Do not persist real customer names, app identifiers, domains, internal URLs, proprietary screen names, ticket IDs, business copy, screenshots, or exact customer source paths from customer artifacts.

## Step 0: Establish Context

First, run the read-only project inspector if available:

```bash
node map-project/scripts/inspect-project.mjs /path/to/project
```

Treat the inspector JSON as discovery input, not as proof. Verify React Native and Expo signals by reading package manifests, app config, navigation files, and actual screen/component source.

Determine these facts before asking the user:

- Application model: Expo managed, Expo prebuild, bare React Native, monorepo app, library/package, or mixed web/native app.
- Platforms in scope: iOS, Android, both, or shared JS/TS only.
- Navigation model: React Navigation stack/tabs/drawer, Expo Router, file-based routing, custom router, native navigation, or mixed.
- Screen roots: `app/`, `src/screens/`, `src/features/`, `src/navigation/`, route groups, layouts, modal routes, and deep-link entry points.
- Reusable UI roots: `components/`, `src/components/`, `src/ui/`, design-system packages, form controls, list rows, cards, cells, modals, toasts, icons, charts, maps, media, and web views.
- Localization: i18n libraries, translation files, hardcoded strings, locale formatting, RTL/I18nManager usage, and platform-specific strings.
- Accessibility settings: screen reader labels, roles, states, hints, focus management, font scaling, reduce motion, color/contrast support, and touch target conventions.

If app model, navigation, or screen roots remain ambiguous after inspection, ask for the app package/target and primary screen folders.

## User Scope Questions

Ask these when the user did not already provide scope:

1. Should I map the whole React Native app, selected flows/screens, selected components, a folder path, or a free-form target list?
2. Should localization/hardcoded string hotspots be included? Default: yes.
3. Should iOS and Android behavior be mapped together? Default: yes when both platforms are in scope.
4. Should web/native shared components be separated? Default: yes when React Native Web or platform-specific files are present.

## Project and Dependency Analysis

Read relevant manifests and configuration files when present:

- `package.json`, lockfiles, workspace configs, `metro.config.*`, `babel.config.*`, `tsconfig.json`.
- Expo config: `app.json`, `app.config.*`, `expo-router` files, plugins, and platform sections.
- Native folders when present: `ios/`, `android/`, Gradle files, Podfile, Info.plist, AndroidManifest.
- Test/storybook/docs when they reveal component variants and states.

Classify dependencies as:

- `A11Y-RELEVANT`: navigation, gesture, modal/bottom-sheet, picker/select, form, validation, date picker, list/virtualization, chart, map, media, webview, animation, icon, UI kit, safe-area, keyboard, and portal libraries.
- `L10N-RELEVANT`: i18n libraries, generated strings, ICU/pluralization, date/number/currency formatting, locale detection, RTL/bidi helpers.
- `NEUTRAL`: unrelated data, networking, persistence, analytics, build, or utility packages.

For relevant packages, record name, version when inferable, role, likely accessibility/localization concerns, and platform-specific runtime checks.

## Localization Detection

Look for:

- i18n libraries such as `i18next`, `react-i18next`, `react-intl`, `expo-localization`, `i18n-js`, `lingui`, `formatjs`, or custom translation helpers.
- Translation resources under `locales/`, `translations/`, `messages/`, `lang/`, `src/i18n/`, JSON/PO/TS resource files, or generated catalogs.
- Hardcoded visible strings in `Text`, button labels, placeholders, alerts, toasts, navigation titles, `accessibilityLabel`, `accessibilityHint`, `accessibilityValue`, and image labels.
- Formatting usage: `Intl.*`, `toLocaleString`, date-fns/dayjs/luxon/moment, currency/number/date helpers, and custom formatters.
- RTL support: `I18nManager`, `isRTL`, `allowRTL`, `forceRTL`, `writingDirection`, left/right styles, directional icons, and platform-specific mirroring.

If no localization setup is found, record `NONE - strings appear hardcoded` and flag user-facing literals and accessibility strings for later review.

## Screen and Flow Detection

Enumerate the mapped scope:

- Expo Router: `app/**/_layout.*`, route files, route groups, dynamic routes, modal routes, tab layouts, and not-found screens.
- React Navigation: navigators, screen registration, tab/drawer/stack options, custom headers, deep-link config, and route params that affect labels/titles.
- Conventional RN apps: `src/screens`, `src/navigation`, `src/features`, `screens`, `navigation`, `routes`, or custom route config.
- Platform-specific files: `.ios.tsx`, `.android.tsx`, `Platform.select`, native module wrappers, and shared/native-web variants.

If no screen model is discoverable and no path was provided, ask for the navigation entry point and primary screen folders.

## Shared UI Inventory

Find likely shared UI roots such as `components/`, `src/components/`, `src/ui/`, `src/shared/`, `src/common/`, `src/design-system/`, `features/*/components`, `packages/ui`, and `app/components`.

For each relevant component/control, record:

- Name and verified file path.
- Component type: Pressable/Button, Link, TextInput, Select/Picker, Checkbox, Radio, Switch, Slider, Dialog/Modal, BottomSheet, Menu, Tabs, Accordion/Disclosure, List/FlatList/SectionList, Card/Row, Image/Icon, Chart, Map, Toast/Banner, Progress/Loading, WebView, Media control, custom gesture area.
- Whether it wraps native RN primitives, Pressable/Touchable components, third-party primitives, gestures, or custom platform code.
- Accessibility props used: `accessible`, `accessibilityRole`, `accessibilityLabel`, `accessibilityHint`, `accessibilityState`, `accessibilityValue`, `accessibilityElementsHidden`, `importantForAccessibility`, `accessibilityActions`, `onAccessibilityAction`, `accessibilityLiveRegion`, `accessibilityViewIsModal`, `accessibilityLanguage`, `nativeID`, and `aria-*` aliases where used by RN.
- Text scaling props used: `allowFontScaling`, `maxFontSizeMultiplier`, `numberOfLines`, fixed height/width styles, absolute positioning, and design-system typography wrappers.
- Which mapped screens/flows use it, when statically discoverable.

## Inline UI Inventory

For each mapped screen/flow, record inline UI that bypasses shared components:

- Interactive controls and gestures: `Pressable`, `TouchableOpacity`, `TouchableHighlight`, `TouchableWithoutFeedback`, `Button`, custom gesture handlers, swipe actions, long press, drag/drop.
- Forms and validation UI: `TextInput`, secure entry, picker/select, checkbox/radio/switch, error text, required state, hints, keyboard types, content types.
- Navigation and modal patterns: screen titles, custom headers, tabs, drawers, bottom sheets, dialogs, alerts, custom close/back buttons, focus movement after navigation.
- Text and layout: `Text`, nested text, rows/cards, `FlatList`/`SectionList` items, dynamic values, fixed sizing, font scaling controls, truncation, absolute/flex layout risks.
- Images, icons, charts, maps, media, web views, camera/scanner flows, and custom drawing.
- Dynamic content: loading indicators, progress, toasts/banners, async results, validation summaries, live updates.
- Hardcoded visible text and hardcoded accessibility text.
- Locale-sensitive formatting and RTL-sensitive layout.

Record file path, approximate line range when verified, UI element/control type, interactivity, existing accessibility props, localization status, font-scaling/layout risks, runtime-only concerns, and notes.

## Output File

Write one file in project-root `Binclusive-auditing/` named `<project-name>_<YYYY-MM-DD>_project-map.md`.

Required sections:

1. Header: app, date, mapper, scope, app model, platforms, language, navigation model, localization, directories, totals.
2. Dependencies: table of relevant packages and concerns.
3. Localization setup: resources, locales, fallback, string APIs, pluralization, formatting, RTL/mirroring evidence.
4. Screens / Flows: route/screen inventory and file paths.
5. Shared Components / Controls: reusable UI inventory.
6. Inline UI Inventory: per-screen inline UI table.
7. Text Scaling / Layout Risk Inventory: `allowFontScaling`, `maxFontSizeMultiplier`, `numberOfLines`, fixed size, absolute positioning, dense flex rows, list item sizing, and platform-specific risk notes.
8. Platform Feature Inventory: screen reader, Switch Control/Voice Control, Dynamic Type/font scale, reduce motion, contrast, touch targets, RTL, keyboard/accessibility focus, and generated/native UI when statically visible.
9. Coverage and Blind Spots: runtime-only concerns, third-party controls, native modules, excluded targets, simulator/device checks required.
10. How to Use This File: instructions for `audit-accessibility`.

## Non-Negotiable Rules

- Do not modify source files.
- Do not install dependencies or run native builds unless the user explicitly asks.
- Do not invent targets, screens, paths, line numbers, rendered accessibility tree output, or runtime behavior.
- Mark screen reader output, focus order, font-scale layout, contrast, touch target size, gestures, third-party UI, native modules, and platform-specific behavior as `needs runtime verification` unless directly verified.
- Keep customer-provided examples out of the skill package unless they have been anonymized into generic patterns.