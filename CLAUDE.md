# CLAUDE.md — krippy.ai

Personal landing page for Krippy (Anil Kripalani). Read [spec.md](spec.md) before changing anything; it describes how the page works today. Proposed work lives in [features.md](features.md).

## Ground rules

- **Never push to `main`.** `main` is live: GitHub Pages serves it at krippy.ai on every push. Always work on a branch and open a pull request. Anil reviews and merges.
- Branch name: `claude/<linear-issue-id>-<short-slug>` (e.g. `claude/KRI-12-tab-urls`).
- One issue = one PR. If the work is bigger than one reviewable PR, stop and say so on the issue instead of building it.
- If the issue is unclear or a decision is needed, ask on the issue and stop. Don't guess on design or copy.

## The codebase

- **No build step, no dependencies to install.** Static files only.
- `index.html` holds all markup, CSS and JS (~1,140 lines). Keep it that way; don't split it into modules or add a bundler.
- `ape-model.js` is a ~4.5 MB base64 GLB. Never open, rewrite or reformat it.
- Images in `art/` and `nft/` are `.webp`. Don't re-encode or rename them unless the issue asks.
- External scripts are pinned: three.js r128 and GLTFLoader 0.128.0. Don't upgrade them as a side effect.

## Conventions

- Match the existing style: 2-space indent, plain ES2015+ in a single `<script>`, no frameworks.
- Use the palette tokens on `:root` (`--ink`, `--fg`, `--muted`, `--amber`, `--rose`). Dark only, by design.
- Fonts: Unbounded (display) and IBM Plex Mono (UI). Don't add others.
- Every tab change goes through `setForm(i, scatter)`. Hook into it rather than duplicating its logic.
- Keep the accessibility behaviour in spec.md intact (`aria-pressed`, `aria-live`, alt text, `prefers-reduced-motion`).
- Keep performance budgets: adaptive particle counts, DPR cap of 2, pause on hidden tab.
- `localStorage` access stays wrapped in try/catch.

## Checking your work

There are no automated tests yet. Before opening a PR:
1. Serve the folder (`python3 -m http.server 8000`) and load it in a headless browser if one is available. Check the console for errors.
2. Click through all four tabs, and confirm Starfall still starts and ends.
3. If you changed layout, check a narrow (mobile) width too.
4. Say in the PR exactly what you checked and what you couldn't.

## Docs

If your change alters behaviour described in spec.md, update spec.md in the same PR. If it ships a feature from features.md, mark it shipped there.

## PR description

- Link the Linear issue.
- What changed and why, in a few lines.
- How you tested it, and anything left for Anil to check by eye.
