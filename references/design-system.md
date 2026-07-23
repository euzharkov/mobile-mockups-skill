# Building the design system

The design system is two files: `design-system.css` (the values) and
`DESIGN-SYSTEM.md` (the contract). Everything else consumes them. Get these
right before drawing a single screen — every later decision gets cheaper.

## 1. Discovery

Ask the user, in one batched round (don't interrogate — if answers are thin or
the user is away, choose sensible defaults from the product context and state
the assumptions you made):

1. **What is the product, and for whom?** Domain and audience drive everything:
   a finance dashboard for analysts and a sleep app for new parents cannot
   share a system.
2. **Three tone words.** "Calm, warm, trustworthy" vs "sharp, dense,
   technical" — these translate directly into temperature, contrast, radius,
   and type choices (see the translation table below).
3. **Platform and frame.** Phone (default, 390×844), tablet, desktop web?
   This sets the shell and the density expectations.
4. **Existing brand?** A logo color or an existing palette is an input, not a
   veto: it usually becomes one accent, not the whole system.
5. **Light and dark?** Scaffold both unless the user says otherwise — dark is
   cheap now (one override block) and expensive to retrofit.
6. **Context of use.** Glanced at in line at a store, or lived in for
   eight-hour sessions? This decides type sizes and spacing generosity.
7. **Icon library.** Ask — never dictate. Offer common options (Phosphor,
   Lucide, Material Symbols, Heroicons, emoji/inline SVG, or none) and note
   trade-offs only if asked. Record the choice, its default weight, and its
   active-state variant in DESIGN-SYSTEM.md; the starter pages load Phosphor
   purely so the example renders — swap the `<script>` tag to match the
   choice.

### Tone → system translation

| Tone words like… | Palette | Shape | Type |
| --- | --- | --- | --- |
| calm, warm, human, gentle | warm neutrals (paper, cream), muted accent, low saturation | larger radii (16–20 card) | serif or humanist display, roomy line heights |
| sharp, technical, precise | cool neutrals (slate, graphite), one saturated accent | small radii (6–10) | grotesk throughout, tighter leading |
| playful, energetic | brighter canvas, 2 accents max, higher chroma | pill-heavy | rounded sans, generous sizes |
| premium, editorial | near-black ink, off-white paper, restrained metallic accent | mixed: sharp cards, pill buttons | high-contrast serif display |

Never pick colors by appearance alone; pick roles, then find hexes that fill
the roles at the required contrast.

## 2. The color roles

Fill the role set in `design-system.css`. The starter roles cover most
products; rename or extend them to fit the domain, and keep names semantic
(`--canvas`, not `--beige`):

- **Surfaces**: `--canvas` (app background), `--card` (raised), `--functional`
  (quieter: form fields, secondary containers), `--selected` (chosen/pressed),
  `--accent-tint` (soft highlight wash). Canvas and card must remain visibly
  distinct in both themes — if they blur together, elevation dies.
- **Ink scale**: `--ink-900/600/400` for primary/secondary/tertiary text.
  Every step must pass its contrast target on canvas AND card.
- **Accent, split by role**: `--accent-interactive` (carries text and active
  states, must pass 4.5:1) and `--accent-decorative` (fills, illustration,
  never carries text). In dark mode the two hexes usually swap roles so the
  interactive one stays readable — this swap is the single most useful trick
  in the file.
- **Status**: `--danger` at minimum — serious, not alarmist; a full
  fire-engine red makes users feel at fault in products where errors are
  recoverable. If the domain needs a status scale (good/okay/bad), give each
  status a triad: solid mark, soft fill, and an ink that is legible ON the
  soft fill: `--status-good`, `--status-good-soft`, `--status-good-ink`.
  Resist encoding "bad" as red when the domain is sensitive (health, money) —
  a cool dark tone reads serious without alarm.
- **Controls**: `--border-subtle` / `--border-control`, `--btn-fill` /
  `--btn-label`.

### Dark theme

Dark is a re-resolution of roles, not `filter: invert()`. Surfaces rise
slightly from black (#17-ish, not #000), ink softens from white, soft fills
often become translucent (`rgba`) so cards keep their ground color, and the
accent pair swaps. The override block is scoped to screens —
`.theme-dark .screen, .screen.night { … }` — so the gallery workbench stays
light and annotations stay readable while screens flip.

If the product is genuinely single-theme, delete the block; the foundations
page detects its absence and hides the dark column.

## 3. Typography

Define roles as `.t-*` classes — a role owns family, weight, size, line
height, tracking, and default color together. The starter's ten roles
(display, title, heading, subheading, body-lg, body, callout, caption, link,
on-accent) fit most products; merge or rename rather than growing the list.
Sizes are production points at the 390×844 baseline — mockup px = production
pt, 1:1, no mental conversion.

Rules that keep a type system alive:

- At most two families: one display (optional, for editorial character), one
  UI/body. Load at most three weights and never synthesize others.
- Nothing relies on browser-default `h1`–`h6`/`strong`/button styling — roles
  are always assigned explicitly.
- Screens may override alignment, wrapping, or an explicitly semantic color —
  never a role's metrics "to make it fit". If a layout needs different
  metrics, that's either a different role or a layout problem.
- For midlife or accessibility-sensitive audiences, keep body at 17px+ and be
  generous with line height; cramped type reads as unfriendly faster than any
  color choice.

If the system needs a webfont, add the `<link>` to each page head and point
`--font-display`/`--font-body` at it. The system stack is a fine default.

## 4. Spacing, shape, motion

- 4px scale. Half steps only for compact optical alignment, never as the
  default rhythm. A negative margin requires a comment explaining why layout
  structure cannot express the result.
- Four radii: small (~8), control (~14), card (~18), pill. Resist a fifth.
- Touch targets ≥ 44×44 pt.
- Mockups are static; where motion matters, mock the end states as separate
  phones and describe the transition in a note.

## 5. Contrast pairs

Declare every fg/bg combination the product will actually use in the page's
`contrastPairs` array (index.html). The foundations section renders a live
WCAG 2.1 audit for light and dark. Requirements:

- Body text: ≥ 4.5:1. Large text (≥ 24px, or ≥ 18.5px bold) and non-text UI:
  ≥ 3:1. Treat "AA large only" as a warning, acceptable only for roles that
  genuinely never set small text.
- Soft fills with alpha need a third entry — the surface they sit on:
  `["--status-good-ink", "--status-good-soft", "--card"]`.
- Fix failures in `design-system.css` by adjusting the role hex, never by
  deleting the pair. The audit is the point of the method: a style guide that
  cannot lie.

When you add a color role, add its pairs in the same edit.

## 6. Write the contract

Fill `DESIGN-SYSTEM.md` from the template: product context paragraph, the
role tables with the real values, the family rules, and the translation
rules. Keep it short enough that a future agent reads it whole before
touching the mockups — it is the file that makes the system enforceable
across sessions.

## 7. Validate before building screens

Serve the page, open `?solo=foundations`, and check:

1. Contrast audit fully green (or consciously-accepted "large only" rows).
2. Palette swatches read as one family in both themes — screenshot light and
   dark, look for the odd one out.
3. Type ramp has a visible step between every adjacent role; if two look the
   same, merge them.

Show the user the foundations screenshots and get a nod before Phase 4 —
changing a role after twenty screens exist is fine (that's the point of
tokens); discovering the whole temperature is wrong after twenty screens is
an afternoon lost.
