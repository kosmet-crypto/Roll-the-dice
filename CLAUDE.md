# Roll the Dice: notes for Claude

Roll the Dice is a 3D dice roller. One web page (`index.html`, vanilla JS, settings in `localStorage`)
is shipped three ways: GitHub Pages (web), PWA (`manifest.json`, `sw.js`), and an Android app
(`android/`, a WebView wrapper whose APK GitHub Actions builds and publishes as a Release on every push to `main`).
Its sibling app is `kosmet-crypto/Point-counter`, set up the same way.

The owner talks in Serbian (Cyrillic); answer in Serbian. Code, comments, UI text and commit messages are in English.

## Privacy
- Commit as `Claude <noreply@anthropic.com>` (e.g. `git -c user.name=Claude -c user.email=noreply@anthropic.com commit ...`),
  never with a name or email taken from git config, the session or anywhere else.
- Merging a PR through GitHub records the owner's GitHub profile name as author/committer of the merge.

## Workflow
- Work on a branch, open a PR, wait for the `build` check, and let the owner merge it.
  Never force-push `main`. After a PR is merged, start the next change from the latest `main`.
- Before pushing, test the page in headless Chromium (Playwright is preinstalled): serve the repo
  (`npx http-server`), load `index.html`, exercise the change, and check for page errors.
  Simulate the Android app with `addInitScript(() => { window.DiceAndroid = {...} })`.
- The Android SDK is not reachable from the dev container; the PR's CI build is the Gradle build.

## App behaviour worth knowing
- Dice values come from `getWeightedRandomNumber()`, which uses the weights set in the hidden
  "Secret Menu" (tap the header icon 5 times). Keep that menu hidden and do not mention it in
  public text (manifest, README, store descriptions).
- `held[i]` marks dice kept on the next roll. Dice never tapped have no entry, so check every index
  (`Array.from({length: count}, (_, i) => !!held[i])`), never `held.slice(...).every(...)`.
- Settings (`roll_dice_prefs`), weights (`roll_dice_weights`) and the shake switch (`roll_dice_shake`)
  live in `localStorage`; validate anything read back from it.

## Rules that keep updates working
- **Page changes (`index.html`)**
  - Bump `VERSION` in `sw.js` so PWA caches refresh.
  - The Android app downloads `index.html` from `main` by itself (`Ota.java`) and serves it from the
    same in-app origin, so settings stay. If the page starts calling a new `DiceAndroid` bridge
    method, raise `<meta name="app-native">` in `index.html` and `Ota.NATIVE_API` together; older apps
    then keep their page until the APK is updated.
- **Version numbers:** `versionCode` = Actions `run_number` and the release tag is
  `v1.0.<run_number>`. The update check compares the number after the last dot with the installed
  `versionCode`, so change both together or neither.
- **APK offers:** CI fingerprints `android/`, `icons/`, `manifest.json` and `*.mp3` into
  `BuildConfig.NATIVE_HASH` and the release notes (`native: <hash>`). The app only offers an APK when
  that fingerprint changes; page-only changes arrive silently through `Ota`.
  The "Check for updates" button (under ROLL) runs both checks right away.
- **Signing:** `android/app/dice.keystore` must never change. A different key means installed apps
  cannot update and the owner would have to reinstall.

## Setting up the owner's next app the same way
When the owner brings a new repo "like Roll the Dice / Point", give it: a review and fixes of the page;
an icon (`icons/` PNGs rendered from an SVG with Playwright, plus adaptive/monochrome vectors in
`android/.../res/drawable`); manifest + service worker; the Android wrapper and CI workflow copied from
here with a new package name, APK name and a new keystore; the "Check for updates" button; a README
with the APK link; and a CLAUDE.md like this one.

## Keeping sessions cheap
- Batch small items into one PR, with one CI build and one merge.
- Verify in the browser with numbers (DOM values, counts, page errors); take a screenshot only when
  the layout changes.
- Keep replies short: what was done and what the owner should try.
- Do not poll CI; report back once the build has finished.
