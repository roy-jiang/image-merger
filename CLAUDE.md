# Image Merger Tool

Single-page HTML app for merging multiple images into one canvas.

## File
- `index.html` — the entire app (HTML + CSS + JS, no dependencies)

## Architecture
- PackingAlgorithms: vertical stack, shelf (row-based), MaxRects (rectangle packing)
- All algorithms use binary search for optimal scale factor
- Multi-page support: auto-detect overflow, split into pages
- Canvas interaction: drag to move, 8 resize handles (corners=aspect-locked, edges=stretch)

## Deploy
- Static file served at https://hermes.royjiang.me/image-merger/
- Nginx alias: /var/www/html/image-merger/
- After editing: `cp index.html /var/www/html/image-merger/index.html`

## Design Requirements
- Dark theme, professional tool UI
- Must be a single self-contained HTML file
- No external JS/CSS dependencies
- Chinese UI text
