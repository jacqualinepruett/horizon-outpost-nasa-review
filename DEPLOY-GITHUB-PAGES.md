# Deploying Horizon Outpost to GitHub Pages

Horizon Outpost runs as a **standalone browser game** — no login, no backend, no paid
services are required to play. The simulation engine is pure client-side logic.

There are two builds in this repository:

| Build | Config | Output | Purpose |
|-------|--------|--------|---------|
| Base44 app | `vite.config.js` | Cloudflare Worker | The live hosted app on Base44 |
| Static SPA | `vite.pages.config.mjs` | `dist-pages/` | GitHub Pages (or any static host) |

Both builds share the **same source** — the same routes, the same game engine, the same
components, the same dark-navy/cyan design. Nothing about the game changes.

## How the static build works

`vite.pages.config.mjs` builds the React app as a pure client-side SPA:

- Uses `@tanstack/router-plugin` to generate the route tree from `src/routes/`.
- Uses **hash history** (`#/briefing`, `#/game`, …) so routing works on both GitHub
  user pages (`user.github.io`) and project pages (`user.github.io/repo/`) with
  zero base-path configuration.
- Aliases `base44:document` to a tiny no-op shim (`pages-shim/base44-document.jsx`) so
  the shared `__root.jsx` imports cleanly without the Base44 platform plugin.
- The game's `GameProvider` (reducer + timers) needs no server. `AuthProvider` resolves
  to an anonymous visitor with no token, so nothing calls the network.

## Build locally

```bash
npm install
npx vite build --config vite.pages.config.mjs
```

This writes a static site to `dist-pages/`. Open it with any static server:

```bash
npx serve dist-pages
```

## Deploy to GitHub Pages

1. Push this repository to GitHub.
2. In the repo, go to **Settings → Pages → Build and deployment → Source** and choose
   **GitHub Actions**.
3. The included workflow `.github/workflows/deploy-pages.yml` runs automatically on
   every push to `main`. It installs dependencies, builds the static SPA, and deploys
   it to GitHub Pages.
4. After the first run, your game is live at
   `https://<your-username>.github.io/<your-repo>/`.

You can also trigger the workflow manually from the **Actions** tab
(**Run workflow** button).

## Notes

- The game is **not** affiliated with, sponsored by, or endorsed by NASA. See the
  in-game **Sources & Credits** and **NASA Resources** pages.
- The dark-navy and cyan theme, all controls, all gameplay systems, and both
  developer credits (Jacqualine Pruett, Isabella Sherwin) are preserved in both builds.
- Version: November 14, 2026.
