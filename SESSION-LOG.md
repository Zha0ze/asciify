# ASCIIFY — Session Log

This file tracks what happened in each working session so we can pick up where we left off.

---

## Session 1 — 2026-03-26

### What we did
- Reviewed all project docs (spec, skills, contracts, gemini prompts)
- Made key technical decisions
- Updated asciify-spec.md with all decisions
- Created MILESTONES.md with M1/M2 feature breakdown (including collage mode for M2)
- Rewrote all agent skill files — new structure in /agents/
- Created CLAUDE.md (orchestrator instructions, read automatically)
- Reviewed reference photos in Paper scratchpad (tulips, cat, rose, statue, cityscapes, Midjourney images)
- Reviewed visual reference screenshot (tulip glow = hero target)
- Checked ascii-magic.com as UI reference (different tool — full image conversion vs our object-isolated approach)
- Initialized git repo, pushed planning docs to https://github.com/Zha0ze/asciify

### Decisions made
- **Framework:** Vite + React + TypeScript (not Next.js — SPA, no server needed)
- **Hosting:** Vercel free tier (all client-side, no backend)
- **Gemini dropped:** Tetiana is the Art Director, Claude Code does code review
- **Agent roles:** Orchestrator + Visual Buddy (Paper) + Design System (code + design tool, teaches) + Builder (code)
- **Subject detection:** Client-side ML (`@imgly/background-removal`) + brush refinement
- **Design system workflow:** Paper first (speed) → Figma library later (proper components/variants/tokens)
- **Teaching mode:** Always on — explain code↔Figma parallels
- **Skills are living docs:** Rewrite after first feature, then iterate based on experience
- **Collage feature added to M2:** SVG export, swap background, drag/reposition ASCII subject
- **Public repo:** Good for portfolio — shows process and architecture thinking

### Files created/modified
- Created: CLAUDE.md, CONTRACTS.md (rewritten), MILESTONES.md, SESSION-LOG.md, .gitignore
- Created: agents/visual-buddy.md, agents/builder.md, agents/design-system.md
- Updated: asciify-spec.md (framework, agent roles, scope, references)
- Deleted: orchestrator.md, gemini-prompts.md, old builder.md, old design-system.md

### Current state
- **Planning phase complete** — no code written yet
- All agent skill files written (Round 1 — will evolve)
- Git repo initialized and pushed to GitHub

### Next session should
- Scaffold the Vite + React + TypeScript project
- Install core dependencies (Tailwind, shadcn/ui)
- Set up the basic file structure from CONTRACTS.md
- Optionally: Visual Buddy explores initial UI layout in Paper

---

## Session 2 — 2026-03-27

### What we did
- Scaffolded Vite + React + TypeScript project (npm, Tailwind CSS v4)
- Created full directory structure from CONTRACTS.md (engine/, features/, components/, hooks/, styles/, types/, lib/)
- Minimal App.tsx with dark theme placeholder
- Dev server confirmed working at localhost:5173
- Updated visual-buddy.md skill file — added /rams for accessibility review
- Visual Buddy explored 3 panel style directions in Paper (Monospace Terminal, Clean Studio, Floating Cards)
- Tetiana rejected Terminal style — deleted from Paper
- Visual Buddy refined remaining 2 styles + created new minimalist variant (Style D)
- Final 3 variants in Paper: Clean Studio (purple), Floating Cards (amber), Minimalist (no accent, grayscale)

### Panel style exploration (in Paper file "ASCIIFY")
- **Style B "Clean Studio"** (artboard 7P-0) — purple accent, flat sections, DevTools feel
- **Style C "Floating Cards"** (artboard A4-0) — amber accent, card-based collapsible sections
- **Style D "Minimalist"** (artboard IE-0) — no accent color, grayscale, inline layout, most compact, best accessibility
- References used: nodes 1-0, 2-0, 3-0 (tuning panel screenshots from other tools)
- **Status:** Starting point for design — needs Tetiana's direction on which to pursue

### Files created/modified
- Created: src/ directory structure with .gitkeep files
- Created: src/App.tsx (minimal dark placeholder), src/index.css (Tailwind import)
- Modified: vite.config.ts (added Tailwind plugin), index.html (fixed title)
- Modified: .gitignore (added .claude/, merged Vite defaults)
- Modified: agents/visual-buddy.md (added /rams skill)
- Removed: Vite boilerplate (App.css, assets/, public/, README.md)

### Decisions made
- **Use AskUserQuestion tool** for presenting choices (Tetiana's preference — saved to memory)
- **Panel style direction:** 3 options on the table, no final pick yet

### Current state
- **Project scaffolded** — Vite + React + TS + Tailwind running
- **shadcn/ui not yet installed** — waiting for style direction to finalize
- **3 panel designs in Paper** — ready for Tetiana to choose/refine
- No feature code written yet

### Next session should
- Tetiana picks a panel style direction (or mixes elements)
- Install shadcn/ui and configure theme based on chosen style
- Design System agent builds first Ascii* components
- Begin M1 feature work (upload → detect → render pipeline)
