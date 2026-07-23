# {{PRODUCT}} — Mockup Design System

<!-- This file is the written contract for the design system in design-system.css.
     Fill every {{placeholder}}, delete instruction comments, and keep it honest:
     when the CSS changes, this file changes in the same commit. -->

This document governs `design-system.css` and every mockup page beside it. The
mockups are the design source of truth for {{PRODUCT}}; production implements
what these pages show, and user-facing copy is taken from them 1:1.

## Model: two layers only

1. **Flat shared values** live in `design-system.css`: semantic colors, type
   roles, spacing, radii, and screen insets.
2. **Screen CSS** lives with each HTML page and controls composition,
   positioning, and exceptional screen-specific presentation.

There is intentionally no component-token tier, component library, or shared
runtime. CSS classes in pages are ordinary presentation hooks, not a component
API that production must mirror.

## Product context

<!-- Two or three sentences: audience, tone words, platform. Everything below
     should be explainable from this paragraph. -->

{{Audience, the three tone words chosen during discovery, target platform, and
anything about context of use that shapes the system — e.g. "used half-asleep
at 6am" or "dense pro tool used all day".}}

## Colors

Use semantic roles, never colors by appearance:

| Role | Light | Dark | Rule |
| --- | --- | --- | --- |
| `--canvas` | {{hex}} | {{hex}} | App background. Must stay visibly distinct from card. |
| `--card` | {{hex}} | {{hex}} | Raised surface for primary content. |
| `--functional` | {{hex}} | {{hex}} | Quieter surface: form fields, secondary containers. |
| `--selected` | {{hex}} | {{hex}} | Chosen/pressed fill. |
| `--accent-tint` | {{hex}} | {{hex}} | Soft wash for highlighted containers. Never carries body text alone — pair with ink. |
| `--ink-900/600/400` | {{hex}} ×3 | {{hex}} ×3 | Primary / secondary / tertiary text. Each step passes its contrast target on canvas AND card. |
| `--accent-interactive` | {{hex}} | {{hex}} | Links, active states — must pass 4.5:1 where it carries text. |
| `--accent-decorative` | {{hex}} | {{hex}} | Fills and illustration; never carries text. The two accent hexes may swap roles between themes so interactive stays the readable one. |
| `--danger` | {{hex}} | {{hex}} | Serious, not alarmist. |

Raw colors are reserved for preview-only material (phone shell, gallery
chrome, illustrations, simulated OS UI) — that is `mockup.css` territory.

Dark mode is a re-resolution of roles, not an inversion of hexes: surfaces
rise instead of going pure black, ink softens instead of going pure white.
{{Note any screens that are light-only or dark-only, e.g. onboarding.}}

## Typography

A role owns its family, weight, size, line height, tracking, and default color
together, as a `.t-*` class. Mockup px = production pt, 1:1 at the 390×844
baseline.

| Role | Family / weight | Size / line | Tracking | Default color | Intended use |
| --- | --- | --- | --- | --- | --- |
| `.t-display` | {{family}} {{weight}} | {{34/40}} | {{-0.3}} | Primary | Emotional opening or highest-emphasis heading |
| `.t-title` | {{…}} | {{28/34}} | {{…}} | Primary | Functional screen title |
| `.t-heading` | {{…}} | {{22/28}} | {{…}} | Primary | Section or strong card heading |
| `.t-subheading` | {{…}} | {{17/23}} | 0 | Primary | Compact section heading |
| `.t-body-lg` | {{…}} | {{18/28}} | 0 | Primary | Prominent supporting copy |
| `.t-body` | {{…}} | {{17/26}} | 0 | Primary | Default reading copy |
| `.t-callout` | {{…}} | {{15/22}} | 0 | Secondary | Compact supporting or control copy |
| `.t-caption` | {{…}} | {{13/18}} | +0.1 | Secondary | Metadata and secondary labels |
| `.t-link` | {{…}} | {{17/26}} | 0 | Interactive | Text action or inline link |
| `.t-on-accent` | {{…}} | {{16/22}} | 0 | On-accent | Label on a filled primary action |

<!-- Rename, add, or remove roles to fit the product — then keep this table
     and the .t-* classes in perfect sync. Ten roles is usually plenty. -->

Family rules:

- At most two families: {{display family}} for `display`/`title`, {{body
  family}} for everything else.
- Only weights {{400, 500, 600}} are loaded. Do not request or synthesize
  others.
- Do not rely on browser-default `h1`–`h6`, `strong`, or button typography;
  assign a role class explicitly.
- Italics are not a hierarchy tool.

Screen CSS may change alignment, wrapping, truncation, or an explicitly
semantic color. It must not partially override a role's family, weight, size,
line height, or tracking to solve a layout problem. Let text wrap; avoid
fixed-height text containers so longer copy stays visible.

## Icons

{{Library and version, or "emoji / inline SVG only"}} — the user's choice at
discovery, loaded identically in every page head. Default weight
{{outline/regular}}; active or selected states use {{filled variant / weight
change}}. Icons never substitute for labels on primary navigation.

## Spacing

4px scale (`--space-1` … `--space-10`). {{Note any half-step policy.}}
Negative spacing is an explicit optical adjustment and needs a short comment
explaining why layout structure cannot express the result.

## Radius and sizing

Four shared radii: small {{8}}, control {{14}}, card {{18}}, pill. Device
shell radii and SVG geometry are preview geometry, not product values.
Touch targets are at least 44×44 pt.

## Screen-specific CSS

Screen selectors may define display mode, flex/grid direction, order,
wrapping, scrolling, positioning, and responsive behavior. They consume shared
values for typography, spacing, color, and shape. An intentional exception
must be obvious from its location and comment; do not silently promote an
exception into a new convention.

## Incremental migration

Older pages may contain legacy one-off values. Do not normalize a whole file
while making an unrelated change: clean the declarations you touch, leave
unrelated screens stable.

## Translating to and from production

Production code is evidence of intended results, not a second source of design
values. Translate by role and intent:

- A typography change maps to the closest existing type role.
- A palette change updates the flat semantic color, both themes considered.
- A screen layout change stays in that screen's CSS on the spacing scale.
- A one-off implementation workaround is not copied back unless it represents
  an intentional design decision.

If a change requires a new shared value, it must be reusable beyond the
current screen or map to an established product-wide role.
