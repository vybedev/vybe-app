# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

VYBE is a single-file mobile web app (`index.html`) for nightlife event discovery in Berlin. No build toolchain, no npm, no framework — pure vanilla HTML/CSS/JS in one file.

## Running the app

Open `index.html` directly in a browser, or serve it with any static file server:

```
npx serve .
# or
python -m http.server
```

There is no build step, no install step, no test suite.

## Architecture

Everything lives in `index.html`:

- **Pages**: `<div class="page">` elements. Shown/hidden by toggling `.active` class via `go(pageName)`.
- **Modals**: `.modal` and `.create-modal` elements toggled via `.open` class. Closed with `closeModal(id)`.
- **Navigation**: bottom nav bar duplicated inside each page's markup; the active tab is `.ni.on`.
- **Events data**: `events[]` array merges local mock data (`EVENTS` constant) with rows fetched from Supabase on load.
- **Map**: Leaflet.js with CartoDB dark tiles. Custom emoji pins via `L.divIcon`. `renderMarkers()` rebuilds all pins on filter/state change.
- **Geocoding**: Nominatim (OpenStreetMap) — used both for address autocomplete in the create form and for resolving a typed address to lat/lng on event creation.
- **Backend**: Supabase (URL and anon key hardcoded at top of `<script>`). Used for persisting events (`events` table). Auth via `supabase.auth` email/password, though the flow is partially wired.

## CSS design tokens

Defined on `:root`:
- `--red` `--green` `--purple` `--yellow` `--orange` — accent colors
- `--bg` `--s1` `--s2` — background layers (dark to slightly lighter)
- `--border` `--muted` — subtle UI chrome

## Known issues / gotchas

- `doLogin()` reads from `document.getElementById('login-name')` but the HTML has `login-email` / `login-pw` fields (no `login-name` input). The actual Supabase auth call is missing — clicking login currently just reads an undefined field.
- `doRegister()` is referenced in the HTML button but never defined.
- Address autocomplete (`addrSearch`) uses inline `onclick` with string interpolation; single-quotes in address names will break the handler.
- The Supabase anon key is embedded in source — safe for a client-side anon key but should not be confused with a service role key.
