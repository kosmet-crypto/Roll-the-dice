# Roll the Dice

A 3D dice roller for phones and desktop browsers. Roll 1–6 dice with a tap or by shaking the phone.
Vanilla HTML, CSS and JavaScript in a single `index.html`.

## Features
* 1–6 animated 3D dice, white or black.
* Dice sound (can be turned off) and shake-to-roll.
* Your dice count, theme and sound setting are remembered.
* Installable as an app (PWA) and works offline after the first visit.

## Install as an app
* **Android / Chrome:** open the GitHub Pages link, then menu → *Install app* (or *Add to Home screen*).
* **iPhone / Safari:** open the link, tap *Share* → *Add to Home Screen*.

When changing `index.html`, the sound or the icons, bump `VERSION` in `sw.js` so installed copies pick up the update.

## Android app (APK)
Every change merged into `main` builds a new APK with GitHub Actions and publishes it as a release.
Always the newest version: https://github.com/kosmet-crypto/Roll-the-dice/releases/latest/download/roll-the-dice.apk

1. Open the link on your Android phone and download `roll-the-dice.apk`.
2. Open the file. Android will ask to allow installs from your browser or file manager; allow it once.
3. Install. Newer APKs install over the old one and keep your settings.

The app checks for a newer release at most twice a day and offers to download it. Updates are not silent:
you tap **Download**, then open the file to install. To check right away, tap **Check for updates** under the ROLL button
(in the web version it reloads the page when a newer one is online).

The APK bundles `index.html`, the sound and the icons, so it works offline from the first launch.
The Android project lives in `android/` (a small WebView wrapper). To build locally: `cd android && ./gradlew assembleRelease`.

The app icon is drawn in `icons/icon.svg`; the Android launcher icon is the same design as vectors in
`android/app/src/main/res/drawable/`.
