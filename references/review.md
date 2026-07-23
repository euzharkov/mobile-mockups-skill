# Reviewing and iterating

A mockup you haven't looked at is a guess. Review in a real browser, as
screenshots, the way the user will judge it.

## Serving

The foundations renderer reads CSS rules, which browsers block on `file://` —
always serve over http.

- **Claude Code with the browser pane**: add a launch entry once and use
  `preview_start {name: "mockup"}`:

  ```json
  {
    "name": "mockup",
    "runtimeExecutable": "sh",
    "runtimeArgs": ["-c", "python3 -m http.server \"${PORT:-4680}\" --directory PATH/TO/mockup"],
    "port": 4680,
    "autoPort": true
  }
  ```

- **Anywhere else**: `python3 -m http.server 4680 --directory PATH/TO/mockup`
  or `npx serve PATH/TO/mockup`. Plain static files — no build step, ever.

## Deterministic URLs

The canvas is controlled entirely by URL, so every screenshot is
reproducible and nameable:

| URL | Shows |
| --- | --- |
| `/?solo=foundations` | design system + contrast audit |
| `/?solo=<section-id>` | one flow section |
| `/?solo=<id>&theme=dark` | same, dark |
| `/onboarding.html?solo=…` | sections on other pages |
| `/?zoom=100` | phones at full 390px width |

**Screenshot section by section via `?solo=`, not by scrolling a full page.**
Long mockup pages freeze headless screenshots during deep scrolls; solo mode
renders only the requested section, so captures stay fast and reliable. This
is a lesson paid for in a real project — don't relearn it.

## The review loop

After each meaningful chunk (a section, a new page):

1. Reload and check the browser console — template-literal typos surface as
   errors, not blank screens you'd misread as style choices.
2. Screenshot each new section via `?solo=`, light and dark.
3. Critique against the checklist below. Fix in source, not in your head.
4. Re-screenshot, then show the user the good candidates with one line per
   screenshot on what to look at.

### Self-critique checklist

Work through it honestly — screens that "look done" on first render rarely
survive all seven:

1. **Hierarchy** — squint (or blur mentally): does the most important thing
   read first? Is there exactly one primary action per screen?
2. **Rhythm** — spacing on the 4px scale, aligned to a consistent inset; no
   accidental 13px gaps, no crowding at card edges.
3. **Type honesty** — every text node has a role class; no browser-default
   sizes leaking; no role metrics overridden to force a fit.
4. **Contrast** — audit green; secondary text still legible on tinted fills;
   dark theme checked, not assumed.
5. **Touch** — interactive things ≥ 44pt, spaced enough to miss-proof.
6. **States** — does the section show empty/error/edge, or only the happy
   path? (See the states discipline in screens.md.)
7. **Copy** — real words, product voice, data that makes sense together.

### Reviewing with the user

Present 2–4 screenshots at a time with a specific question ("Is the empty
state too playful for this audience?"), not "thoughts?". When the user gives
a direction ("warmer", "less dense"), change the ROLE values in
`design-system.css` first and re-screenshot — one edit restyles every screen,
which is the entire argument for the token layer. Only if the note is about
one screen does the fix belong in that screen's CSS.

Record the outcome of real decisions as section notes in the page — the
mockup is the design memory, not the chat transcript.

## When something looks wrong

Debug in this order: (1) console errors — an unclosed backtick kills a whole
page render; (2) inspect the element — is it consuming the var you think it
is, or a raw value; (3) check the screen in isolation with `?solo=` before
blaming the canvas; (4) only then adjust CSS. Resist fixing a symptom with an
inline style — find which layer owns the mistake.

One silent killer to know: writing `*/` inside a CSS comment (easy to do in
prose like `--font-*/--weight-*`) terminates the comment early, and the
leftover text swallows the next rule — with no console error. The symptom is
the foundations page suddenly reporting unresolved values or system fonts;
the fix is rewording the comment. The audit exists to catch exactly this
class of quiet breakage.
