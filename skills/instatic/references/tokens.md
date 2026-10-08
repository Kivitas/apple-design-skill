# Token recipes

Concrete derivations behind `SKILL.md`. Adapted to RHENIUS's existing layers in `src/index.css`.

## 1. Colour: one brand colour → a ramp

Instatic declares one brand colour and generates the tint/shade set from it. The equivalent here is
to express each role as a step on a ramp rather than as a named one-off:

```
base        #1A73E8          (RHENIUS action blue)
hover       base lightened ~6% in L   -> #3B85E8
pressed     base darkened  ~8% in L   -> #2563C4
ink-on-base #FFFFFF
```

Rules that make this a system rather than a palette:

- A role is `surface | content | accent | signal`, and each role gets **one** value per appearance —
  not per component.
- Tints are translucent steps of one ink, so they compose over any surface: `rgba(45,42,38,0.05)`
  → `0.10` → `0.24` in the light appearance, `rgba(255,255,255,0.06)` → `0.14` → `0.20` in dark.
- Never mix a hex that is "close to" a token. Either it is the token, or the token needs a new step.

Current RHENIUS ramps:

| Role | Light | Dark |
| --- | --- | --- |
| glass materials | `--lg-regular-bg` … `--lg-modal-bg` | same variable names, dark values |
| neutral content layer | `--surface-card`, `--surface-blend`, `--surface-heading`, `--surface-muted` | dark overrides on the same names |
| content-layer panels | `--panel-0/1/2`, `--panel-inset`, `--panel-line`, `--panel-ink`, `--panel-ink-muted`, `--panel-chip` | dark overrides on the same names |

## 2. Type: one fluid ramp

Instead of hand-picked sizes per component, declare a single ramp and let it scale:

```css
--step--1: clamp(0.78rem, 0.76rem + 0.1vw, 0.84rem);   /* meta, counters        */
--step-0:  clamp(0.94rem, 0.92rem + 0.12vw, 1rem);      /* body                  */
--step-1:  clamp(1.06rem, 1.02rem + 0.2vw, 1.2rem);     /* card title            */
--step-2:  clamp(1.3rem,  1.22rem + 0.4vw, 1.6rem);     /* section heading       */
```

- Weight and colour carry emphasis; the ramp carries scale. Avoid adding a size when a weight
  change would do.
- Floors matter: nothing below ~11pt/0.7rem survives a phone at arm's length
  (`accessibility.md › Vision` in the apple-design skill).
- RHENIUS keeps `--font-*` stacks (Jakarta, Playfair, Fira Code …) as *families*; the sizes belong
  to the ramp above, not to the family.

## 3. Spacing: one rhythm

One scale, shared across breakpoints: `2, 4, 8, 12, 16, 24, 32, 48, 64` (px) expressed in `rem`.

- A breakpoint changes composition (columns, density, which controls are inline) — never the scale.
- Gutters around bezelled controls ≈ 12pt, around borderless ones ≈ 24pt
  (`accessibility.md › Mobility`): the scale above already contains both steps.
- Arbitrary values such as `p-[13px]` mean the scale is missing a step; add the step or reuse one.

## 4. Utilities: locked, generated, small

Surfaces are declared once in `@layer utilities` in `src/index.css` and composed everywhere else:

- Functional/floating layer: `.liquid-glass`, `.liquid-glass-chrome`, `.liquid-glass-modal`,
  `.liquid-glass-segmented`, `.liquid-glass-scroll-edge`.
- Content layer: `.liquid-glass-card`, `.surface-btn`, `.surface-bar`.
- Content-layer panels (dual appearance): `.rh-panel*` conventions referenced by `var(--panel-*)`.

A component that needs a new surface adds a class here and uses it — it does not invent a
`bg-[#0E0D17]` inline. That is exactly the failure this skill exists to prevent: a panel authored
against one appearance's literal value.

## 5. Appearance pairs

Every token declares both appearances; `[data-theme]` on the app root selects them. The three values
in use are `liquid-glass` (default warm paper), `light` and `dark`.

- Dark is not a light inversion: elevated surfaces advance over the base, foregrounds brighten.
- A value that only exists in one appearance is a bug report waiting to happen — see the `--panel-*`
  pair, added because the contact-card, sheet and modal surfaces were authored dark-only.
- Contrast floors come from the apple-design skill: 4.5:1 for text at or below 17pt, 3:1 for bold or
  18pt-and-larger text, checked in both appearances.

## 6. Verification

Token discipline is only real if it is measured:

1. Grep the surface for hex literals — anything that is not in `index.css`, `discovery.ts` or a test
   fixture is a token that was not used.
2. Render the surface in each appearance and read the computed background and text colour.
3. Compute the contrast ratio from those computed values; do not eyeball a screenshot.
