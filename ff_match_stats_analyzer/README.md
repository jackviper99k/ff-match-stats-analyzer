# FF Match Stats Analyzer — Android + Windows starter

This is a responsive, installable web app (PWA) built against the actual uploaded FF Esports `match_data` JSON structure.

## Included features
- Import one or many complete match response JSON files (workspace limit: 500 unique match IDs).
- Parses `data.match_info`, `data.team_stats[].team_stats[]`, and nested `player_data[]`.
- Deduplicates/replaces a match by Match ID.
- Team leaderboard (points, kills, damage, Booyah, average place).
- Player leaderboard (kills, assists, damage, headshots, knockdowns).
- Per-match team and player details.
- Team or player head-to-head comparison.
- CSV exports for team, player, and match tables.
- Local browser storage; no account or cloud sync.
- Demo JSON from the user's supplied sample.

## Run on Android or Windows
For service-worker/PWA installation, serve this folder over HTTPS or localhost. The easiest test is Python:
```bash
python -m http.server 8000
```
Open `http://localhost:8000` on the same computer. For Android, the folder must be hosted on an HTTPS web host reachable by the phone. Open the HTTPS address in Chrome → menu → **Install app** / **Add to Home screen**.
On Windows, open the HTTPS address in Edge or Chrome → **Install app**. This creates an app-like installed window.

For a quick local preview, opening `index.html` directly may work, but service worker and install features require localhost/HTTPS.

## Important API limitation
The observed request uses POST `/api/match_stats/match_data` and a payload containing `match_ids`, `single_match_id`, `_sign`, and `_ts`. The sample shows a signature field, but the exact signing algorithm and timestamp rules have not been established. This app therefore does **not** fabricate signatures or send requests to the website. Import complete JSON responses copied/exported through permitted means. Do not add cookies, authorization headers, or private tokens.

## Current scope / next build steps
- This is a functional PWA source build, not a compiled Android APK or Windows Setup.exe.
- Data is stored separately in each browser/device; there is no automatic sync.
- Current aggregation assumes a player's stable `player_id` identifies that player across matches.
- More detailed skill/weapon analytics and XLSX multi-sheet export can be added after validating the full tournament dataset.
- To package as APK and Windows installer, wrap the PWA using Capacitor (Android) and a Windows packaging tool, or port the same parser into Flutter. Build/signing requires Android Studio/SDK and Windows build tooling.

## Packaging scaffold added (not compiled in this environment)

The project now includes:
- `package.json` and `capacitor.config.json` for Android packaging.
- `electron/main.cjs` and electron-builder NSIS settings for a Windows Setup.exe.
- The sample match is bundled in `demo-data.js`, so the demo button works offline.

### Build Android APK (requires a PC with Node.js, Android Studio, and Android SDK)
```bash
npm install
npx cap add android
npm run android:sync
npm run android:open
```
In Android Studio, build **Build > Build Bundle(s) / APK(s) > Build APK(s)**. The debug APK is for testing. A release APK needs signing configuration.

### Build Windows Setup.exe (requires Windows + Node.js)
```powershell
npm install
npm run windows:installer
```
The installer is written to `dist/`. This packaging scaffold is provided but has not been built or tested here. Android Studio/SDK and Windows packaging are not available in this runtime.

### Data model note
The supplied response structure is `data.match_info[]`, `data.team_stats[]` (each item contains `team_stats[]`), with nested `player_data[]`. Weapon and skill analytics are read from team and player entries. CSV exports are Excel-compatible; native multi-sheet `.xlsx` export is not included in this version.
