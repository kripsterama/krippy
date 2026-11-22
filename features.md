# krippy.ai — Features

Backlog of proposed changes. Current behavior is described in [spec.md](spec.md).

## 1. A URL for each tab

**Status:** Proposed. Parked on 2026-11-22 ("leave as is for now").
**Size:** Small. About 20–40 lines, all in `index.html`.

### Goal

Each tab (krippy, on-chain, art, music box) gets its own shareable URL that opens straight on that tab, and the browser's Back/Forward buttons move between tabs.

### Why it's cheap

The site is one WebGL scene, not separate pages. Every tab change already goes through `setForm(i)`, so the work is keeping the URL and `setForm` in sync both ways.

### Option A: hash URLs (recommended to start)

`krippy.ai/#on-chain`, `krippy.ai/#art`, `krippy.ai/#music-box`

- Works on GitHub Pages as-is: the server always serves `index.html`.
- No new files.
- Downside: link previews (Twitter, Discord, iMessage) can't show a different title or image for each tab.
- Effort: about an hour.

### Option B: clean paths

`krippy.ai/on-chain`, `krippy.ai/art`, `krippy.ai/music-box`

GitHub Pages has no URL rewrites, so reloading `/art` would 404. Two ways around that:

1. **Stub folders:** `on-chain/index.html`, etc. Each loads the same page and starts on its tab, and can carry its own `<title>` and Open Graph preview tags. This is the only option that gives each tab its own link preview.
   - **Conflict:** `art/` already holds the gallery images, so the image folder would need renaming (e.g. `img/art/`) or the tab would need a different slug.
2. **`404.html` redirect trick:** hacky. The page flashes a redirect and crawlers see 404s. Not recommended.

- Effort: about half a day, mostly the `art/` rename and per-tab meta tags.

### Implementation sketch (either option)

1. **Slug list:** `['krippy', 'on-chain', 'art', 'music-box']`, index-aligned with `forms`.
2. **Initial load:** read the hash or path, find the index, and start on that form. `start()` currently hard-codes `forms[0]` when seeding particles and colors. Seed from the chosen form instead, so the page opens on the tab rather than morphing into it.
3. **Tab click:** after `setForm`, update the URL (`location.hash` for A, `history.pushState` for B) and `document.title` (e.g. "krippy · art").
4. **Back/Forward:** listen for `hashchange` (A) or `popstate` (B) and call `setForm` without writing the URL again.
5. Optionally, pause auto-advance on a deep link, since someone opening `#art` probably wants to stay there.

### Decisions to make

- **Which triggers change the URL?** `setForm` also runs from auto-advance, canvas taps, arrow keys and Starfall start.
  - Recommendation: tab clicks (and maybe arrow keys) push history. Auto-advance and taps either leave the URL alone or use `replaceState`. Otherwise Back has to step through every shape the page auto-cycled through.
- **Root URL:** keep `krippy.ai/` as the krippy tab, with no `#krippy` needed.
- **Starfall:** nothing extra. Tab changes already end the game. Don't make a URL for the game itself.

### Recommendation

Ship Option A first. Move to Option B with stub folders only if per-tab link previews or SEO become important.
