# React Native Styling Guide (Junior-Friendly Deep Dive)

Think of styling as telling boxes how big they are, where they sit, what they look like, and how they change when the screen changes. React Native uses the Yoga layout engine (flexbox), and numbers are "device-independent pixels" (dp) so you don't worry about raw screen pixels.

## 1. Orientation and Mindset

### What styling is (before you touch code)
- Everything on screen is a box or text. Styling is about size, position, visual look, and how things respond to different screens or states.
- Flexbox handles layout; dp units keep sizes consistent across screen densities.
- Aim for readable, tappable, and consistent UI before chasing fancy visuals.

### Mental Models for a 10-Year-Old
- Flexbox: lining up books on a shelf; `justifyContent` is how you space them, `alignItems` is whether they sit on the shelf edge or float in the middle.
- Padding vs margin: padding is the room inside your backpack; margin is the space between backpacks.
- Absolute vs relative: relative is staying in the line; absolute is stepping out of line and standing at a marked spot.
- Shadows: like shining a flashlight—bigger `shadowRadius` is fuzzier, `elevation` lifts it higher on Android.
- Responsive: if the table gets wider (tablet/landscape), you can spread out; if it gets narrow (phone/portrait), you stack.

### If You've Never Done Design Before (super simple cheatsheet)
- Layout: pretend you're arranging books on shelves—rows or columns. Flexbox picks how they line up and spread out.
- Spacing: padding = space inside the box (like bubble wrap); margin = space outside between boxes (like air gaps).
- Color and contrast: dark text on light backgrounds (or the reverse) is easiest to read. Use one bright color for action buttons so they stand out.
- Size and hierarchy: big text = titles, medium = subtitles, normal = body. Keep 4-8-12 px spacing steps for consistency.
- Alignment: line things up (left-align most text); center only when it's short (titles/buttons).
- Feedback: things that move or dim on press feel alive (`Pressable` with `pressed` styles). Loading? Show a spinner or skeleton.
- Accessibility: make touch targets comfy (add `hitSlop`), keep good contrast, let fonts scale (don't block it unless the layout breaks).

### Units and Measurements (what the numbers mean)
- RN numbers are "device-independent pixels" (dp): think "points" that auto-scale to screen density. A width of 100 looks similar on low- and high-density screens.
- What is dp (kid version): dp is a stretchy ruler mark. On a low-density phone 1 dp ~= 1 real pixel; on a 2x "retina" phone 1 dp ~= 2 pixels; on a 3x phone 1 dp ~= 3 pixels. You keep writing "44" for a touch target, and the phone stretches that to ~44 physical pixels on any screen so your button stays finger-sized.
- Raw pixels vs dp: you almost never need raw pixels; dp is the default. Use `PixelRatio.get()` only for edge cases (e.g., pixel-perfect hairlines).
- `StyleSheet.hairlineWidth`: the thinnest visible line for the device (great for dividers).
- `%`: works for some props (width/height) but depends on the parent having a measured size. Flex layouts are more predictable.
- No `rem`/`em` in React Native. If you want a scale, make your own:
  ```javascript
  const spacing = 8; // base step
  const font = (step) => 14 + step * 2; // tiny scaler
  // use: padding: spacing * 2, fontSize: font(1)
  ```
- Font scaling: RN respects system font size by default. If a layout breaks on big fonts, cap with `maxFontSizeMultiplier`, but try to keep things flexible.

## 2. Core Building Blocks (styles you write)

### Core Styling Concepts
- Styles are plain JS objects. Use `StyleSheet.create` to keep them organized and faster:
  ```javascript
  const styles = StyleSheet.create({
    box: { backgroundColor: "#4a90e2", padding: 12, borderRadius: 8 },
  });
  ```
- Combine styles with arrays: `<View style={[styles.box, isActive && styles.active]}>`.
- `StyleSheet.compose(a, b)` merges two style objects. `StyleSheet.flatten` turns an array/nested styles into one object (handy for debugging).
- `StyleSheet.absoluteFillObject` is a shortcut for `{ position: "absolute", top: 0, right: 0, bottom: 0, left: 0 }`.
- Prefer stable style objects (from `StyleSheet.create`) to avoid re-renders; dynamic values can be inlined or precomputed.

### Spacing and Size
- `padding`: inside space; `margin`: outside space. Use side variants: `paddingHorizontal`, `paddingVertical`, `paddingTop`, etc.
- `width`/`height`: numbers are dp. Percentages are supported for some props but rely on parent size; prefer flex or `useWindowDimensions` for true responsiveness.
- `minWidth`/`maxWidth`/`minHeight`/`maxHeight`: set limits.
- `overflow`: `visible` (default), `hidden` (clip children), `scroll` (rare on RN; use `ScrollView` instead).
- `borderRadius`: rounded corners; `borderTopLeftRadius`, etc. for per-corner control.
- `borderWidth`/`borderColor`/`borderStyle` (`solid`, `dashed`, `dotted`).

### Positioning and Layering
- `position: "relative"` (default) flows with flex; `position: "absolute"` uses `top/left/right/bottom` to pin.
- `zIndex`: controls stacking (larger shows on top). Works with positioned elements.
- Shortcut: `StyleSheet.absoluteFillObject` to fill the parent.

### Display and Visibility
- `display`: `flex` (default) or `none` (removes from layout).
- `opacity`: 0 (invisible) -> 1 (fully visible).

### Color, Background, and Effects
- `backgroundColor`: view background. `color`: text color.
- Gradients aren't a core style; use `expo-linear-gradient` or a custom component for gradients.
- `shadowColor`, `shadowOffset`, `shadowOpacity`, `shadowRadius` (iOS). `elevation` (Android) for shadows. For cross-platform shadows, set both; Android mainly respects `elevation`.
- `backfaceVisibility`: hide the back of a rotated view (`hidden`/`visible`).
- `transform`: move/scale/rotate:
  ```javascript
  transform: [{ translateX: 10 }, { rotate: "15deg" }, { scale: 1.1 }];
  ```
- `opacity` + `scale` are common for press/animation effects.
- Color formats you can use: hex `"#ff9900"`, short hex `"#f90"`, `rgb(255, 153, 0)`, `rgba(255, 153, 0, 0.5)` for transparency.
- Platform system colors: `PlatformColor("label")` (iOS) or `PlatformColor("@color/black")` (Android) to match system palettes; `DynamicColorIOS` can auto-switch light/dark.

### Text Styling
- `fontSize`, `fontWeight` (`"400"`, `"700"`, or `bold`), `fontFamily` (needs the font loaded).
- `lineHeight`: distance between baselines.
- `textAlign`: `left`, `center`, `right`, `justify`.
- `textDecorationLine`: `underline`, `line-through`.
- `textDecorationColor`, `textDecorationStyle` (`solid`, `double`, `dotted`, `dashed`).
- `textTransform`: `uppercase`, `lowercase`, `capitalize`.
- `letterSpacing`: space between letters.
- `numberOfLines` (prop, not style) + `ellipsizeMode` for truncation.
- `textShadowColor`, `textShadowOffset`, `textShadowRadius`: shadows for text.
- `includeFontPadding` (Android): set to `false` to remove extra top padding some fonts have.
- `textAlignVertical` (Android): `top`, `center`, `bottom` for vertical text alignment inside `TextInput`.
- `allowFontScaling` / `maxFontSizeMultiplier`: respect or limit system font scaling.
- `writingDirection` / `direction`: force `ltr` or `rtl` if needed.

### Images and Icons
- `resizeMode`: `cover` (fill, crop), `contain` (fit inside), `stretch`, `center`, `repeat`.
- `tintColor`: recolor monochrome PNG/SVG (for icons).
- `ImageBackground` lets children sit on top of an image.
- `blurRadius`: blur amount.
- `borderRadius` works on images, too (pair with `overflow: "hidden"` on the parent for clipping).

### Lists, Overflow, and Clipping
- `overflow: "hidden"` to clip children (for rounded images, masked content).
- `borderRadius` + `overflow: "hidden"` makes circular avatars (`width/height` equal, `borderRadius: width/2`).

### Assets (images, fonts) - where to put them and how to use them
- Images: place in `assets/` (e.g., `assets/icon.png`). Import with `require("./assets/icon.png")` or `import icon from "./assets/icon.png";` then `<Image source={icon} />`. For remote images, use `{ uri: "https://" }`. Keep images organized (icons in `assets/icons`, photos in `assets/images`).
- Backgrounds: use `ImageBackground` or a gradient via `expo-linear-gradient`.
- Fonts: store font files in `assets/fonts/` (e.g., `assets/fonts/Inter-Regular.ttf`). Load them before render (Expo example):
  ```javascript
  import { useFonts } from "expo-font";
  import { Text } from "react-native";

  export default function App() {
    const [ready] = useFonts({
      Inter: require("./assets/fonts/Inter-Regular.ttf"),
      "Inter-Bold": require("./assets/fonts/Inter-Bold.ttf"),
    });
    if (!ready) return null; // or a splash/loading
    return <Text style={{ fontFamily: "Inter-Bold" }}>Hello</Text>;
  }
  ```
- App icons/splash: set paths in `app.json` under `"icon"` and `"splash"` (Expo picks from `assets/`).

## 3. Layout with Flexbox (containers and items)
Flexbox is like arranging books on a shelf. The shelf is the container; the books are the items.
- `flexDirection`: row (left->right) or column (top->bottom). Default is `column`.
- `justifyContent`: how items spread along the main axis. Options: `flex-start`, `center`, `flex-end`, `space-between`, `space-around`, `space-evenly`.
- `alignItems`: how items align on the cross axis. Options: `flex-start`, `center`, `flex-end`, `stretch`.
- `alignContent`: cross-axis alignment when items wrap to multiple lines (use with `flexWrap: "wrap"`).
- `flexWrap`: `wrap` lets items go to the next line; default is `nowrap`.
- `gap` / `rowGap` / `columnGap`: spacing between children (RN 0.71+; check your version).
- Item-level overrides: `alignSelf` (overrides `alignItems` for one child).
- Sizing within flex:
  - `flexGrow`: how much free space this item can take (0 = none, 1+ = share extra).
  - `flexShrink`: whether it can shrink when space is tight (0 = don't shrink, 1 = share shrinking).
  - `flexBasis`: starting size before growing/shrinking (like a preferred width/height).
  - Shortcut: `flex: 1` equals `flexGrow: 1, flexShrink: 1, flexBasis: "auto"`.
- `aspectRatio`: keeps width/height proportional. `aspectRatio: 1` makes a square.

## 4. Applying Styles Correctly

### Style Precedence and Inheritance
- Text nesting: styles on a parent `Text` pass down to children (color, font). Views do not inherit. If a child text should look different, override its style directly.
- Arrays win right-to-left: `<View style={[styles.base, isError && styles.error]}>` applies `styles.error` last.
- Inline style objects win over `StyleSheet.create` entries when both are in the array and placed last.
- Platform overrides: `Platform.select({ ios: {...}, android: {...} })` merges with your base style; place it later in the array if it should win.

### Where to Apply Styles on Common Components
- `View`, `Text`, `Image`: use `style`.
- `ScrollView`: layout/background goes on `style`; inner spacing goes on `contentContainerStyle`.
- `FlatList`/`SectionList`: outer style is `style`; inner padding/spacing is `contentContainerStyle`; item spacing is usually via item wrappers or `ItemSeparatorComponent`.
- `TextInput`: `style` controls text/box; `textAlignVertical` (Android) for vertical centering; keep padding comfortable.
- `Pressable`/`Touchable*`: use `style` (or style function) for states.
- `SafeAreaView`: use `style` on the safe area wrapper; if using `SafeAreaProvider`, wrap at the root.
- `KeyboardAvoidingView`: wrap inputs so they move above the keyboard; give it a `style={{ flex: 1 }}` and test `behavior` (`padding`/`position`).

### Extra Helpers That Affect "Feel"
- `pointerEvents`: control if a view blocks touches (`"auto"`, `"none"`, `"box-none"`, `"box-only"`). Handy for overlays that should let touches pass through.
- `hitSlop`: extra touch area around buttons/pressables: `<Pressable hitSlop={8}>...</Pressable>` makes taps easier without changing visuals.
- `importantForAccessibility` (Android) / `accessibilityElementsHidden` (iOS): hide elements from screen readers if they are purely decorative.

## 5. Responsive, State, and Theming (adapting to change)

### Responsive/Dynamic Layout (like "Geometry" in Swift)
- `useWindowDimensions()` gives live `width`/`height`; great for switching layouts when rotating or on tablets.
  ```javascript
  const { width } = useWindowDimensions();
  const isWide = width > 600;
  return <View style={isWide ? styles.row : styles.column} />;
  ```
- `Dimensions.get("window")` is static; use `useWindowDimensions` for updates on rotate.
- `onLayout` event gives actual measured size/position of a view for dynamic decisions.
- `Platform.select` or `Platform.OS` to tweak styles per platform.
- `PixelRatio` for pixel-perfect tweaks; `StyleSheet.hairlineWidth` gives thinnest visible border.
- Safe areas: wrap top-level in `SafeAreaView` (or `SafeAreaProvider` + `SafeAreaView` from `react-native-safe-area-context`) to avoid notches. `StatusBar` component controls status bar style/visibility.

### State-Based Styles (interaction)
- `Pressable` supports a style function: `style={({ pressed }) => [styles.base, pressed && styles.pressed]}` for tap feedback.
- Change styles with component state (`useState`) to reflect theme, selection, validation, etc.

### Theming (light/dark and custom palettes)
- Detect system theme: `const scheme = useColorScheme();` then pick colors.
  ```javascript
  const colors = scheme === "dark" ? darkPalette : lightPalette;
  <View style={[styles.box, { backgroundColor: colors.card }]} />
  ```
- Store theme in context or state if you want manual toggles.
- Keep a `colors` object and reuse it to stay consistent.

### Animation Hooks (styles that move)
- With the built-in `Animated` API or Reanimated (popular third-party), you animate style props like `opacity`, `transform`, `height`, `backgroundColor`.
- Simple example with `Animated`:
  ```javascript
  const fade = useRef(new Animated.Value(0)).current;
  useEffect(() => {
    Animated.timing(fade, { toValue: 1, duration: 400, useNativeDriver: true }).start();
  }, []);
  return <Animated.View style={{ ...styles.box, opacity: fade }} />;
  ```

## 6. Patterns and Recipes (reusable shapes)

### Common Style Recipes
- Center everything: `{ flex: 1, justifyContent: "center", alignItems: "center" }`.
- Two-column wrap: `{ flexDirection: "row", flexWrap: "wrap", gap: 12 }` (or margins if `gap` unsupported).
- Sticky footer: parent `flex: 1`, content `flex: 1`, footer `alignSelf: "stretch"`; or use `position: "absolute", bottom: 0, left: 0, right: 0`.
- Full-screen background: `{ flex: 1 }` on root, then use `ImageBackground` or `backgroundColor` on the container.
- Active/idle toggle (state-driven):
  ```javascript
  import { useState } from "react";
  import { Pressable, StyleSheet, Text, View } from "react-native";

  export function Toggle() {
    const [on, setOn] = useState(false);
    return (
      <Pressable onPress={() => setOn((v) => !v)}>
        <View style={[styles.pill, on ? styles.pillOn : styles.pillOff]}>
          <Text style={styles.pillText}>{on ? "ON" : "OFF"}</Text>
        </View>
      </Pressable>
    );
  }

  const styles = StyleSheet.create({
    pill: {
      paddingVertical: 10,
      paddingHorizontal: 18,
      borderRadius: 999,
      alignSelf: "flex-start",
    },
    pillOn: { backgroundColor: "#2ecc71" },
    pillOff: { backgroundColor: "#e74c3c" },
    pillText: { color: "#fff", fontWeight: "700" },
  });
  ```

### Hands-On Examples (copy/paste and tweak)
- Press feedback + theme-aware card:
  ```javascript
  import { Pressable, Text, useColorScheme, StyleSheet } from "react-native";

  const light = { card: "#fff", text: "#111", shadow: 6 };
  const dark = { card: "#1c1c1e", text: "#f5f5f7", shadow: 0 }; // use elevation 0 to flatten

  export function Card({ title, onPress }) {
    const scheme = useColorScheme();
    const colors = scheme === "dark" ? dark : light;
    return (
      <Pressable
        onPress={onPress}
        style={({ pressed }) => [
          styles.card, // base shape
          { backgroundColor: colors.card, elevation: colors.shadow }, // theme swap
          pressed && styles.cardPressed, // tap feedback
        ]}
      >
        <Text style={[styles.cardTitle, { color: colors.text }]}>{title}</Text>
      </Pressable>
    );
  }

  const styles = StyleSheet.create({
    card: {
      padding: 16,
      borderRadius: 12,
      shadowColor: "#000",
      shadowOffset: { width: 0, height: 4 },
      shadowOpacity: 0.15,
      shadowRadius: 8,
      elevation: 4,
    },
    cardPressed: {
      transform: [{ scale: 0.98 }],
      opacity: 0.85,
    },
    cardTitle: { fontSize: 18, fontWeight: "600" },
  });
  ```
- Responsive grid that flips to rows on wide screens:
  ```javascript
  import { useWindowDimensions, View, StyleSheet } from "react-native";

  export function Tiles({ children }) {
    const { width } = useWindowDimensions();
    const isWide = width > 700; // feel free to tweak the breakpoint
    return (
      <View style={isWide ? styles.gridWide : styles.gridNarrow}>
        {children}
      </View>
    );
  }

  const styles = StyleSheet.create({
    gridNarrow: { flexDirection: "column", gap: 12 },
    gridWide: { flexDirection: "row", flexWrap: "wrap", gap: 12 },
  });
  ```
- Circular avatar with clipping:
  ```javascript
  import { Image, StyleSheet } from "react-native";

  export function Avatar({ uri, size = 64 }) {
    return (
      <Image
        source={{ uri }}
        style={[
          styles.avatar,
          { width: size, height: size, borderRadius: size / 2 },
        ]}
      />
    );
  }

  const styles = StyleSheet.create({
    avatar: { borderWidth: StyleSheet.hairlineWidth, borderColor: "#ccc" },
  });
  ```
- Safe-area padded screen with status bar color:
  ```javascript
  import { SafeAreaView } from "react-native-safe-area-context";
  import { StatusBar, StyleSheet, View } from "react-native";

  export function Screen({ children }) {
    return (
      <SafeAreaView style={styles.safe}>
        <StatusBar barStyle="dark-content" backgroundColor="#f7f7f7" />
        <View style={styles.body}>{children}</View>
      </SafeAreaView>
    );
  }

  const styles = StyleSheet.create({
    safe: { flex: 1, backgroundColor: "#f7f7f7" },
    body: { flex: 1, padding: 16 },
  });
  ```
- Gradient background (Expo):
  ```javascript
  // npm i expo-linear-gradient
  import { LinearGradient } from "expo-linear-gradient";
  import { StyleSheet, Text } from "react-native";

  export function Hero() {
    return (
      <LinearGradient colors={["#6a11cb", "#2575fc"]} style={styles.hero}>
        <Text style={styles.heroText}>Gradient magic</Text>
      </LinearGradient>
    );
  }

  const styles = StyleSheet.create({
    hero: { padding: 24, borderRadius: 16 },
    heroText: { color: "#fff", fontSize: 22, fontWeight: "700" },
  });
  ```
- Hairline divider row:
  ```javascript
  import { StyleSheet, View } from "react-native";
  export const Divider = () => <View style={styles.divider} />;

  const styles = StyleSheet.create({
    divider: {
      height: StyleSheet.hairlineWidth,
      backgroundColor: "#dcdcdc",
      marginVertical: 8,
    },
  });
  ```
- Scrollable form with comfy inputs:
  ```javascript
  import { ScrollView, TextInput, StyleSheet } from "react-native";

  export function Form() {
    return (
      <ScrollView
        style={styles.screen}
        contentContainerStyle={styles.content} // spacing inside
        keyboardShouldPersistTaps="handled"
      >
        <TextInput
          placeholder="Name"
          placeholderTextColor="#999"
          style={styles.input}
        />
        <TextInput
          placeholder="Email"
          keyboardType="email-address"
          autoCapitalize="none"
          style={styles.input}
        />
        <TextInput
          placeholder="About you"
          multiline
          numberOfLines={4}
          style={[styles.input, styles.multiline]}
          textAlignVertical="top"
        />
      </ScrollView>
    );
  }

  const styles = StyleSheet.create({
    screen: { flex: 1, backgroundColor: "#f5f7fb" },
    content: { padding: 16, gap: 12 },
    input: {
      backgroundColor: "#fff",
      padding: 12,
      borderRadius: 10,
      borderWidth: StyleSheet.hairlineWidth,
      borderColor: "#d8dde6",
      fontSize: 16,
    },
    multiline: { minHeight: 100 },
  });
  ```

## 7. Guided Labs and Practice (your first wins)

### Guided Lab 1: Build a comfy card (uses everything above)
```javascript
import { Pressable, StyleSheet, Text, View } from "react-native";

export default function App() {
  return (
    <View style={styles.screen}>
      <Text style={styles.title}>Welcome!</Text>
      <Text style={styles.body}>
        This is a comfy card. Tap the button to see press feedback.
      </Text>
      <Pressable style={({ pressed }) => [styles.button, pressed && styles.buttonPressed]}>
        <Text style={styles.buttonText}>Tap me</Text>
      </Pressable>
    </View>
  );
}

const styles = StyleSheet.create({
  screen: {
    flex: 1,
    padding: 20,
    backgroundColor: "#f7f7f7",
    justifyContent: "center",
    gap: 16,
  },
  title: { fontSize: 24, fontWeight: "700", color: "#1f2937" },
  body: { fontSize: 16, lineHeight: 22, color: "#4b5563" },
  button: {
    paddingVertical: 12,
    paddingHorizontal: 18,
    backgroundColor: "#2563eb",
    borderRadius: 10,
    shadowColor: "#000",
    shadowOffset: { width: 0, height: 4 },
    shadowOpacity: 0.12,
    shadowRadius: 6,
    elevation: 3,
    alignSelf: "flex-start",
  },
  buttonPressed: { opacity: 0.85, transform: [{ scale: 0.98 }] },
  buttonText: { color: "#fff", fontSize: 16, fontWeight: "600" },
});
```
What to observe: flex container with gap, text sizing and lineHeight, corner rounding + shadow, press feedback via style function.

### Practice Plan (beginner-friendly)
- Play with spacing: change `padding`, `gap`, `margin` and see how blocks move.
- Flip layout: switch `flexDirection` between `column` and `row`, and toggle `justifyContent` / `alignItems` to feel the difference.
- Color swaps: try `backgroundColor` in hex and `rgba(...)`, add `tintColor` to an icon.
- Text tuning: adjust `fontSize`, `lineHeight`, `fontWeight`; add `numberOfLines` to truncate long text.
- Shadows vs corners: compare iOS shadows (`shadow*`) and Android `elevation`; see how rounding + `overflow: "hidden"` affects them.
- Responsive tweak: use `useWindowDimensions` to switch between a column and row layout at a width breakpoint.
- Add press feedback: use a `Pressable` style function to change `opacity`/`scale` when pressed.
- Load an image: put a file in `assets/images`, render with `<Image source={require("./assets/images/pic.png")} resizeMode="cover" />`, and add `borderRadius`.

## 8. Craft, Habits, and Troubleshooting

### Good Habits
- Reuse colors/sizes from a constants file to stay consistent.
- Prefer `StyleSheet.create` for stable styles; use inline objects only when truly dynamic.
- Test on different screen sizes (simulator resize, rotate, or use `useWindowDimensions` to branch).
- Keep `overflow: "hidden"` in mind when rounding corners for images.

### Debugging Styles (simple tricks)
- Order matters: later styles in an array win (`[a, b]` -> `b` overrides `a` on conflicts). If something won't change, check style order.
- Log a final style: `console.log(StyleSheet.flatten([styles.box, maybeStyle]));` to see what values are applied.
- Use the React Native Inspector (shake device or press `i`/`m` in Expo CLI) to pick a view and see its styles.
- Temporary "safety vest": add `borderWidth: 1, borderColor: "red"` to spot invisible boxes, then remove.
- If `gap` isn't working, you might be on an older RN—switch to margins between children.

## 9. Platform Differences and Pitfalls

### Platform Differences and Pitfalls
- Shadows: iOS uses `shadow*`; Android uses `elevation`. Set both for consistency, but expect small visual differences.
- Fonts: custom `fontFamily` must be loaded (e.g., with `expo-font`). Missing fonts fall back silently.
- `gap`: only in newer RN versions; if missing, use margins.
- Percentages: work for some props; flex + `useWindowDimensions` are more predictable.
- Overflow on Android: `overflow: "hidden"` can clip shadows. Consider wrapping: outer view for shadow, inner view for rounded clip.
- Text scaling: if your UI must not break with large system fonts, cap with `maxFontSizeMultiplier`.
- RTL: use `writingDirection` or `I18nManager` if supporting right-to-left layouts.

### Common Pitfalls (quick fixes)
- "Why is there no gap?" -> Your RN version may not support `gap`; use margins between children.
- "My shadow disappeared on Android when I rounded corners." -> Wrap in two views: outer for shadow/elevation, inner for rounded `overflow: "hidden"`.
- "Text is too small/large on some phones." -> Respect font scaling; if it breaks, set `maxFontSizeMultiplier` and add more flexible layout (wrap text, allow extra lines).
- "Percent width doesn't work." -> Ensure the parent has a size or use flex (`flex: 1` / `flex: 0` / `flexBasis`) or `useWindowDimensions`.
- "Padding vs margin confusion." -> Padding = inside the box; margin = outside between boxes. If boxes touch each other, increase margin; if text touches edges, increase padding.
- "Touchable is hard to tap." -> Add `hitSlop={8}` and ensure height >= 44dp.

## 10. Reference (fast lookup)

### Style Prop Catalog (quick but fuller list)
- View/layout visuals: `backgroundColor`, `border*Color`, `border*Width`, `borderRadius` (and per-corner), `borderStyle`, `opacity`, `elevation` (Android shadow), `shadow*` (iOS shadow), `transform`, `backfaceVisibility`, `overflow`, `gap/rowGap/columnGap`.
- Layout sizing/position: `flex`, `flexGrow`, `flexShrink`, `flexBasis`, `flexDirection`, `justifyContent`, `alignItems`, `alignContent`, `alignSelf`, `flexWrap`, `aspectRatio`, `width/height`, `min*/max*`, `margin/padding` (+ all side variants), `position`, `top/right/bottom/left`, `zIndex`, `display`.
- Text: `color`, `fontSize`, `fontWeight`, `fontFamily`, `lineHeight`, `letterSpacing`, `textAlign`, `textAlignVertical` (Android), `textDecorationLine`, `textDecorationColor`, `textDecorationStyle`, `textTransform`, `textShadowColor`, `textShadowOffset`, `textShadowRadius`, `includeFontPadding` (Android), `writingDirection`, `direction`.
- Text extras: `fontStyle` (`normal`/`italic`), `fontVariant` (small-caps/lining-nums), `lineBreakStrategyIOS` (word vs character wrapping nuance).
- Image: `resizeMode`, `tintColor`, `blurRadius`, `borderRadius` (+ per-corner), `opacity`, `overflow`, `backfaceVisibility`.
- Platform helpers: `Platform.select`, `Platform.OS`, `PixelRatio`, `StyleSheet.hairlineWidth`, `useWindowDimensions`, `onLayout`.

### Quick Reference (common style props)
- Layout: `flex`, `flexDirection`, `justifyContent`, `alignItems`, `alignContent`, `flexWrap`, `gap`, `alignSelf`, `aspectRatio`.
- Spacing: `margin` (+ `Top/Bottom/Left/Right/Horizontal/Vertical`), `padding` (+ variants).
- Size: `width`, `height`, `minWidth`, `maxWidth`, `minHeight`, `maxHeight`.
- Positioning: `position`, `top`, `right`, `bottom`, `left`, `zIndex`.
- Visuals: `backgroundColor`, `borderRadius`, `borderWidth`, `borderColor`, `borderStyle`, `opacity`, `transform`, `shadow*`, `elevation`.
- Text: `color`, `fontSize`, `fontWeight`, `fontFamily`, `lineHeight`, `textAlign`, `textDecorationLine`, `textTransform`, `letterSpacing`.
- Images: `resizeMode`, `tintColor`.
- Misc: `overflow`, `display`.
