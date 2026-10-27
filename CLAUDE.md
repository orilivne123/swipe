# Playlist Swipe

A tiny phone web app: shows the song currently playing on Spotify as a card. Swipe left → the song is removed from the playlist and Spotify skips to the next song. ⏭ button skips without removing. "ביטול" (undo) re-adds the removed song at the end of the playlist.

## About the user
- Speaks Hebrew; reply in Hebrew. Not a developer: explain steps simply (where to click), no jargon.
- Uses an iPhone; the app is added to the home screen from Safari.
- UI text is Hebrew, RTL, masculine form.

## Structure
- Everything is in one file: `index.html` (HTML + CSS + JS, no build step, no dependencies).
- Hosted on GitHub Pages: https://orilivne123.github.io/swipe/ (branch `main`, root).
- Deploy = commit `index.html` to `main`; Pages updates in ~1–2 minutes.

## Spotify
- Auth: Authorization Code with PKCE, client-side only (no server, no client secret). Client ID is entered once in the app and saved in localStorage.
- Redirect URI registered in the Spotify Developer Dashboard must exactly equal the page URL: `https://orilivne123.github.io/swipe/`
- App is in Spotify Development Mode: owner must have Premium; max 5 users (added under User Management).
- Endpoints used (post–Feb 2026 Web API):
  - `GET /me`
  - `GET /me/player/currently-playing` (polled every 3s while the page is visible)
  - `GET /playlists/{id}?fields=name,collaborative,owner(id)`
  - `DELETE /playlists/{id}/items` with body `{ "items": [{ "uri": ... }] }`  (old `/tracks` + `tracks` param are removed)
  - `POST /playlists/{id}/items` with body `{ "uris": [...] }` (undo)
  - `POST /me/player/next` (requires Premium)
- Known limits: can only remove from playlists the user owns or collaborates on. Dev-mode apps cannot read items of other users' playlists, so "copy a friend's playlist" can't be automated — user copies it manually in Spotify.

## History / fixes
- Player endpoints (e.g. `/me/player/next`) can return 200 with a non-JSON body. `api()` parses JSON defensively and treats any `r.ok` as success — fixed a false "הדילוג נכשל" error.
