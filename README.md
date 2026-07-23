# Mobile Mockups Skill

**Design your mobile app in plain HTML instead of Figma — an
[Agent Skill](https://agentskills.io) for Claude Code, Codex, Cursor,
Qwen Code, and any agent that reads `SKILL.md`.**

Ask your agent to *"mock up the onboarding flow"* and it interviews you,
builds a small design system for your product, then produces phone-frame
screens you review as screenshots in a real browser — real copy, light and
dark themes, and a live accessibility audit included.

<p align="center">
  <img src="https://raw.githubusercontent.com/euzharkov/blog/main/mobile-mockups-skill-examples/ios-calendar/ios-calendar-mockup-demo.gif" alt="Animated tour of the iOS Calendar fidelity study" width="49%" />
  <img src="https://raw.githubusercontent.com/euzharkov/blog/main/mobile-mockups-skill-examples/gmail/gmail-mockup-demo.gif" alt="Animated tour of the Gmail fidelity study" width="49%" />
</p>

Extracted from a real production workflow, where an entire mobile app was
designed this way before a line of it was implemented. The full story is in
the article. <!-- TODO: link the article when published -->

## Quick start

One command, works with any supported agent
([70+ of them](https://github.com/vercel-labs/skills)):

```bash
npx skills add euzharkov/mobile-mockups-skill
```

Then just ask for design work:

> mock up the onboarding flow for my app

The skill triggers on its own, interviews you about your product, and builds
from there.

<details>
<summary><strong>Manual install</strong></summary>

The skill is this repository. Clone or download it, then copy the folder
into your agent's skills directory as `mobile-mockups-skill`:

| Agent       | Per project                              | Personal (all projects)             |
| ----------- | ---------------------------------------- | ----------------------------------- |
| Claude Code | `.claude/skills/mobile-mockups-skill/` | `~/.claude/skills/mobile-mockups-skill/` |
| Codex CLI   | `.codex/skills/mobile-mockups-skill/`  | `~/.codex/skills/mobile-mockups-skill/`  |
| Cursor      | `.cursor/skills/mobile-mockups-skill/` | —                                   |
| Qwen Code   | `.qwen/skills/mobile-mockups-skill/`   | `~/.qwen/skills/mobile-mockups-skill/`   |

Any other agent that supports the [Agent Skills](https://agentskills.io)
standard works the same way — put the folder wherever it discovers `SKILL.md`
files. Codex and Qwen Code load skills at startup, so restart the agent after
installing.

</details>

## See it in action

Live examples, straight from this repo:

- **[Starter canvas](https://euzharkov.github.io/mobile-mockups-skill/assets/)**
  — what the skill scaffolds into your repo: a foundations page (palette,
  type ramp, spacing, contrast audit) and a small example flow for
  "Fieldnote", a fictional journaling app.
- **[iOS Calendar study](https://euzharkov.github.io/blog/mobile-mockups-skill-examples/ios-calendar/)**
  — a fidelity study recreating Apple's iOS Calendar from real simulator
  captures: day/year/month/list views and the iOS 26 "New" sheet, with
  Apple's own sub-AA contrast choices flagged (not hidden) by the audit.
  [Read more](https://github.com/euzharkov/blog/tree/main/mobile-mockups-skill-examples/ios-calendar).
- **[Gmail iOS study](https://euzharkov.github.io/blog/mobile-mockups-skill-examples/gmail/)**
  — inbox rows, the overlay drawer in both states, a branded email thread,
  compose with a simulated iOS keyboard, and the account sheet — every piece
  of mail data replaced by fictional equivalents.
  [Read more](https://github.com/euzharkov/blog/tree/main/mobile-mockups-skill-examples/gmail).

Or run the examples locally — they live in the
[blog repo](https://github.com/euzharkov/blog) and the folder is
self-contained: clone it and start any static server inside
`mobile-mockups-skill-examples/`. With Node:

```bash
npx serve -l 4680
```

or with Python:

```bash
python3 -m http.server 4680
```

then open `http://localhost:4680/` — a little home screen linking both
studies. (The starter canvas lives in this repo — serve it the same way and
open `http://localhost:4680/assets/`.)

## Why HTML instead of Figma?

When your designer is an agent, a design tool is the wrong medium. Static
HTML is the right one:

- **Agents are native here.** They read, write, and diff HTML/CSS fluently —
  no plugin API, no export step, no screenshots-of-canvases.
- **It's versionable.** Design reviews become PRs. Design history becomes
  git history.
- **The style guide can't lie.** The auto-generated foundations page (your
  live style guide) renders palette, type
  ramp, spacing, and radii live from the actual CSS — plus a live WCAG
  contrast audit for both themes. Drift is impossible.
- **Copy is real.** Screens carry shippable words, so the mockup doubles as
  the copy spec. Production takes text from it 1:1.
- **Handoff is honest.** Design values map to your production theme 1:1 by
  role name — sizes in the mockup are the sizes you ship (mockup px =
  production pt). No redlines, no eyedropper.
- **No build step.** A directory of static files and any static server.

## How it works

The method has two layers, nothing else:

1. **A design system** — `design-system.css`: flat semantic values (color
   roles, type roles, spacing, radii, one dark override block) plus
   `DESIGN-SYSTEM.md`, the written contract that keeps agents honest across
   sessions.
2. **Screens** — one HTML page per flow area, sections as flows, phone
   frames as states.

There is deliberately **no component library** in between — mockup classes
are presentation hooks, so your production app is never pressured to mirror
a fake component API.

When you ask for design work, the agent:

1. **Interviews you** — product, audience, three tone words, platform, brand
   constraints.
2. **Scaffolds** — copies the starter canvas into your repo and wires up a
   static server.
3. **Builds the design system** — translates your tone words into role
   values for both themes, iterates until the contrast audit is green, and
   shows you foundations screenshots for sign-off.
4. **Builds screens** — flow by flow, with a states discipline (empty /
   loading / error / edge, not just the happy path) and real copy.
5. **Reviews its own work** — screenshots each section in light and dark,
   critiques against a seven-point checklist, iterates with you.
6. **Hands off** — exports values by role into your production theme (React
   Native, Tailwind, plain CSS) and keeps the mockup alive as the source of
   truth.

## The workbench

`mockup.js` + `mockup.css` render the gallery around your screens:

- phone shell at the 390×844 iPhone baseline, authored 1:1 with production
  points
- light/dark toggle and phone zoom, all URL-driven
  (`?theme=dark&zoom=70`), so every screenshot is reproducible
- **solo mode** (`?solo=section-id`): renders one section only — fast loads
  and reliable captures on long pages
- auto-generated foundations section + WCAG 2.1 contrast audit, read live
  from your `design-system.css`

## Changing the design system

The agent builds the design system for you in steps 1–3 above — you never
write it by hand. (The full procedure it follows lives in
[references/design-system.md](references/design-system.md).)

Changing it later is the easy part — that's the point of role-based values:

- **Ask the agent.** *"Make it warmer"*, *"swap the accent to our brand
  green"*, *"body text feels small"* — it adjusts the roles in
  `design-system.css`, re-checks the audit, and updates `DESIGN-SYSTEM.md`.
- **Or edit `design-system.css` yourself.** Screens only ever consume roles
  (`--canvas`, `--accent-interactive`, `.t-body`…), so changing a role's
  value restyles every screen at once. Refresh the foundations page to
  re-run the contrast audit after any change.

Two rules keep the system healthy: if you add a color role, add its
foreground/background pairs to the audit in the same edit; and keep
`DESIGN-SYSTEM.md` in sync — it's the contract the agent reads at the start
of every session, so a value changed only in CSS will drift back.

## What's in the box

```
mobile-mockups-skill/
├── SKILL.md                    # the workflow the agent follows
├── references/
│   ├── design-system.md        # interview → roles → dark theme → contrast
│   ├── screens.md              # flows, states discipline, copy, annotations
│   ├── review.md               # serving, solo screenshots, critique checklist
│   └── handoff.md              # mapping roles into your production theme
├── assets/                     # the starter canvas (copied into your repo)
│   ├── design-system.css       # the flat design values + .t-* type roles
│   ├── DESIGN-SYSTEM.md        # the written contract (template)
│   ├── mockup.css              # workbench chrome
│   ├── mockup.js               # workbench engine (foundations, audit, solo)
│   ├── index.html              # starter canvas + example flow
│   └── flow.html               # blank page for new flow areas
└── evals/evals.json            # test prompts for skill evaluation
```

The worked examples live in the blog repo, in
[`mobile-mockups-skill-examples/`](https://github.com/euzharkov/blog/tree/main/mobile-mockups-skill-examples)
— deliberately outside this repo, so installers download the ~110 KB skill,
not 10 MB of demo media.

## License

MIT
