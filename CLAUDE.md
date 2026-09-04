# dogfood — dog food portion calculator

Static app on GitHub Pages: https://marad.github.io/dogfood/
Single `index.html` + `foods.json` (all persistent data). No build step.

## Adding a new can (the most common task)

The user sends the flavor name and the metabolizable energy from the can label
(kcal/100 g, sometimes printed as MJ/kg — 1 MJ/kg = 23.9 kcal/100 g).
Add `{ "name": ..., "kcalPer100g": ... }` to `foods` in `foods.json`.

- An entry without `kcalPer100g` is kept in the file but hidden in the app
  until the value is filled in.
- After adding, sanity-check the per-meal portion (other cans land around
  110–165 g at 275 kcal/day).

## Data model

- `dailyKcal` in `foods.json` is the daily energy target (275 kcal as of
  September 2026, a diet target) — update only this field when it changes.
- `mealsPerDay: 2` — the app shows per-meal portions (daily kcal / 2).
- Portion in grams = meal kcal / `kcalPer100g` × 100.
- Mixing two cans: grams already given are converted to kcal via food A's
  density; the app tops up with the remaining kcal converted to grams of B.

## Deployment & testing

- Push to `main` = automatic GitHub Pages deploy (~1 min, legacy build).
- Local testing needs an HTTP server (`python3 -m http.server`) because the
  page fetches `foods.json` — opening the file directly fails.

## PWA (manifest.json, sw.js, icons)

- The service worker is network-first: fresh content whenever online, cache
  only as an offline fallback. No need to bump `CACHE` on every deploy —
  `activate` deletes stale caches.
- Changing an icon means **renaming the file** (`icon-192-v2.png` → `-v3`)
  and updating `manifest.json`, `sw.js` and the `apple-touch-icon` in
  `index.html`. The installed Android WebAPK bakes the icon in; Chrome only
  rebuilds it when the manifest changes, and it can take up to a day.
  Removing and re-adding the home screen shortcut is the instant fix.

## qr.png

Printed and hanging on the user's fridge. If the URL ever changes, the QR
code must be regenerated (and reprinted).
