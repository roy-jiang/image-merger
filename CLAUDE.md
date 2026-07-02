# Image Merger Tool

Single-page HTML app for merging multiple images into one canvas.

## File
- `index.html` — the entire app (HTML + CSS + JS, no dependencies)

## Architecture
- PackingAlgorithms (every mode keeps images within the page — priority is showing ALL images, not filling the sheet):
  - `vertical` (垂直堆叠) — one image per row, capped to the page box (too-tall images shrink & center), page-break aware
  - `horizontal` (横排) — uniform-height filmstrip: all images share one row height (~√n cols), flow left→right, wrap rows, page-break
  - `shelf` (齐行 Justified) — justified rows: fill a row until justifying to full width hits a target height (~√n columns), commit flush to both edges; row height capped to page
  - `maxrects` (矩形密排) — true maximal-rectangles packing (`tryPackUniform`) at ONE uniform scale, binary-searched so images are as large as possible while all fit; single page
  - `auto` — runs vertical/shelf/maxrects non-paginated, binary-searches a fit scale, keeps the best area utilization
- Rotation-aware layout: `arrange()` lays out on each image's rotated AABB (`effLayoutDims`) then re-centers the real footprint inside that slot, so rotated images never overlap
- Page count: `state.targetPages` (0 = auto/overflow). When ≥1, `layoutForcedPages` splits images into N area-balanced contiguous groups (`partitionByArea`) and scales each group to its own sheet (页数 selector)
- Multi-page support: auto-detect overflow, split into pages (page-break aware — items never straddle a page boundary)
- Free rotation (`angle` field, continuous degrees) as a pure visual transform — does NOT reflow layout; stored per-image so it survives re-arrange, carried onto placements in `arrange()`/`paginate()`; render/export via `drawImageInto`
- Canvas interaction: drag to move, 8 resize handles (corners=aspect-locked & track both axes, edges=stretch), a rotation knob above the top edge for free rotation (Shift snaps to 15°), R key snaps 90°. Hit-testing/resize work in the item's rotated local frame via `toLocal()`
- Material-yield gauge (`updateYield`): current-page image area / sheet area, shown in the console; recomputed inside `renderCanvas`
- Proof-mark decorations (crop marks, registration target, CMYK strip) are CSS/SVG overlays on the `.sheet` wrapper, toggled by `#proofMarks` in `renderCanvas` — NOT drawn on the exported bitmap

## Deploy
- Static file served at https://hermes.royjiang.me/image-merger/
- Nginx alias: /var/www/html/image-merger/
- After editing: `cp index.html /var/www/html/image-merger/index.html`

## Design Requirements
- Dark theme, professional tool UI
- Must be a single self-contained HTML file
- No external JS/CSS dependencies
- Chinese UI text

## Design language — "Imposition Station" (prepress)
- Cool steel graphite workspace (`--bg #121619`), the white press sheet is the hero
- One functional accent: registration cyan `--reg #25c7db` (selection outline/handles use `SEL` = same hex)
- Mono-forward typography (`--mono`) for all labels/data; sans only for CJK UI text
- Signature: the sheet dressed as an imposition proof (crop marks + registration target ⊕ + CMYK control strip) plus the 用料率 yield gauge; ⊕ registration mark is the logo glyph
