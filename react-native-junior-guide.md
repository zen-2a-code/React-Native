# React Native Junior Guide



## What React Native Is (Plain Words)
- Think of React Native as a magic translator: you write JavaScript + JSX, and it turns that into real iOS and Android screens.
- It reuses React ideas (components, state) but swaps the web-only parts for mobile-native ones.
- You can ship the result to the App Store and Play Store like any other app.

## React, React DOM, and React Native
- `React.js`: the brain; it manages components and state but does not know about any device.
- `React DOM`: the web translator; it tells browsers how to draw your React components.
- `React Native`: the phone translator; it tells iOS/Android how to draw your React components and lets you use phone features (camera, sensors, etc.).

## Why Use React Native?
- One codebase in JavaScript can reach both iOS and Android.
- Comes with ready-to-use building blocks (`View`, `Text`, `Button`, `Image`, etc.).
- Lets you call native device abilities (camera, vibration, file storage) from JavaScript.

## Tiny App Example (Hello Native)
Create an `App.js` like this to see something on screen:

```javascript
import React from 'react';
import { View, Text, Button, Alert, StyleSheet } from 'react-native';

export default function App() {
  return (
    <View style={styles.container}>
      <Text style={styles.title}>Hello from React Native!</Text>
      <Button
        title="Press me"
        onPress={() => Alert.alert('Hi!', 'You tapped the button.')}
      />
    </View>
  );
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    backgroundColor: '#f0f4ff',
    alignItems: 'center',
    justifyContent: 'center',
    padding: 24,
  },
  title: {
    fontSize: 22,
    fontWeight: '600',
    color: '#1b2733',
    marginBottom: 12,
  },
});
```

What happens here:
- `View` is like a simple box/container.
- `Text` shows words.
- `Button` shows a native-looking button; `Alert` pops a native dialog.
- `StyleSheet` helps keep styles tidy.

## How React Native Talks to the Phone
- You write React components.
- React Native translates those components into native UI pieces (e.g., real `UILabel` on iOS, real `TextView` on Android).
- Native APIs (camera, vibration) are exposed to JavaScript so you can call them without writing Swift/Objective-C/Java/Kotlin.

## Under the Hood (Simple View)
- The UI parts (`View`, `Text`, `TextInput`, etc.) get compiled into real native widgets (views) for iOS and Android.
- Your JavaScript logic (functions, state updates, if statements) does **not** get compiled; it runs as JavaScript on a separate JavaScript thread that React Native starts inside the app.
- React Native provides a translation bridge so the JS thread can ask the native side to draw things or use device features. Think of it like a walkie-talkie between your code and the phone.

## Quick Component Mapping
- Web `div` → RN `View` → native container view.
- Web `p` / `span` → RN `Text` → native text labels.
- Web `input` → RN `TextInput` → native `UITextField` (iOS) / `EditText` (Android).
- Web `img` → RN `Image` → native image views.
- Web `button` → RN `Button` (or `Pressable`) → native button controls.

## Tiny Input Example (shows mapping in action)
```javascript
import React, { useState } from 'react';
import { View, Text, TextInput, StyleSheet } from 'react-native';

export default function App() {
  const [name, setName] = useState('');

  return (
    <View style={styles.container}>
      <Text style={styles.label}>Name:</Text>
      <TextInput
        style={styles.input}
        placeholder="Type here"
        value={name}
        onChangeText={setName}
      />
      <Text style={styles.greeting}>Hello {name || 'friend'}!</Text>
    </View>
  );
}

const styles = StyleSheet.create({
  container: { flex: 1, padding: 24, justifyContent: 'center' },
  label: { fontSize: 18, marginBottom: 8 },
  input: {
    borderWidth: 1,
    borderColor: '#ccc',
    padding: 10,
    borderRadius: 6,
    marginBottom: 12,
  },
  greeting: { fontSize: 20, fontWeight: '600' },
});
```
- Here `TextInput` will become `UITextField` on iOS and `EditText` on Android, but you only wrote JavaScript/JSX.

## Starting a Project (Expo First, Then Native if Needed)
- Install Node.js (grab the latest LTS from nodejs.org). This unlocks `npx`, which runs CLI tools without installing them globally.
- In a terminal, `cd` to the folder where you want the app, then create a minimal Expo project with: `npx create-expo-app@latest MyFirstApp --template blank`.
  - The `--template blank` flag keeps the starter super small and JavaScript-only. Without it, you may get a TypeScript-heavy template.
  - The command can take a bit; it installs dependencies and sets up the folder (named after your project).
- Avoid double folders: don’t `mkdir MyFirstApp && cd MyFirstApp && npx create-expo-app MyFirstApp` (that makes `MyFirstApp/MyFirstApp`). Either let `create-expo-app` make the folder (`npx create-expo-app@latest MyFirstApp --template blank`) or, if you already have an empty folder (like a fresh Git clone), run into it and use a dot: `cd MyFirstAppRepo && npx create-expo-app@latest . --template blank`.
- GitHub repo → clone → Expo in place (no subfolder): create an empty repo on GitHub, clone it with VS Code (“Clone Repository” or `git clone <url>`), open that folder, ensure it’s empty except `.git`, then run `npx create-expo-app@latest . --template blank` so Expo scaffolds directly into the repo root. Commit normally afterward.
- `cd MyFirstApp` and run `npx expo start`; scan the QR with the Expo Go app or use a simulator/emulator.
- Edit `App.js` and watch it reload—same React/JSX as before, but with Expo’s helpers available.
- If you prefer the bare React Native CLI from the start, use `npx react-native init MyFirstApp` and follow the platform setup guides for Android Studio/Xcode.
- Expo docs and commands live at expo.dev; the React Native docs are at reactnative.dev.

## Reopening VS Code and Restarting Expo
- Open VS Code and pick “Open Folder,” then choose your project (the folder with `package.json` and `App.js`).
- Open a terminal in that folder (VS Code Terminal → “New Terminal” or your system terminal + `cd /path/to/your/app`).
- If it’s a fresh clone or new machine: run `npm install` once to grab dependencies.
- Start the dev server with `npx expo start` (same as `npm start`) from the project root; scan the QR or pick a simulator when the dev tools page opens.
- If you see odd caching issues, rerun with `npx expo start --clear` to force a clean bundle.
- macOS simctl/xcrun errors: install Xcode or Command Line Tools (`xcode-select --install`), open Xcode once to accept prompts, set the dev path if needed (`sudo xcode-select --switch /Applications/Xcode.app/Contents/Developer`), and verify with `xcrun simctl list`; reinstall/repair Xcode if it still fails.

## Expo Structure Starter (one-stop map)
- Run commands from the project root (the folder with `package.json`). If you see double folders, you likely ran `npx create-expo-app` inside an already-named folder without using `.`—recreate with `npx create-expo-app@latest . --template blank` from the repo root to avoid that.
- File/folder tour and how to use each:
  - `.git` and `.gitignore`: Git history + ignore list. Don’t touch `.git`; add new ignores (like debug logs) to `.gitignore`. `node_modules/` stays out of Git.
  - `.expo/`: Expo’s local cache (device history, dev settings). Ignore it; don’t commit.
  - `.DS_Store` (macOS): Finder metadata. Safe to delete; keep it ignored.
  - `assets/`: app images, icons, fonts. Import with `require("./assets/icon.png")` or `import logo from "./assets/logo.png";`.
  - `node_modules/`: downloaded packages. Never edit manually. If broken, remove and reinstall (`rm -rf node_modules && npm install` or `npm ci`).
  - `App.js`: main UI entry in the starter. Add screens/components here or import them from files you create.
  - `index.js`: registers `App` with React Native/Expo. Leave it alone unless you know you need changes.
  - `package.json`: project manifest.
    - Scripts: `npm start` (== `npx expo start`), `npm test`, etc. Run with `npm run <script>` when not a built-in.
    - Add app dependencies (used in your code): prefer `npx expo install <package>` for React Native/Expo packages so versions stay compatible; pure JS libs can use `npm install <package>`.
    - Add dev-only tools (linters, formatters): `npm install -D <tool>`.
    - Remove a package: `npm uninstall <package>` (and `npx expo uninstall <package>` if you used `expo install`).
    - Keep this file in Git; it tells others what to install.
  - `package-lock.json`: exact versions for repeatable installs. Commit it; don’t hand-edit.
  - `app.json`: Expo config for how your app looks/behaves.
    - Basics: `"name"` (display name), `"slug"` (URL-ish name), `"version"`, `"orientation"`, `"icon"`, `"splash"`, `"backgroundColor"`.
    - Platform-specific: `"ios"` (`"bundleIdentifier"`), `"android"` (`"package"`), permissions, status bar config, etc.
    - Custom data: put small config in `"extra"` and read via `Constants.expoConfig.extra`.
    - Expo reads this when starting/building; edit here instead of hard-coding these values elsewhere.
  - `babel.config.js`: tells Expo/Metro how to transpile modern JS/JSX. Change only if adding Babel plugins/presets.
  - (Sometimes) `expo-shared/`: older/other projects may have this; it stores shared settings. Safe to ignore/commit per template guidance.
- Main code entry is `App.js`. The default Expo starter looks like this (matches the file in this folder):
```javascript
import { StatusBar } from "expo-status-bar";
import { StyleSheet, Text, View } from "react-native";

export default function App() {
  return (
    <View style={styles.container}>
      <Text>Open up App.js to start working on your app!</Text>
      <StatusBar style="auto" />
    </View>
  );
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    backgroundColor: "#fff",
    alignItems: "center",
    justifyContent: "center",
  },
});
```
- You can swap the `Text` and `styles` to see live reloads while `npx expo start` runs.

## Expo vs React Native CLI (When to Choose)
- Expo CLI (managed workflow): fastest setup, fewer config headaches, lots of built-in APIs (camera, sensors, image picker, haptics), easy sharing over QR. Great for learning, prototypes, and many production apps that stay within Expo’s supported modules.
- React Native CLI (bare workflow): more manual setup, but full control of native projects. Best if you know you must add custom native code, heavy native SDKs, or deep platform tweaks from day one.
- Quick map: if you’re new, want speed, or don’t know your native needs yet → start Expo. If you must blend Swift/Objective-C/Java/Kotlin right away → start React Native CLI.

## Switching Paths (Expo ⇄ Bare/CLI)
- Expo → Bare/React Native CLI: run `npx expo prebuild` (or `expo eject`) to generate the Android/iOS native projects. After this, you manage native code directly. It’s effectively one-way for that codebase, but you keep using many Expo packages in the bare app.
- Bare/React Native CLI → “Expo style”: you can add `expo` and many Expo packages to a bare app, but you won’t get back the lightweight managed workflow without creating a fresh Expo project and moving your JS over.
- Finishing Swift work? If you only need temporary native edits, eject/prebuild an Expo app so you still keep Expo tooling. Once ejected, treat it like a normal native+RN project and commit the `android`/`ios` folders.

## Starter Project Checklist
- Install Node.js and a package manager (npm or yarn).
- Install the React Native CLI or use Expo for an easier start.
- Run the default template, edit `App.js`, and watch changes reload in the simulator or on a device.

## Acronyms Explained for a 10-Year-Old
- UI (User Interface): the stuff you see and tap—buttons, text, images.
- UX (User Experience): how it feels to use the app—smooth, easy, or confusing.
- API (Application Programming Interface): a menu of actions the phone lets you use (like “ask the camera for a photo”).
- JSX (JavaScript XML): a friendly way to write UI in JavaScript that looks like HTML.
- DOM (Document Object Model): the tree of things on a web page; React DOM controls this for websites.
- RN (React Native): React for phones; it draws real phone widgets instead of web ones.
- JS Thread (JavaScript Thread): a lane where your JS code runs inside the app.
- Bridge: the messenger that carries requests between JS code and the phone’s native side.
- CLI (Command Line Interface): a helper you run in the terminal to create/build apps (Expo CLI or React Native CLI).
- LTS (Long Term Support): a “stable and supported for a long time” version of software like Node.js.
- IDE (Integrated Development Environment): the app you code in (VS Code, Android Studio, Xcode).
- OS (Operating System): the boss software that runs the device (iOS or Android).
- CSS (Cascading Style Sheets): rules that style how things look; React Native uses similar style ideas in JavaScript objects.
- JS (JavaScript): the language you write to make React Native apps.
- JSON (JavaScript Object Notation): a text way to store settings and data, like labeled boxes written in a notebook (`package.json`, `app.json`).
