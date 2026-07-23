---
name: mobile-mockups-skill
description: >-
  Design product UI in plain HTML instead of Figma: build a per-project design
  system (semantic color roles, type roles, spacing — as CSS custom
  properties with a live WCAG contrast audit), then compose phone-frame
  mockup screens that an AI agent can create, screenshot, critique, and
  iterate on directly in the browser. Use whenever the user wants mockups,
  wireframes, screen designs, UI concepts, a design system, a style guide, a
  design review of planned screens, or says things like "mock up this
  feature", "design the onboarding", "how should this screen look", "explore
  some UI directions", or wants to design before implementing — even if they
  never say the word "mockup" or mention HTML. Also use when asked to extend
  or restyle an existing mockup directory built with this method.
---

# Mobile Mockups

Design in the medium you ship. This skill turns a plain directory of static
HTML into a Figma replacement that is *better* for agent-driven work: you can
read it, write it, diff it, version it, screenshot it, and audit its
accessibility — none of which an agent can do inside a design tool.

The method has exactly two layers, and holding that line is what keeps it
fast:

1. **Flat shared design values** — `design-system.css`: semantic colors, type
   roles (`.t-*` classes), spacing, radii. One file, both themes.
2. **Screens** — HTML pages that compose those values with page-local CSS and
   JS template helpers. Deliberately **no component library, no tokens-of-
   tokens, no build step**: mockup classes are presentation hooks, and
   production is never pressured to mirror a fake component API.

A written contract (`DESIGN-SYSTEM.md`) makes the system enforceable across
sessions, and the workbench (`mockup.js`) auto-renders a foundations page —
palette, type ramp, spacing, radii, and a live WCAG contrast audit — straight
from the CSS, so the style guide cannot drift from reality.

## Phase 0 — Detect state

Look for an existing mockup directory (`design-system.css` +
`DESIGN-SYSTEM.md` + `mockup.js` together). If found: read
`DESIGN-SYSTEM.md` fully, respect it as the contract, and jump to whichever
phase the request needs (usually Phase 4 for new screens, Phase 3 for
restyling). Never rebuild a system that exists; never normalize old screens
you weren't asked to touch.

If starting fresh, continue in order. Phases 1–3 end with the user approving
the foundations; phases 4–5 loop per flow.

## Phase 1 — Discovery

Read `references/design-system.md` § Discovery, ask its interview (one
batched round: product & audience, three tone words, platform, existing
brand, light+dark?, context of use, icon library). If the user is unavailable
or brief, pick defaults from the product context and say which assumptions
you made.

## Phase 2 — Scaffold

1. Copy every file from this skill's `assets/` directory into the project —
   default `design/mockup/` (or `mockup/` at the repo root, or where the user
   prefers): `design-system.css`, `mockup.css`, `mockup.js`, `index.html`,
   `flow.html`, `DESIGN-SYSTEM.md`.
2. Wire up serving (any static server; foundations need http, not `file://`)
   — see `references/review.md` § Serving for the launch-config pattern.
3. Open the page once to confirm the starter renders: foundations plus one
   example section ("Fieldnote").

The starter ships with a neutral placeholder system — warm paper, graphite
ink, indigo accent. It exists so the canvas renders before Phase 3; it is not
a recommendation.

## Phase 3 — Design the system

Follow `references/design-system.md` §2–7:

1. Translate the discovery answers into role values in `design-system.css` —
   surfaces, ink scale, the interactive/decorative accent split, status
   colors, both themes. Adjust type roles, spacing, radii to the product.
2. Update `contrastPairs` in `index.html` to cover every fg/bg pair the
   product will use.
3. Fill `DESIGN-SYSTEM.md` — it's a template with `{{placeholders}}`; the
   product-context paragraph and role tables must reflect the real system.
4. Open `?solo=foundations`, light and dark. Iterate until the contrast
   audit is green and the palette reads as one family.
5. Show the user foundations screenshots; get sign-off before building
   screens. Changing a role later is cheap; discovering the temperature is
   wrong after twenty screens is not.

## Phase 4 — Build screens

Follow `references/screens.md`. The short version:

- One page per flow area (`flow.html` is the blank); sections are flows,
  phones are states, authored as JS template literals via `phone()` /
  `tabbar()` and page-local helpers.
- Screens compose design-system values only — no new hexes, no ad-hoc font
  sizes. Type is `.t-*` classes on the markup.
- **States discipline**: mock empty, loading, error, and edge-content states,
  not just the happy path — that's where design actually happens.
- **Copy is spec**: real words in the product voice, plausible data, no
  lorem ipsum. Production takes user-facing text from the mockup 1:1.
- **Annotate**: column labels say what each state shows; `note()` cards carry
  the why. Decisions live in the mockup, not in chat history.
- Delete the "Fieldnote" example section once real sections exist.

## Phase 5 — Review loop

Follow `references/review.md` after every meaningful chunk: check the
console, screenshot each new section via `?solo=<id>` (light and dark), run
the seven-point critique (hierarchy, rhythm, type honesty, contrast, touch
targets, states coverage, copy), fix in source, then show the user curated
screenshots with a specific question.

Screenshot long pages section-by-section with `?solo=` — full-page deep
scrolls freeze headless captures. When the user asks for a global change
("warmer", "bigger text"), change role values in `design-system.css`, not
individual screens.

## Phase 6 — Handoff (when asked)

When the user moves to implementation, follow `references/handoff.md`:
values and type roles map 1:1 by role name into the production theme, copy
transfers verbatim, layout transfers by intent, and mockup class names/DOM
transfer never. Keep the mockup alive as the design source of truth —
design changes go mockup-first, implementation deviations get back-ported by
role and intent.

## The rules that hold the method together

- Two layers only. The moment a "shared component" tier appears, delete it.
- Semantic roles, never colors by appearance. Interactive accent ≠
  decorative accent; the hexes may swap between themes.
- Mockup px = production pt, 1:1 at 390×844. No scaled mental math.
- Every value change keeps `design-system.css`, `DESIGN-SYSTEM.md`, and
  `contrastPairs` in sync in the same edit.
- The contrast audit is a gate, not a dashboard: fails get fixed in role
  values, never hidden.
- `mockup.css` / `mockup.js` are the workbench — raw colors allowed, product
  values forbidden; you should rarely edit them.

## Files

| Path | What it is |
| --- | --- |
| `assets/design-system.css` | Starter Layer-1 values + `.t-*` roles + dark block (placeholder palette) |
| `assets/mockup.css` | Workbench chrome: canvas, phone shell, rails, notes, foundations styles |
| `assets/mockup.js` | Engine: `phone()`/`tabbar()`/`note()`, `renderMockupPage()`, foundations auto-doc, WCAG audit, `?solo/theme/zoom` |
| `assets/index.html` | Starter canvas with foundations + deletable "Fieldnote" example |
| `assets/flow.html` | Blank template for additional flow pages |
| `assets/DESIGN-SYSTEM.md` | Contract template with `{{placeholders}}` |
| `references/design-system.md` | Phase 1+3 in depth: interview, roles, dark theme, type, contrast |
| `references/screens.md` | Phase 4 in depth: anatomy, helpers, states, copy, annotations |
| `references/review.md` | Phase 5 in depth: serving, solo screenshots, critique checklist |
| `references/handoff.md` | Phase 6 in depth: role mapping, theme export, keeping mockups alive |
