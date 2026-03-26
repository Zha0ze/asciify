# CONTRACTS.md — Shared Conventions

This document is the single source of truth for conventions that ALL agents follow. Read this before starting any task.

---

## File Structure

```
/
├── CLAUDE.md               — orchestrator instructions (read automatically)
├── CONTRACTS.md            — this file (shared conventions)
├── MILESTONES.md           — feature breakdown per milestone
├── SESSION-LOG.md          — what happened each session
├── agents/
│   ├── visual-buddy.md     — Visual Buddy skill file
│   ├── builder.md          — Builder skill file
│   └── design-system.md    — Design System skill file
├── src/
│   ├── app/                — page-level composition
│   ├── engine/             — canvas pipeline, rendering core
│   │   ├── strategies/     — fill strategy modules
│   │   ├── pipeline.ts     — main rendering pipeline
│   │   ├── compositor.ts   — blend mode compositing
│   │   ├── colorSampler.ts — pixel color extraction
│   │   └── densitySorter.ts — character density calculation
│   ├── features/           — feature-specific logic
│   │   ├── mask/           — ML detection + brush editing
│   │   ├── export/         — PNG/GIF/SVG export
│   │   └── animation/      — animation system (M2)
│   ├── components/
│   │   ├── ui/             — Design System primitives (shadcn wrappers)
│   │   └── features/       — composed feature components
│   ├── hooks/              — React hooks bridging engine to UI
│   ├── styles/             — tokens, theme, global CSS
│   ├── types/              — shared TypeScript types
│   └── lib/                — utilities, helpers
└── tests/
    └── engine/             — tests for core engine logic
```

---

## Naming Conventions

### Files
- Engine modules: `camelCase.ts` (e.g. `luminanceMapping.ts`, `colorSampler.ts`)
- React components: `PascalCase.tsx` (e.g. `AsciiSlider.tsx`, `ControlsPanel.tsx`)
- Hooks: `use[Name].ts` (e.g. `useCanvasRenderer.ts`)
- Types: `types.ts` per directory
- Tests: `[module].test.ts`

### Components
- Wrapped primitives: `Ascii[ComponentName]` (e.g. `AsciiSlider`, `AsciiSelect`)
- Feature components: descriptive name, no prefix (e.g. `ControlsPanel`, `MaskToolbar`)

### Variables and Functions
- camelCase for variables and functions
- PascalCase for types, interfaces, components
- UPPER_SNAKE_CASE for constants
- Prefix boolean props with `is`, `has`, `should` (e.g. `isAnimating`, `hasCustomCharset`)

---

## Boundaries

### Builder Owns:
- Everything in `/src/engine/`, `/src/features/`, `/src/hooks/`, `/src/app/`
- Page composition and layout logic
- Integration of Design System components into features
- Performance optimization of rendering pipeline

### Design System Owns:
- Everything in `/src/components/`, `/src/styles/`
- Component API design (props, variants, defaults)
- Design token definitions
- Visual consistency enforcement
- Design tool components (Paper phase 1, Figma phase 2)

### Visual Buddy Owns:
- Exploration artboards in Paper
- Layout and aesthetic variant drafts
- Does NOT own any code files

### Shared:
- `/src/types/` — either Builder or Design System can add shared types, but should not modify types created by the other without orchestrator approval
- `/src/lib/` — either agent can add utilities

### Conflict Resolution:
If Builder needs a component change that Design System disagrees with, the orchestrator decides. Tetiana has final say on all visual decisions.

---

## Component Contract

### How Builder Requests Components
Builder tells the orchestrator:
```
Need: AsciiSlider
Props: label (string), min (number), max (number), value (number), onChange (function), step? (number), showValue? (boolean)
Behavior: horizontal slider with label left, value right, slider track below
Context: used in Controls Panel for font size, opacity, density parameters
```

### How Design System Delivers
Design System builds the component and reports:
```
Created: /src/components/ui/AsciiSlider.tsx
Props: AsciiSliderProps exported
Defaults: step=1, showValue=true
Usage: <AsciiSlider label="Font Size" min={4} max={24} value={8} onChange={setValue} />
Figma parallel: "This is like a Slider component in your Figma library — one main component with properties for label, min, max, default value."
```

---

## Design Tokens (Initial — Will Evolve)

To be established by Visual Buddy exploration + Design System agent. Starting structure:

```
colors:
  background: TBD (dark theme default)
  surface: TBD
  border: TBD
  text-primary: TBD
  text-secondary: TBD
  text-muted: TBD
  accent: TBD

typography:
  font-family: TBD (likely Geist Mono)
  font-size-base: TBD
  line-height-base: TBD

spacing:
  unit: 4px
  scale: 4, 8, 12, 16, 20, 24, 32, 40, 48

radii:
  sm: 4px
  md: 6px
  lg: 8px
```

---

## Git Conventions

- Branch naming: `feature/[short-description]` (e.g. `feature/luminance-strategy`)
- Commit messages: imperative tense, concise (e.g. "Add luminance mapping strategy")
- One feature per branch when possible

---

## Quality Gates

Before any feature is considered done:
1. Builder confirms it works (manual testing in browser)
2. Orchestrator reviews the code
3. Tetiana approves the visual output (she is the Art Director)
4. Design System confirms component usage is correct (for UI features)

---

## Updating This Document

Any agent can propose changes to CONTRACTS.md by reporting to the orchestrator. The orchestrator reviews and applies the change. No agent modifies this file directly without orchestrator approval.
