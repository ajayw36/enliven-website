# Enliven website

Static site — no build step. `index.html` is the whole site; `support.js` and `image-slot.js` are its runtime.

## Deploy on Netlify
New site from Git → pick this repo → leave **Build command** empty and set **Publish directory** to `/` (repo root). Every push to `main` redeploys.

## Editing
- Copy, team bios, stats: edit the text directly in `index.html`.
- Photos: `photos/`. Videos + poster frames: `videos/`. Gallery lists live near the bottom of `index.html` (`PHOTOS` / `VIDEOS`).
- Sign-up link: the `signupUrl` default in the `data-props` attribute at the bottom of `index.html`.

## Design planning
`design/Enliven Website Directions.html` — the three original directions (1a/1b/1c) and the chosen blend (2a), desktop + mobile.
