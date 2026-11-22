# krippy.ai — Spec

Personal landing page for Krippy (Anil Kripalani): "Enterprise AI executive and crypto native."
A single full-screen, interactive particle field that morphs between four "forms" (tabs).

Planned and proposed work lives in [features.md](features.md).

## Hosting & repo

- **Repo:** https://github.com/kripsterama/krippy (branch `main`)
- **Hosting:** GitHub Pages with a custom domain via `CNAME` → `krippy.ai`
- **No build step.** Everything ships as static files; edits to `index.html` go live on push.
- Files are often added via GitHub's web "Add files via upload", so pull before editing locally.
- `.gitignore` excludes `.DS_Store`.

## Files

| Path | What it is |
|---|---|
| `index.html` | The whole site: markup, CSS and JS in one file (~1,140 lines) |
| `ape-model.js` | The 3D ape bust as a base64 GLB (`window.APE_GLB`, ~4.5 MB), loaded async after the page is up |
| `art/u1–u5.webp` | Unbounded Collective portraits for the Art gallery wall |
| `nft/n1–n6.webp` | NFTs that orbit the ape on the On-chain tab |
| `CNAME` | Custom domain for GitHub Pages |

## External dependencies

- Google Fonts: **Unbounded** (display) and **IBM Plex Mono** (UI text)
- three.js r128 (cdnjs) + GLTFLoader (jsdelivr), used only for the ape model

## Layout

- One full-bleed canvas (`#field`) of particles that react to the pointer.
- Name top-left, profile links top-right (LinkedIn, GitHub).
- Caption (eyebrow, title, line, CTA link) and the form switches pinned bottom-left.
- A hint line under the switches that disappears after the first interaction.
- Dark only, by design (`color-scheme: dark`). Palette tokens on `:root`: ink `#08070f`, fg `#f3eefb`, muted, amber `#ffb957`, rose `#ff5d8f`.

## The four forms (tabs)

Defined in the `forms` array in `index.html` (~line 227). Buttons are `#form-0`…`#form-3` with `data-i`.

| # | Button | Eyebrow / Title | Visual | CTA |
|---|---|---|---|---|
| 0 | krippy | "Hello, I'm" / Krippy | Particles sampled from the word "krippy" set in Unbounded; tapping it starts **Starfall** | Play Starfall button |
| 1 | on-chain | "Since 2013" / On-chain | 3D ape model with a light halo; NFTs from `nft/` orbit it | NightWatch on ETHGlobal (ETHOnline 2026 build) |
| 2 | art | "Unbounded Collective" / Art | Five portraits on a curved CSS-3D gallery wall with gallery light and motes; holds longer (16 s) | Collection on OpenSea |
| 3 | music box | "Now playing" / Music Box | Particle music box (pinned drum, comb, notes) | https://kripsterama.github.io/musicbox/ |

## How switching works

Everything funnels through `setForm(i, scatter)` (~line 664), which sets `cur`, drops cohesion so particles re-flow, optionally scatters them, and calls `showCaption(i)`.

`setForm` is called from:
- **Tab button click.** Ends Starfall first if it is running.
- **Auto-advance.** Every `forms[cur].hold || 11` seconds unless paused, in a game, or the pointer is down. Toggled by `#auto-toggle`.
- **Tap on the canvas.** On form 0 it starts Starfall, on other forms it advances to the next one.
- **Arrow keys.** Left and right step through the forms.
- **Starting Starfall.** Forces form 0.

The page always starts on form 0 (`start()` seeds from `forms[0]`). The URL never changes.

## Starfall (game on the krippy tab)

- Stars fall toward the wordmark; the pointer is a shield that knocks them away.
- A star that gets through blasts a crater in the letters; deflections rebuild them. Gold stars, streaks and multipliers.
- Uses a reserved slice of the field's own particles (`RES`) for comets, shield and sparks.
- Ends on word collapse, Stop, Esc, or switching tabs; the score counts however it ends.
- Best score is kept in `localStorage` under `krippy-starfall-best` (wrapped in try/catch).

## Rendering & performance

- WebGL point renderer with a 2D-canvas fallback if WebGL is unavailable; handles context loss/restore.
- Particle count adapts: 38k desktop, 16k on small or touch screens, 5k on the 2D fallback.
- Device pixel ratio capped at 2; resize is debounced (180 ms) and rebuilds all forms.
- Animation pauses when the tab is hidden (`visibilitychange`).
- Start waits for the Unbounded font (max 1.8 s) so the wordmark samples the real glyphs.
- `prefers-reduced-motion`: no scatter, particles start in place, and full cohesion.

## Accessibility notes

- Form switches are a `role="group"` of buttons with `aria-pressed`.
- Caption is `aria-live="polite"`; it is set to `off` during Starfall to avoid spamming screen readers.
- All gallery and NFT images have descriptive `alt` text.
