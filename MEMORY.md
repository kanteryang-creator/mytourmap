# Project Memory

## Project Shape
- Static single-page site.
- Main app lives entirely in [index.html](/Users/neira/Desktop/mytourmap/index.html).
- Repo also contains [places.json](/Users/neira/Desktop/mytourmap/places.json), but the running app currently reads places from the worker endpoint first and falls back to built-in `INIT_PLACES`.

## Live URLs
- Public site: `https://nomadlyzone.pages.dev/`
- GitHub repo: `https://github.com/kanteryang-creator/mytourmap`
- Worker backend: `https://mytourmap.kanteryang.workers.dev`

## Current Product Behavior
- Default landing state is `推荐路线`.
- `推荐路线` filter is the leftmost top filter button.
- There are currently 10 route entries in `ROUTES`.
- Mobile route mode uses:
  - bottom horizontal stop cards
  - expandable route sidebar/list
  - route stop highlight on map
  - route review modal
  - Gaode navigation deep links
- When a route is selected, the app first draws a fallback line, then asynchronously tries to replace each segment with real Google walking directions.

## Architecture Notes
- App config is near the top of `index.html`:
  - `APP_CONFIG.dataSource`
  - `APP_CONFIG.mapProvider`
- Data source abstraction lives in `DATA_SOURCES`.
  - Current default: `worker-google`
  - There is also a `local-only` example provider.
- Map abstraction lives in `MAP_PROVIDERS`.
  - Current default: `google`
- Route walking path logic:
  - uses `DirectionsService`
  - caches segment paths in localStorage under `mtm_walk_route_cache_v1`
  - falls back to straight lines if walking directions fail
- Route place/review/photo lookup logic:
  - uses Google Places
  - caches stop metadata in localStorage under `mtm_places_photo_cache_v5`

## Deployment
- Branch: `main`
- Cloudflare Pages is expected to auto-deploy from GitHub after push.
- GitHub CLI is installed at `/Users/neira/.local/bin/gh`.
- `gh auth status` was successfully configured for account `kanteryang-creator`.
- Future sessions should prefer normal `git push origin main` flow.

## Collaboration Rules For Future Changes
- Test changes yourself before asking the user to verify.
- If browser automation is unavailable or crashes, say so explicitly and still perform code-level verification.
- For UI regressions on mobile route mode, check:
  - sidebar expand/collapse
  - route list visibility
  - bottom route cards
  - route switching
  - map overlay/button clickability
- After fixing user-facing issues, push to GitHub so Pages can deploy.

## Known Risks / Gotchas
- This project has a lot of logic in one file, so regressions can happen when route/mobile/map code interacts.
- Mobile route mode has been fragile because of:
  - overlay z-index conflicts
  - forced sidebar collapsed logic
  - route list vs route detail visibility branching
- Google Maps / Pages setup is not mainland-China-friendly.
- Walking directions depend on Google services and may be slower or unavailable for some segments.
- Many added route stop coordinates are approximate and may need later refinement.

## Recent Important Changes
- `c734bc9` Refactored data-source and map-provider structure, expanded route library.
- `d8ed11a` Fixed mobile route list visibility.
- `2634651` Fixed route sidebar toggle on mobile.
- `9a8db5c` Raised route sidebar toggle above overlays.
- `1dae2ac` Added walking route segments for highlighted routes.

## Recommended Next Refactors
- Split route data out of `index.html` into a dedicated JSON or JS module.
- Split map provider / data source / route UI into separate files.
- Add a more reliable in-page mobile route-switching control that does not depend on overlay stacking.
- Add explicit loading state for walking-route calculation.
