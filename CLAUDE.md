<!-- Japanese version: CLAUDE.ja.md -->

# Project Conventions

## Deliverable
- A single `index.html` file (no external dependencies, no CDN, no build step)
- Must be publishable as-is on GitHub Pages

## Non-negotiable requirements
- This demo itself must be WCAG 2.2 AA compliant (it would be a fatal irony
  for an a11y demo to be inaccessible)
- Support `prefers-reduced-motion` / `prefers-color-scheme`
- Must work without breaking under real screen readers (VoiceOver/NVDA)
- Fully bilingual in Japanese/English (including dynamically switching the
  `lang` attribute)

## Verification commands
- `npx serve .`                            # local preview
- `npx axe http://localhost:3000 --exit`    # audit (pass = 0 violations)

## Git conventions
- Always commit at each generation/fix stage (`v1`, `v1-audit`, `v2`, ...)
- Commit messages must state which a11y violation was fixed
