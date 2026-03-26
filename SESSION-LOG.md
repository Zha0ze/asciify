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
