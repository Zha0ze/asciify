# ASCIIFY — Milestones

## Milestone 1: "It works and looks impressive"
**Goal:** A working tool that produces the glowing ASCII overlay effect (tulip reference quality).
**Timeline:** Couple of weeks, few evenings after work.

### Features
- [ ] Image upload (drag & drop + file picker)
- [ ] ML auto-detect subject (`@imgly/background-removal`, client-side WASM)
- [ ] Brush tool — paint to add/remove mask areas
- [ ] Luminance mapping fill strategy (classic ASCII-by-brightness)
- [ ] Random fill strategy
- [ ] 3 preset character sets + custom text input
- [ ] Blend mode selector (screen, multiply, overlay, color-dodge, etc.)
- [ ] Font size slider (controls ASCII grid density)
- [ ] Character opacity slider
- [ ] Background: original image or solid color
- [ ] PNG export

### Tech Stack
- Vite + React + TypeScript
- Tailwind CSS + shadcn/ui (wrapped in Ascii* components)
- Canvas 2D API for rendering
- Deployed on Vercel (free tier)

---

## Milestone 2: "More creative range"
**Goal:** Expand creative tools — more fill strategies, animation, and the collage feature.
**Depends on:** M1 complete and stable.

### More Fill Strategies
- [ ] Edge detection strategy (line-drawing effect)
- [ ] Gradient flow strategy (characters follow image curves)

### Advanced Controls
- [ ] Brightness / contrast sliders
- [ ] Coverage slider (% of mask filled)
- [ ] Edge emphasis slider
- [ ] Density slider
- [ ] Resolution multiplier for export (1x, 2x, 3x)
- [ ] Background blur option

### Animation System
- [ ] Animated ASCII toggle
- [ ] Drift mode (characters move in a direction)
- [ ] Pulse mode (opacity/size oscillation)
- [ ] Wave mode (sine wave through grid)
- [ ] Typewriter mode (sequential character reveal)
- [ ] GIF export (for animated results)

### Collage Mode (new idea)
- [ ] SVG export — ASCII layer as scalable vector "sticker"
- [ ] Swap background — drop a new photo behind the ASCII subject
- [ ] Drag & drop to reposition the ASCII subject on the new background
- [ ] Resize/scale the ASCII subject
- [ ] Export collage as PNG

---

## Future (v2+)
- Multiple object selection with independent settings
- Video (MP4) export
- Preset save/load (save parameter combinations)
- Shareable URLs with encoded parameters
- Undo/redo for mask editing
- Additional animation modes
