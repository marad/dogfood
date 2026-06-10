# dogfood — dog food portion calculator

Static app on GitHub Pages: https://marad.github.io/dogfood/
Single `index.html` + `foods.json` (all persistent data). No build step.

## Adding a new can (the most common task)

The user sends a photo of the dosage table from a can plus the flavor name.
Transcribe the table into a new entry in `foods.json`.

- **Validation trick**: adjacent weight brackets always share the boundary
  value (e.g. 5–10 kg → …–620 g and 10–20 kg → 620–…). A mismatch means a
  misread — on these labels digits 3/8 are easy to confuse. If the suspect
  value falls in the dog's own bracket (currently 5–10 kg), ask the user to
  check the can.
- The first bracket on cans reads "do 5kg" — store it as `minKg: 0`.
- After adding, sanity-check the per-meal portion for the dog's weight
  (other cans land around 250–280 g).

## Data model

- `dogWeightKg` in `foods.json` is the single source of the dog's weight
  (7.9 kg as of June 2026) — update only this field when the weight changes.
- `mealsPerDay: 2` — the app shows per-meal portions (daily dose / 2).
- Daily dose = linear interpolation of weight within its bracket; weights
  outside the table are clamped to the nearest edge.
- Mixing two cans: grams already given cover a fraction of food A's portion;
  the app tops up with `(1 - fraction) × portion of food B`.

## Deployment & testing

- Push to `main` = automatic GitHub Pages deploy (~1 min, legacy build).
- Local testing needs an HTTP server (`python3 -m http.server`) because the
  page fetches `foods.json` — opening the file directly fails.

## qr.png

Printed and hanging on the user's fridge. If the URL ever changes, the QR
code must be regenerated (and reprinted).
