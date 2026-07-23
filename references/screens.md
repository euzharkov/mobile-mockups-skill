# Building mockup screens

Screens are where the method pays off: an agent can draft five states of a
flow in the time a Figma session takes to open. The discipline that keeps the
output useful is below.

## Page anatomy

One HTML page per flow area (`index.html` for the main app + foundations,
`onboarding.html`, `settings.html`, …). Every page:

- links `design-system.css`, `mockup.css`, `mockup.js` and any fonts/icons in
  an identical `<head>` (copy `flow.html` to start a new page);
- declares its content as data and calls `renderMockupPage(cfg)`;
- keeps its own Layer-2 screen CSS in a `<style>` block.

Splitting by flow area is not cosmetic: a single giant page eventually scrolls
badly, screenshots unreliably, and merges painfully. Split when a page grows
past roughly a dozen sections; link siblings from the intro line.

Within a page: **sections are flows, phones are states.** A section is one
user journey or feature moment (`{ id, title, desc, cols, notes }`); each
column is one screen state with a label explaining what it shows and why
(`{ label, tag, tagAccent, html }`). Rails scroll horizontally, so a flow
reads left→right like a storyboard.

## Authoring screens

Build screens as JS template literals composed from helpers:

```js
const TABS = [
  { key: "home", icon: "house", label: "Home" },
  { key: "you", icon: "user-circle", label: "You" },
];

const settingsHome = phone(
  `<h2 class="t-title">Settings</h2>
   <div class="card">…</div>`,
  { tab: tabbar(TABS, "you") },
);
```

Why template literals instead of static HTML: repeated chrome (status bar,
tab bar, back button) stays in one function, and near-identical states are a
copy with a two-line diff — which is exactly how you want to author "same
screen, empty vs filled vs error".

Grow page-local helpers freely as patterns repeat (`chip(label, selected)`,
`listRow(icon, title, meta)`…). They are conveniences for THIS page, not a
component library: don't export them, don't force other pages to adopt them,
and don't treat their names as an API production must mirror.

`phone()` options: `tab` (rendered tabbar), `footer` (pinned CTA area),
`back`, `scroll: false` for fixed layouts, `night` to force dark on one
screen, `bare` for full-bleed scenes (OS home screen, widgets, splash).

### Layer-2 CSS rules

Screen CSS lives in the page's `<style>` and may define display mode,
flex/grid, order, wrapping, scrolling, positioning, responsive behavior. It
takes every color, size, radius, and space from design-system values. Type
comes from `.t-*` classes in the markup; screen CSS may adjust alignment,
wrapping, or assign an explicitly semantic color — never re-specify a role's
metrics. An intentional exception carries a comment at the site; it does not
quietly become a convention.

## The states discipline

The happy path is the least informative screen in a flow. For each flow,
mock the states where design actually happens:

- **Empty / first-run** — carries the product voice; never just "No items".
- **Loading** — skeletons or a sentence, whichever the product would ship.
- **Error / offline** — recoverable tone, next step visible.
- **Edge content** — the 40-character name, the 3-line title, the 200-entry
  list, the zero-decimal price. If long content will truncate, show it
  truncating.
- **Variants** — free vs paid, A/B arms, platform differences: same screen
  side by side with `tag:` labels ("Free", "Plus", "Variant B").

Not every flow needs every state — but choosing to skip one should be a
decision, not an oversight. A flow showing only its happy path is a sketch,
not a mockup.

## Copy is part of the mockup

Write real copy, in the product's voice, from the discovery answers. The
mockup doubles as the copy spec — production takes user-facing text from it
1:1, so "Lorem ipsum" and placeholder-y labels ("Item title here") make the
mockup unshippable. Use plausible data: realistic names, dates that make
sense together, numbers that add up. If the product has a translation file,
still write the real English here; it's the source translators start from.

## Annotations carry the reasoning

Decisions die in chat history. Put them in the mockup:

- **Column labels** — one sentence: what this state is, and the non-obvious
  choice it shows (`<b>First run.</b> One action, no feature tour.`).
- **Section notes** (`notes: [note(title, body)]`) — why the design is this
  way: rejected alternatives, constraints, a11y reasoning.
- **Page notes** (`pageNotes`) — cross-cutting rules for the whole area.

A mockup with good notes replaces a spec document; one without them is just a
picture that will be re-litigated in three weeks.

## Icons and images

The icon library is the project's choice, made during discovery and recorded
in DESIGN-SYSTEM.md — don't impose one. Whatever was chosen, keep the same
discipline: one default weight everywhere, one clearly-defined active/selected
variant, loaded identically in every page head. Define a page-local shorthand
(`const icon = (name, fill) => …`) so swapping libraries later is one
function's diff; `tabbar()` accepts rendered icon HTML, so any library — or
emoji, or inline SVG — works. OS chrome (status bar, back chevron) is already
dependency-free SVG inside `mockup.js`.

For imagery, prefer CSS/SVG placeholders in
`--accent-tint`/`--accent-decorative` over stock photos — they keep attention
on structure and read clearly as "illustration goes here". Real product
screenshots or generated art can come later; don't let asset hunting block
flow work.

## Growing the canvas

- New flow area → copy `flow.html`, set `foundations: false`, link it from
  the index page intro.
- New shared value → only if it's reusable beyond one screen; add it to
  `design-system.css` AND its row/pairs to `DESIGN-SYSTEM.md` and
  `contrastPairs` in the same edit.
- Old screens with legacy one-off values → clean what you touch, leave the
  rest stable (incremental migration; no big-bang rewrites).
