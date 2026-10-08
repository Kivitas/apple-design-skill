---
name: instatic-tokens
description: >
  Design-token discipline distilled from Instatic (https://github.com/corebunch/instatic, MIT),
  whose Core Framework layer generates a whole system from a few declared scales: one brand colour
  expanded into a full tint/shade ramp, one fluid mathematical type scale, one spacing scale shared
  across every breakpoint, and locked generated utility classes emitted into a single small
  stylesheet. Use it when adding a surface, a colour, a text style or a spacing step to RHENIUS, or
  when reviewing a screen for "this looks hand-picked rather than systemic". Pairs with the
  apple-design skill in this directory: apple-design carries the platform conventions and
  accessibility floors, instatic-tokens carries how the values themselves are derived and locked.
---

# Instatic Token Discipline

Source of the ideas: <https://github.com/corebunch/instatic> (MIT), specifically its Core Framework
layer — the design-token engine it ships as a core system rather than a plugin. Its published
promises are the whole point of this skill:

- "Color tokens that generate their own shade scale. Define one brand color, get the full set of
  tuned tints and shades automatically."
- "Type scales that are fluid and mathematical. One ramp that scales with the viewport, instead of
  forty hand-picked font sizes you have to keep in sync."
- "Spacing scales so every page and every breakpoint keeps the same rhythm."
- "A utility-class generator that emits locked, generated classes into one small framework.css. No
  bloat, no duplicate rules, nothing you didn't ask for."
- "Your whole design system lives as data. Change one token and every page that uses it updates."
- Published pages carry "plain semantic HTML and compact CSS, with none of the editor's machinery
  left behind in the page."

Take the *method*, not the implementation. RHENIUS is a React + Vite + Tailwind v4 app with its own
token layer in `src/index.css`; there is no Core Framework, no Bun server and no visual editor to
adopt.

## Rules

1. **One source per value.** Every colour, type size, radius and spacing step used by a component
   resolves to a token. A hex literal in a component is a defect, not a style choice.
2. **Generate, do not enumerate.** A new tint is a step in an existing ramp, not a new named
   colour. One brand colour plus a lightness/saturation curve beats ten hand-picked hexes.
3. **One ramp.** Body copy, labels and headings come from a single fluid scale, so hierarchy is a
   property of the ramp, not of forty local font-size decisions.
4. **One rhythm.** Spacing steps are shared across breakpoints; a breakpoint changes the composition,
   not the scale.
5. **Tokens are data.** Changing a token must restyle every surface that uses it, with no per-surface
   patch. If a change requires touching many files, the value was not a token.
6. **Output stays semantic and small.** Prefer the smallest set of generated classes that expresses
   the design; do not ship one-off utilities that duplicate an existing step.
7. **Design for the appearance, not an appearance.** A token carries light and dark values (see the
   `--panel-*` / `--lg-*` pairs in `src/index.css`). A surface that only works in one appearance is
   a defect.

## How this maps onto RHENIUS

| Instatic idea | RHENIUS home |
| --- | --- |
| Generated colour shade scale | `--lg-*` glass materials, `--surface-*` neutrals, `--panel-*` content-layer panels in `src/index.css` |
| Fluid mathematical type ramp | the `--font-*` stacks plus a single scale; sizes expressed in `rem`/`clamp()`, never per-component px |
| Spacing scale across breakpoints | Tailwind's 4pt-derived steps plus the `rem` spacing custom properties; no arbitrary `p-[13px]` |
| Locked generated utility output | `@layer utilities` class set (`.liquid-glass*`, `.surface-btn`, `.rh-panel*`) — the only place surfaces are defined |
| Design system as data | `[data-theme]` on the app root driving all of the above, plus `src/ui/motion/tokens.ts` for spring physics |

## Review checklist

- [ ] Does every new value resolve to an existing token, or does it justify a new *step* on an
      existing scale?
- [ ] Does the surface read correctly in `liquid-glass`, `light` and `dark`?
- [ ] Is the text hierarchy produced by the ramp rather than by local overrides?
- [ ] Does the change restyle coherently elsewhere, or did it require per-file patching?
- [ ] Was any class added that merely duplicates an existing step?

See `references/tokens.md` for the concrete recipes and the values RHENIUS currently uses.
