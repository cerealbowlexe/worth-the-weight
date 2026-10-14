# Coach sync — how logging works

The app is a static GitHub Pages site. Your data lives on your phone (browser storage).
`logs.json` is the hand-off file: Claude adds entries here, and the app merges them in
every time it opens or comes back to the foreground (and on **Plan → Sync now**).

## Rules the app follows
- **Adds only.** It never deletes or overwrites anything you entered on the phone.
- An entry is applied **once** (tracked by `id`). Deleting a synced meal on the phone keeps it deleted.
- `removed` lists IDs **Claude created** that were wrong. The app drops those. To correct an
  entry, add its ID to `removed` and log a replacement with a new ID.
- A weigh-in from `logs.json` is skipped if the phone already has a weight for that date.
- `targets` (`cal`, `protein`) sets the daily goals shown in the app. Change it after a 2–3 week review.
- `note` (optional) shows on the Plan tab after syncing.

## Entry formats
```json
"meals":    [{"id": 2026101401, "date": "2026-10-14", "name": "Bagel with cream cheese", "cal": 380, "protein": 12}]
"weights":  [{"id": 2026101480, "date": "2026-10-14", "value": 128.4}]
"workouts": [{"id": 2026101490, "date": "2026-10-14", "session": "A",
              "entries": [{"name": "Pull-ups", "reps": "8/7/6"}]}]
```

## ID scheme (numbers, never strings)
`YYYYMMDD` + two digits:
- meals `01–79`
- weights `80–89`
- workouts `90–99`

Older entries keep their original IDs (`1000–1011` for Oct 9–11, `2012001–2012005` for Oct 12).

Exercise `name`s must match the app exactly so "Last" and "Ready to progress" work:
- **A:** Pull-ups, Push-ups, Band squats, Resistance band rows, Plank
- **B:** Chin-ups, Pike push-ups, Bulgarian split squats, Resistance band curls, Band lateral raises
- **C:** Pull-ups, Banded push-ups, Band Romanian deadlifts, Resistance band rows, Calf raises
