# KeyStage keyboard profiles

The keyboard profiles that the KeyStage app downloads when the user picks a keyboard
(epic KS-13). This folder is the content of the public GitHub repo the app reads from:

```
https://raw.githubusercontent.com/<owner>/keystage-profiles/main/index.json
https://raw.githubusercontent.com/<owner>/keystage-profiles/main/profiles/<id>.json
```

The base URL is set in `app/build.gradle.kts` (`PROFILES_BASE_URL`).

## Layout

| Path | What it is |
|---|---|
| `index.json` | List of available models: id, name, file, size, SHA-256, identity data |
| `profiles/<id>.json` | One profile per model, format in `docs/device-profiles.md` |

`generic-gm.json` is **not** here — it is bundled in the app as the fallback.

## Adding or changing a keyboard

1. Add or edit `profiles/<id>.json`. The file name must be the profile `id`.
2. Run `python tools/build_profile_index.py` from the app repo root to rebuild `index.json`.
3. Run `./gradlew testDebugUnitTest` — the tests parse every profile here and check the index.
4. Copy `index.json` and the profile into the GitHub profiles repo and push.

Phones that already have the profile keep their copy; new downloads get the new file.

## Rules the app enforces

- Profile file at most 1 MB; size and SHA-256 must match `index.json`.
- `id` is lower-case letters, digits and `-`, at most 64 characters, and equals the index entry.
- `schemaVersion` must be one the app supports; MIDI channels 1–16, bank/program values in range.
- A file that fails any check is rejected and never saved.
