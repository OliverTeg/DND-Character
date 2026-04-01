---
name: react-ui-design
description: |
  High-quality React UI component design skill for creating polished, immersive single-file .jsx prototypes and production-ready components. Use this skill whenever the user asks you to create any UI component, prototype, landing page, dashboard, interactive demo, or visual design in React/JSX — especially for fantasy, gaming, dark-themed, or visually rich interfaces. Also trigger when the user asks you to improve, redesign, or polish an existing UI, or when they mention "make it look better", "more professional", "higher quality UI", "design system", or "prototype". This skill encodes hard-won lessons about what separates amateur-looking AI-generated UIs from professional-grade designs.
---

# React UI Design Skill

You are creating a React UI component. This skill will help you produce work that looks professionally designed rather than AI-generated. The difference comes down to discipline in a few key areas that AI tends to get lazy about.

## Before You Write Any Code

Read the appropriate reference file based on the project's visual direction:

- **Fantasy/Dark theme**: Read `references/fantasy-design-system.md` — contains exact color palettes, typography stacks, glassmorphism values, ornamental SVG patterns, and texture techniques extracted from BG3, Diablo 4, D&D Beyond, and award-winning fantasy web designs.
- **Modern/Minimal theme**: Use the spacing and typography principles below, with shadcn/ui-style clean aesthetics.

## The 8px Grid — Non-Negotiable

Every spacing value must be a multiple of 8. Not 5, not 13, not 20. This single rule eliminates the "something feels off" problem that plagues AI-generated UIs.

```
8px   — tightly coupled elements (icon + label)
16px  — inside components (button padding, input padding)
24px  — between related groups
32px  — between distinct sections
48px  — major section gaps
64px+ — page-level breathing room
```

When defining padding/margin/gap, mentally check: "Is this a multiple of 8?" If not, round to the nearest one.

## Typography Hierarchy

AI-generated UIs almost always fail at type hierarchy — everything ends up similar size with no clear visual weight. Fix this by establishing exactly 5-6 type sizes and sticking to them:

```
Display:  32-48px  (page titles only, used once per view)
H1:       24-28px  (section headings)
H2:       18-20px  (subsection headings)
Body:     14-15px  (main content)
Caption:  12px     (metadata, labels, secondary info)
Micro:    10px     (tags, badges, timestamps)
```

Rules:
- Never use more than 2 font families (1 display + 1 body)
- Weight contrast matters more than size contrast — use 400 vs 700, not 14px vs 16px
- Line height: 1.5 for body text, 1.2-1.3 for headings, 1.0 for single-line display
- Letter-spacing: +1-4px for uppercase labels, 0 for body, -0.5px for large display

## Color System Architecture

Define colors as CSS custom properties. Every UI needs exactly these semantic layers:

```css
/* Surfaces (backgrounds) */
--bg-primary     /* deepest background */
--bg-secondary   /* cards, panels */
--bg-tertiary    /* nested elements, hover states */

/* Text */
--text-primary   /* main content — high contrast */
--text-secondary /* supporting text — medium contrast */
--text-muted     /* metadata, placeholders — low contrast */

/* Accent */
--accent         /* primary action color */
--accent-hover   /* accent + 10% lighter */
--accent-subtle  /* accent at 8-12% opacity — for backgrounds */

/* Borders */
--border-default /* subtle — 6-12% opacity */
--border-hover   /* 15-25% opacity */
--border-active  /* accent color or 30%+ opacity */

/* Feedback */
--success, --warning, --error
```

The #1 mistake: using too many colors. Limit your palette to 2-3 hues max. Everything else is opacity/lightness variations of those hues.

## Interactive States — The Professional Differentiator

Every clickable element needs ALL of these states. Missing any one makes the UI feel broken:

1. **Default** — base appearance
2. **Hover** — subtle lift or highlight (transform: translateY(-1px) + border/shadow change)
3. **Focus-visible** — keyboard navigation ring (NEVER remove outline without replacement)
4. **Active/Pressed** — brief "push" feedback (transform: translateY(0) or scale(0.98))
5. **Selected** — persistent visual marker (border-color + subtle glow + background tint)
6. **Disabled** — opacity 0.35-0.5, cursor: not-allowed

```css
/* Pattern for glass-card hover */
.card {
  transition: all 0.25s cubic-bezier(0.4, 0, 0.2, 1);
  border: 1px solid var(--border-default);
}
.card:hover {
  border-color: var(--border-hover);
  transform: translateY(-2px);
  box-shadow: 0 8px 32px rgba(0,0,0,0.15);
}
.card:focus-visible {
  outline: 2px solid var(--accent);
  outline-offset: 2px;
}
.card.selected {
  border-color: var(--accent);
  background: var(--accent-subtle);
  box-shadow: 0 0 20px rgba(accent, 0.12);
}
```

## Shadows — The Depth Cheat Code

Use layered shadows for realistic depth. Single `box-shadow` always looks flat:

```css
/* Elevation Level 1 (cards) */
box-shadow:
  0 1px 2px rgba(0,0,0,0.1),
  0 4px 8px rgba(0,0,0,0.08);

/* Elevation Level 2 (hover / floating) */
box-shadow:
  0 2px 4px rgba(0,0,0,0.12),
  0 8px 24px rgba(0,0,0,0.1),
  0 0 12px rgba(accent, 0.06);  /* subtle color glow */

/* Elevation Level 3 (modals / dropdowns) */
box-shadow:
  0 4px 8px rgba(0,0,0,0.15),
  0 16px 48px rgba(0,0,0,0.15);
```

Dark themes: `rgba(0,0,0,X)` is invisible on dark backgrounds. Use very dark, slightly saturated versions of the element's own color.

## Animation Principles

1. **Duration**: 150-300ms for micro-interactions, 300-500ms for layout changes, 500ms+ for dramatic reveals
2. **Easing**: Always `cubic-bezier(0.4, 0, 0.2, 1)` (ease-out) for entrances, `cubic-bezier(0.4, 0, 1, 1)` for exits
3. **Properties**: ONLY animate `transform` and `opacity`. Never animate width/height/top/left (triggers layout reflow)
4. **Stagger**: When revealing lists, delay each item by 50-80ms
5. **Reduce motion**: Always respect `prefers-reduced-motion`

```css
@keyframes fadeInUp {
  from { opacity: 0; transform: translateY(16px); }
  to { opacity: 1; transform: translateY(0); }
}

/* Stagger pattern */
.item:nth-child(1) { animation-delay: 0ms; }
.item:nth-child(2) { animation-delay: 60ms; }
.item:nth-child(3) { animation-delay: 120ms; }
```

## Glassmorphism (When Appropriate)

Exact values that work:

```css
background: rgba(22, 18, 30, 0.55);      /* dark glass */
/* or */
background: rgba(255, 255, 255, 0.08);   /* light glass on dark */
backdrop-filter: blur(16px) saturate(140%);
-webkit-backdrop-filter: blur(16px) saturate(140%);
border: 1px solid rgba(255, 255, 255, 0.08);
```

Glass only works over rich backgrounds (gradients, images, particles). On flat solid backgrounds it just looks muddy.

## Layout Patterns

### Left-Right Split (Character Creation / Settings)
```
┌──────────────────────────────────┐
│  Preview (30%)  │  Controls (70%) │
│  Fixed/Sticky   │  Scrollable     │
└──────────────────────────────────┘
```

### Card Grid
- 2 columns for selection (race/class): `grid-template-columns: repeat(2, 1fr); gap: 8px;`
- 3 columns for stats/attributes: `repeat(3, 1fr)` or `repeat(6, 1fr)` for compact
- Always set `min-width: 0` on grid children to prevent overflow

### Scrollable Content
- Max-height containers with `overflow-y: auto`
- Fade gradient at bottom: `mask-image: linear-gradient(to bottom, black 85%, transparent)`

## Component Checklist

Before finishing any component, verify:

- [ ] 8px grid spacing
- [ ] Type hierarchy (5-6 distinct sizes, 2 font families max)
- [ ] All interactive states (hover, focus-visible, active, selected, disabled)
- [ ] Layered shadows (not single box-shadow)
- [ ] Transition on all interactive properties
- [ ] Loading state (skeleton or spinner)
- [ ] Empty state (if applicable)
- [ ] Semantic HTML (button for actions, not div)
- [ ] Text contrast ratio ≥ 4.5:1
- [ ] No hardcoded pixel widths (use %, max-width, clamp())

## File Output

When creating a single .jsx file prototype:
- All styles as CSS-in-JS objects or a `<style>` block in the component
- Use CSS custom properties for theming (define in a style block)
- Google Fonts via `@import url(...)` in the style block
- SVG ornaments inline (not external files)
- Include sample/mock data so the prototype is immediately interactive
- Export a single default component that renders the full experience
