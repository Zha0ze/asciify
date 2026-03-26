# asciify — Project Spec

## Overview

A browser-based tool that isolates an object in a photograph and fills its shape with ASCII characters, composited over the original image using configurable blend modes. The tool produces stylized visual outputs where ASCII text acts as a procedural texture layer — creating effects ranging from glowing luminous overlays to dark textured stamps, all from a single source image.

This is not a full-image ASCII converter. The key differentiator is **object-isolated rendering** — the user selects a subject, and only that subject receives the ASCII treatment. The rest of the photo remains untouched.

---

## Core Pipeline

```
Image Upload → Subject Detection (ML) → Manual Mask Refinement → Grid Sampling → Character Mapping → Color Sampling → Blend Mode Compositing → Output
```

### Step-by-step:

1. **Image Upload** — user drags/drops or picks a file
2. **Subject Detection** — ML model (e.g. `@imgly/background-removal` running client-side in WASM) auto-detects the primary subject and generates a binary mask
3. **Manual Refinement** — brush tool to add/remove mask areas. Paint to include, erase to exclude
4. **Grid Generation** — the masked area is divided into a grid. Cell size = font size of the ASCII characters
5. **Character Mapping** — for each grid cell inside the mask, a character is selected based on the active fill strategy
6. **Color Sampling** — each character is colored by sampling the pixel color at its grid position from the original image
7. **Compositing** — characters are drawn onto the canvas using the selected blend mode (`globalCompositeOperation`)
8. **Output** — real-time preview on canvas. Export as PNG or GIF/video (for animated results)

---

## Fill Strategies

Four switchable modes that control how characters are selected and placed within the mask. All share the same pipeline — only the character selection logic changes.

### 1. Luminance Mapping
The classic approach. Characters are sorted by visual density (how much "ink" they occupy). Bright pixels get dense characters (`@`, `#`, `W`), dark pixels get sparse ones (`·`, `:`, `.`). The tool auto-computes density by rendering each character to a small canvas and counting filled pixels.

**Visual result:** The ASCII layer reads as a recognizable reproduction of the image content. Closest to traditional ASCII art.

### 2. Edge Detection
A Sobel or similar edge-detection kernel is run on the image first. Characters are only placed along detected edges/contours. Interior areas of the mask remain empty (or receive a very sparse fill).

**Visual result:** The object looks like a line drawing made of text. Dramatic, graphic, architectural.

**Character mapping options:**
- Map by edge strength (stronger edge → denser character)
- Cycle through user's character set along contour lines
- Randomize selection along edges

### 3. Random Fill
Ignores luminance. Characters from the user's set are distributed randomly within the mask at uniform density. Color still comes from the underlying pixel.

**Visual result:** Even texture overlay. Less "image reproduction," more "ASCII as material." Works especially well with blend modes like `color-dodge` or `screen` for glow effects.

### 4. Gradient Flow
Computes the gradient direction per pixel (same math as edge detection, but the angle is preserved instead of discarded). Characters are selected or rotated to follow the directional flow of the image.

**Visual result:** Characters sweep along curves — petals, hair, fabric folds. ASCII as brushstrokes. The most visually distinctive mode.

**Simpler fallback:** If full directional mapping is too complex for v1, rotate through the character set along the flow direction instead of matching character shapes to angles.

---

## Character Sets

### Presets
Curated character sets optimized for different looks:

- **Standard** — `@#%&*+=-:. ` (full density ramp)
- **Minimal** — `·:;` (subtle, delicate)
- **Blocks** — `█▓▒░` (Unicode block elements, chunky)
- **Symbols** — `★☆◆◇●○` (decorative)
- **Slashes** — `/\|—_` (directional, good for gradient flow)
- **Code** — `{}[]()<>;` (programmer aesthetic)
- **Dots** — `⠁⠂⠄⡀⢀⠈⠐⠠` (Braille patterns)

### Custom Input
A text input field where the user types their own characters. The tool uses them immediately — type, see the result, iterate. Single character input produces a halftone/stencil effect. Multiple characters create richer texture.

---

## Controls Panel

Right sidebar layout, grouped into collapsible sections. Reference: see uploaded UI screenshot of ASCII Generator tool.

### Section: Source
- **Upload Image** — drag & drop zone / file picker button
- **Export Format** — dropdown: PNG, GIF, MP4
- **Export** — button
- **Resolution** — dropdown: 1x, 2x, 3x

### Section: Mask
- **Auto-detect Subject** — button (triggers ML segmentation)
- **Show Mask Overlay** — toggle (visualizes the mask in semi-transparent color)
- **Brush Size** — slider
- **Brush Mode** — toggle: Add / Erase
- **Reset Mask** — button

### Section: Characters
- **Fill Strategy** — dropdown: Luminance, Edge Detection, Random Fill, Gradient Flow
- **Character Set** — dropdown (presets) + custom text input field below
- **Font Size** — slider with numeric value (controls grid cell size)
- **Blend Mode** — dropdown: Normal, Screen, Multiply, Overlay, Color Dodge, Soft Light, Lighten, Darken, etc.
- **Character Opacity** — slider, 0–100%
- **Invert Mapping** — toggle (flip the density ramp: dark pixels get dense characters)

### Section: Intensity
- **Coverage** — slider, 0–100% (what percentage of mask grid cells get filled)
- **Edge Emphasis** — slider, 0–100% (boosts character placement along edges regardless of strategy)
- **Density** — slider (adjusts the density ramp spread)
- **Brightness** — slider, -100 to +100
- **Contrast** — slider, -100 to +100

### Section: Background
- **Mode** — dropdown: Original Image, Blurred Image, Solid Color, Transparent
- **Blur** — slider (when Blurred Image is selected)
- **Opacity** — slider, 0–100%
- **Color Picker** — (when Solid Color is selected)

### Section: Animation
- **Animated ASCII** — toggle (off by default)
- **Animation Type** — dropdown: Drift, Pulse, Wave, Typewriter
- **Speed** — slider
- **Direction** — dropdown (for Drift: up, down, left, right, random)

---

## Animation Modes (v1 scope — static by default, toggle to enable)

- **Drift** — characters slowly move in a direction, wrapping within the mask
- **Pulse** — characters oscillate in opacity or size
- **Wave** — a sine wave travels through the character grid, modulating size or opacity
- **Typewriter** — characters appear sequentially as if being typed, filling the mask progressively

Animation runs on `requestAnimationFrame`. When animation is active, export switches to GIF/video.

---

## Export

### PNG
Flattens the canvas (photo + composited ASCII layer) to a single raster image. Resolution multiplier (1x, 2x, 3x) scales the output for print or high-DPI use.

### GIF / Video (for animated results)
When animation is active, the tool records frames and exports as:
- **GIF** — using a client-side GIF encoder (e.g. `gif.js`)
- **MP4** — using `MediaRecorder` API on the canvas stream

User controls duration or loop count before export.

---

## Tech Stack

### Framework
**Decided: Vite + React + TypeScript**

Why Vite over Next.js: ASCIIFY is a single-page app where all processing (ML detection, canvas rendering, ASCII generation) runs client-side in the browser. No server-side features needed. Vite is simpler, faster to develop, and has a lower learning curve.

**Deployment target: Vercel (free tier)** — serves static files only, no server costs.

### Key Dependencies
- **Canvas 2D API** — core rendering engine
- **`@imgly/background-removal`** — client-side ML subject segmentation (WASM, no server needed)
- **shadcn/ui** — component library for controls (wrapped in custom components)
- **Tailwind CSS** — styling
- **gif.js or similar** — GIF encoding for animation export (Milestone 2)
- **Geist Mono** — typography (pending design exploration)

### Design System
All shadcn components are wrapped in project-specific components. The app never imports shadcn directly — only through the wrapper layer. This allows:
- Consistent styling enforced in one place
- Swappable underlying library
- Clear component API with project-specific defaults
- Naming convention: `Ascii[ComponentName]` (e.g. `AsciiSlider`, `AsciiSelect`, `AsciiToggle`)

Component location: `/src/components/ui/`
Page/feature components: `/src/components/features/`

---

## Multi-Agent Architecture

### Overview
The project uses a multi-agent setup with Claude Code as the orchestrator spawning sub-agents. Gemini API was removed from the setup — Tetiana (UX designer) serves as the Art Director, and Claude Code handles code review.

### Environment Setup
The orchestrator (main Claude Code instance) needs:
- **Agent skill files** in `/agents/` directory — each sub-agent reads its skill file at spawn
- **Shared contract document** (`CONTRACTS.md`) — defines component naming conventions, file structure, token names, and boundaries between agents
- **CLAUDE.md** in project root — orchestrator instructions (replaces separate orchestrator skill file)

### Agent Roles

#### Orchestrator (Claude Code — main instance)
- Receives instructions from the user
- Decides which sub-agent handles each task (or handles small tasks directly)
- Writes scoped task prompts for sub-agents
- Passes files/code to sub-agents
- Collects results and coordinates sequential work
- Handles code review directly (no Gemini)
- Does NOT write feature code or components directly (delegates to Builder/Design System)

**Config file:** `CLAUDE.md` (project root — read automatically each session)
**Responsibilities:**
- Task decomposition and delegation
- Context management (what each sub-agent needs to know)
- Conflict resolution between Builder and Design System outputs
- Code review (handled directly, no external API)
- Showing work to Tetiana for visual approval (she is the Art Director)

#### Visual Buddy (Claude Code — sub-agent using Paper MCP)
- Explores layout and aesthetic options in Paper
- Drafts multiple variants (2-3 options) for Tetiana to choose from
- Fast, rough, disposable sketches — not production-ready designs
- Available anytime throughout the project, not just at the start

**Skill file:** `/agents/visual-buddy.md`
**Tools:** Paper MCP server
**Scope:**
- UI layout exploration (controls panel, upload area, etc.)
- Aesthetic exploration (dark/light theme, color directions)
- Quick drafts of new features before coding begins

**Rules:**
- Always present multiple options, not just one
- Keep sketches rough — this is exploration, not final design
- Label each variant clearly so Tetiana can reference them

#### Builder (Claude Code — sub-agent)
- Writes all feature code: canvas pipeline, mask logic, fill strategies, animation, export
- Makes architectural decisions inline (file structure, module boundaries)
- Requests components from Design System agent via orchestrator — does NOT create UI primitives
- Spawned per task with a fresh context window each time

**Skill file:** `/agents/builder.md`
**Scope:**
- `/src/engine/` — canvas pipeline, rendering, compositing
- `/src/features/` — mask tools, export, animation
- `/src/app/` or `/src/pages/` — page-level composition
- Integration of Design System components into features

**Rules:**
- Never create reusable UI components directly — request them via orchestrator
- Follow file structure and naming from CONTRACTS.md
- Write tests for core engine functions

#### Design System (Claude Code — sub-agent)
- Builds and maintains the component library in BOTH code AND design tools
- Enforces design patterns across component-related code
- Reviews component usage in Builder output (when orchestrator requests review)
- **Teaching mode (always on):** Explains how each code concept maps to Figma/design tool concepts

**Skill file:** `/agents/design-system.md`
**Tools:** Paper MCP (phase 1), Figma MCP (phase 2 — proper library)
**Scope:**
- `/src/components/ui/` — all wrapped primitives
- `/src/components/features/` — composed feature components (e.g. ControlsPanel, MaskToolbar)
- `/src/styles/` — design tokens, theme configuration
- Design tool components and tokens (Paper now, Figma later)
- Component documentation

**Rules:**
- All components wrap shadcn — never expose shadcn directly
- Naming convention: `Ascii[ComponentName]`
- Every component exports TypeScript props type
- Always explain the code pattern AND the equivalent Figma/design tool pattern
- Build components in both code and design tool simultaneously

### Workflow Example

```
User: "Build the luminance mapping fill strategy"

Orchestrator:
  1. Checks if Builder needs any new components → no, this is engine work
  2. Spawns Builder sub-agent with:
     - Task: "Implement luminance mapping fill strategy"
     - Context: pipeline architecture, grid system specs, character density sorting algorithm
     - Skill: /agents/builder.md
  3. Builder implements in /src/engine/strategies/luminance.ts
  4. Builder returns: "Done, created luminance strategy module"
  5. Orchestrator reviews the code directly
  6. Orchestrator flags any issues → spawns Builder again with feedback
  7. Builder fixes issues
  8. Orchestrator shows screenshot to Tetiana for visual approval
  9. Tetiana approves or requests changes
  10. Done
```

```
User: "Explore what the controls panel should look like"

Orchestrator:
  1. This is visual exploration → routes to Visual Buddy
  2. Spawns Visual Buddy with:
     - Task: "Draft 3 layout options for the controls panel"
     - Context: list of controls needed, reference screenshot from ascii-magic.com
     - Skill: /agents/visual-buddy.md
  3. Visual Buddy creates 3 variants in Paper
  4. Tetiana picks a direction
  5. Orchestrator routes to Design System to build the components in code
  6. Done
```

### Setup Instructions

To set up the multi-agent environment:

1. **Create the agents directory:**
   ```
   /agents/
     visual-buddy.md
     builder.md
     design-system.md
   ```

2. **Create CLAUDE.md in project root:**
   Contains orchestrator instructions, project context, and pointers to agent skill files and CONTRACTS.md.

3. **Contracts document:**
   ```
   /CONTRACTS.md
   ```
   Contains: file structure map, naming conventions, component API patterns, design tokens, boundaries between agents.

4. **Claude Code sub-agent spawning:**
   - The orchestrator uses Claude Code's Agent tool to spawn sub-agents
   - Each spawn includes: the relevant skill file content, the task description, and any necessary file contents or context
   - Sub-agent results are collected by the orchestrator

5. **First run sequence:**
   - Orchestrator reads CLAUDE.md and CONTRACTS.md
   - Spawns Visual Buddy to explore initial UI layout options
   - Tetiana picks a direction
   - Spawns Design System agent to scaffold the component library structure
   - Spawns Builder agent to scaffold the app and canvas pipeline
   - Iterates from there

---

## Reference Material

### Visual References
- **Tulip ASCII overlay (luminance + screen blend)** — Screenshot 2026-03-26 215244.png (left) — PRIMARY "hero" target
- **Flower ASCII overlay (dense blocky)** — Screenshot 2026-03-26 215244.png (right) — denser alternative style
- **Cat made of letters** — Pinterest reference in Paper scratchpad — shows opaque fill style
- **Test photos in Paper scratchpad** — rose, statue, cityscapes, architecture, Midjourney images
- **UI reference** — ascii-magic.com (similar tool, but does full-image conversion — we do object-isolated)
- **Mountain/flowers full-image ASCII** — this is the full-image approach we're NOT doing — we isolate objects instead

### Aesthetic Targets
- The glowing, luminous quality of the tulip reference (image 1 left) is the primary "hero" output to aim for
- Blend mode `screen` or `color-dodge` over the original photo creates this effect
- Characters colored by underlying pixel + lighter-than-image blend = light map aesthetic

---

## Scope

See `MILESTONES.md` for detailed feature breakdown per milestone.

### Milestone 1: "It works and looks impressive"
Core pipeline + luminance/random fill + basic controls + PNG export.

### Milestone 2: "More creative range"
Edge detection + gradient flow strategies, animation, GIF export, collage mode (swap background, drag/reposition ASCII subject), SVG export.

### Future (v2+)
- Multiple object selection with independent settings
- Video (MP4) export
- Additional animation modes
- Preset save/load (save parameter combinations)
- Shareable URLs with encoded parameters
- Undo/redo for mask editing
