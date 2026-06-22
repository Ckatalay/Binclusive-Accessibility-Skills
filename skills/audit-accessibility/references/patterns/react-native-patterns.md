# React Native Pattern Catalog

This catalog contains anonymized, reusable accessibility patterns only. Do not add customer names, app identifiers, domains, internal URLs, proprietary screen names, ticket IDs, business copy, screenshots, or exact customer source paths.

## Pattern Entry Template

```md
### PATTERN-RN-001: Short title
- Platform: React Native
- Framework: React Native | Expo
- Component type: Pressable | Input | Cell | Modal | Chart | etc.
- WCAG / Platform: WCAG 2.1.1, 4.1.2, VoiceOver/TalkBack, font scaling, etc.
- Severity default: Critical | Serious | Moderate | Minor
- Fix type default: SAFE | VISUAL-IMPACT | FUNCTIONAL-RISK | RUNTIME-CHECK
- Bad shape: anonymized description of the recurring code/UX problem
- Detection hints: grep/search/static cues
- Correct fix: preferred React Native implementation pattern
- Verification: VoiceOver, TalkBack, Switch Control, Voice Control, font scaling, runtime notes
- False positives / exceptions: when not to flag
```

## Seed Patterns

### PATTERN-RN-001: Pressable or gesture-only control lacks semantics
- Platform: React Native
- Framework: React Native | Expo
- Component type: Pressable / Touchable / Gesture target / Card action
- WCAG / Platform: WCAG 2.1.1 Keyboard, WCAG 4.1.2 Name/Role/Value, VoiceOver/TalkBack activation
- Severity default: Critical
- Fix type default: FUNCTIONAL-RISK
- Bad shape: A `Pressable`, `Touchable*`, gesture handler, row, card, icon wrapper, or custom component performs an action without an accessible name, role, state, or accessible activation path.
- Detection hints: `onPress`, `onLongPress`, gesture handlers, row/card wrappers, `TouchableWithoutFeedback`, missing `accessibilityRole`, `accessibilityLabel`, `accessibilityState`, and accessible alternative for gesture-only actions.
- Correct fix: Use a semantic React Native control pattern with localized name, accurate role, state/value, disabled handling, and accessible custom actions where gestures expose hidden behavior.
- Verification: VoiceOver/TalkBack reaches the element, announces name/role/state, and activates it with the standard gesture; Switch Control/Voice Control can target it where applicable.
- False positives / exceptions: Do not flag passive containers when an inner accessible control is the actual target and the wrapper handler is redundant/delegated.

### PATTERN-RN-002: Icon-only control without accessible name
- Platform: React Native
- Framework: React Native | Expo
- Component type: Icon button / header action / tab action / row action
- WCAG / Platform: WCAG 4.1.2 Name/Role/Value, VoiceOver/TalkBack, Voice Control
- Severity default: Serious
- Fix type default: SAFE when adding a localized label; FUNCTIONAL-RISK when changing control structure.
- Bad shape: A pressable icon, vector icon, image button, header button, floating action button, or row action has no meaningful accessible name.
- Detection hints: icon-only children inside `Pressable`, `Touchable*`, `Button`, custom header actions, vector icon components, `Image` controls, missing `accessibilityLabel` or localized text.
- Correct fix: Provide a localized accessible name that describes the action or content; hide decorative icons/images from assistive technologies when needed.
- Verification: VoiceOver/TalkBack announces the intended action and role; Voice Control can target the control by a meaningful name.
- False positives / exceptions: Do not add a label that conflicts with visible text; prefer visible localized text when one exists.

### PATTERN-RN-003: Field uses placeholder as its only label
- Platform: React Native
- Framework: React Native | Expo
- Component type: TextInput / Search / Select / Picker
- WCAG / Platform: WCAG 1.3.1 Info and Relationships, WCAG 3.3.2 Labels or Instructions, mobile screen reader forms
- Severity default: Serious
- Fix type default: SAFE
- Bad shape: An input relies on placeholder text as the only field name, so context may disappear after entry or be unclear in review mode.
- Detection hints: `TextInput` with `placeholder` but no nearby persistent label, no `accessibilityLabel`, or no design-system field label; custom select/search wrappers that only expose placeholder text.
- Correct fix: Provide a persistent visible label or a programmatic localized label tied to the field context; connect help/error text through clear nearby text, announcement, or field-level accessibility description where appropriate.
- Verification: VoiceOver/TalkBack announces the field name, value, role, and relevant help/error context before and after text entry.
- False positives / exceptions: Compact search controls can be acceptable when a clear localized programmatic label remains available.

### PATTERN-RN-004: Font scaling clipping risk
- Platform: React Native
- Framework: React Native | Expo
- Component type: Text / Button / Cell / Card / Form row / Header
- WCAG / Platform: WCAG 1.4.4 Resize Text, mobile font scaling / Dynamic Type
- Severity default: Serious
- Fix type default: RUNTIME-CHECK by default; VISUAL-IMPACT when layout changes are required.
- Bad shape: Text or controls disable scaling, use restrictive max multipliers, fixed dimensions, absolute positioning, dense horizontal layouts, one-line assumptions, truncation, or custom typography that may not scale at large font sizes.
- Detection hints: `allowFontScaling={false}`, `allowFontScaling: false`, low `maxFontSizeMultiplier`, `numberOfLines={1}`, `ellipsizeMode`, fixed `height`/`maxHeight`/`width`, fixed `lineHeight`, `position: 'absolute'`, `top`/`left`/`right`/`bottom`, dense `flexDirection: 'row'`, fixed-size `FlatList`/`SectionList` items, custom `Text` wrappers with hardcoded `fontSize` or hidden text props.
- Correct fix: Allow platform font scaling, avoid restrictive multipliers for important content, use flexible layout and multiline text, provide vertical fallback for dense rows, avoid absolute positioning around dynamic text, and ensure typography wrappers pass through scaling props intentionally.
- Verification: Runtime check with large iOS Dynamic Type and Android font/display size confirms text, controls, row/card contents, and focus outlines remain usable without clipping or overlap.
- False positives / exceptions: Do not flag fixed-size decorative text that is not user-facing or accessibility-relevant. Do not state that overlap is confirmed unless a runtime screenshot, simulator/device check, or rendered output proves it.

### PATTERN-RN-005: Modal or bottom sheet lacks accessible modal strategy
- Platform: React Native
- Framework: React Native | Expo
- Component type: Modal / BottomSheet / Drawer / Overlay
- WCAG / Platform: WCAG 2.4.3 Focus Order, WCAG 4.1.2 Name/Role/Value, mobile screen reader modal behavior
- Severity default: Serious
- Fix type default: RUNTIME-CHECK
- Bad shape: A modal, bottom sheet, drawer, or overlay opens without clear title, close action, focus/background strategy, or accessibility props that prevent users from navigating stale background content where supported.
- Detection hints: `Modal`, bottom-sheet libraries, portal overlays, custom drawers, missing accessible title/close label, missing `accessibilityViewIsModal`, `accessibilityElementsHidden`, or `importantForAccessibility` strategy.
- Correct fix: Provide a named modal region/screen, localized close action, predictable focus/announcement behavior, and hide or deprioritize background content using platform-supported props where appropriate.
- Verification: Runtime VoiceOver/TalkBack checks confirm users enter, understand, dismiss, and do not accidentally navigate hidden background content.
- False positives / exceptions: Native navigation modals may provide part of the behavior, but final behavior still needs runtime verification.

### PATTERN-RN-006: Color-only status or state
- Platform: React Native
- Framework: React Native | Expo
- Component type: Badge / Status / Validation / Chart / Selection
- WCAG / Platform: WCAG 1.4.1 Use of Color, Differentiate Without Color, Increase Contrast
- Severity default: Serious
- Fix type default: VISUAL-IMPACT
- Bad shape: Status, selection, validation, chart categories, or availability are communicated only through color.
- Detection hints: color-coded badges, red/green states without text/icon, chart legends by color only, selected state shown only by tint, error text implied only by color.
- Correct fix: Add text, icon shape, pattern, state, or accessible value that communicates the same information without relying on color alone.
- Verification: Inspect visually with relevant contrast/color settings where applicable and verify VoiceOver/TalkBack announces the state.
- False positives / exceptions: Color can remain as redundant reinforcement when text, icon shape, or accessible state already communicates the meaning.