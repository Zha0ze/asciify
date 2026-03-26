# ASCIIFY — Session Log

This file tracks what happened in each working session so we can pick up where we left off.

---

## Session 1 — 2026-03-26

### What we did
- Reviewed all project docs (spec, skills, contracts, gemini prompts)
- Made key technical decisions (see below)
- Updated asciify-spec.md with all decisions
- Created MILESTONES.md with M1/M2 feature breakdown
- Updated orchestrator.md with new agent roles
- Reviewed reference photos in Paper scratchpad

### Decisions made
- **Framework:** Vite + React + TypeScript (not Next.js — SPA, no server needed)
- **Hosting:** Vercel free tier (all client-side)
- **Gemini dropped:** Tetiana is Art Director, Claude Code does code review
- **Agent roles:** Orchestrator + Visual Buddy (Paper) + Design System (code + design tool, teaches) + Builder (code)
- **Subject detection:** Client-side ML (`@imgly/background-removal`) + brush refinement
- **Design system workflow:** Paper first (speed) → Figma later (proper library)
- **Learning mode:** Always on — explain code↔Figma parallels
- **Collage feature:** Added to M2 — swap background, drag/reposition ASCII subject, SVG export

### Current state
- Planning phase — no code written yet
- Agent skill files need review and rewrite
- CLAUDE.md needs to be created
- Multi-agent setup not yet configured

### Next session should
- Finalize agent skill files
- Create CLAUDE.md
- Set up the Vite + React project
- Begin scaffolding
