# Fantasy Design System Reference

Complete design token library for dark fantasy / RPG / western fantasy themed UIs. Derived from BG3, Diablo 4, Elden Ring, D&D Beyond, Friends & Fables, and award-winning fantasy web designs.

## Table of Contents
1. Color Palettes
2. Typography
3. Glassmorphism Values
4. Shadows & Depth
5. SVG Ornaments
6. Texture CSS
7. Animation Presets
8. Atmospheric Effects
9. Character Portrait Frames
10. Prompt-to-UI Style Guide

---

## 1. Color Palettes

### Dark Eldritch (Primary Recommendation)
Best for: Dark fantasy, horror, gothic interfaces

```css
:root {
  --bg-deep: #08060b;
  --bg-mid: #0f0c14;
  --bg-surface: #16121e;
  --bg-elevated: #1e1828;

  --gold: #c9a84c;
  --gold-bright: #f0d060;
  --gold-dim: #7a6530;
  --gold-muted: rgba(201, 168, 76, 0.08);

  --crimson: #7c142c;
  --crimson-bright: #a82040;
  --ember: #c45c2c;
  --ember-glow: #e8753a;

  --violet: #5e4d69;
  --violet-bright: #8b6fad;
  --violet-deep: #2d2343;

  --text-primary: rgba(244, 228, 193, 0.92);
  --text-secondary: rgba(244, 228, 193, 0.55);
  --text-muted: rgba(244, 228, 193, 0.3);
  --text-inverse: #08060b;

  --border-default: rgba(201, 168, 76, 0.08);
  --border-hover: rgba(201, 168, 76, 0.18);
  --border-active: rgba(201, 168, 76, 0.35);
}
```

### Warm Parchment (Secondary)
Best for: Character sheets, reading-heavy content, tavern aesthetic

```css
:root {
  --parchment: #f4e4c1;
  --parchment-dark: #e8d5a3;
  --parchment-light: #faf3e3;
  --ink: #2a1810;
  --ink-light: #4a3020;
  --ink-faded: #6a5040;
  --seal-red: #8b1a1a;
  --wood-dark: #3d2b1f;
  --wood-medium: #5a3d25;
  --metal-gold: #d4af37;
}
```

### Enchanted Forest
Best for: Druid/ranger themes, nature-focused UIs

```css
:root {
  --forest-deep: #0a1a0f;
  --forest-mid: #142a1a;
  --moss: #354f32;
  --emerald: #2d8a4e;
  --moonlight: #c5d5c8;
  --firefly: #e8d56a;
}
```

### Arcane (Bonus)
Best for: Wizard/sorcerer themes, magical interfaces

```css
:root {
  --void: #0a0815;
  --astral: #1a1535;
  --arcane-blue: #4a6aff;
  --arcane-purple: #8b5cf6;
  --starlight: #e2d9f3;
  --mana: #00d4ff;
}
```

## 2. Typography

### Recommended Font Stacks (Google Fonts)

**Display / Titles:**
- `'Cinzel Decorative', serif` — Most regal, use for app titles only
- `'Cinzel', serif` — Section headings, stat labels
- `'Pirata One', serif` — Gothic/pirate, more aggressive

**Body Text (Western):**
- `'Crimson Text', serif` — Old-world scholarly feel, includes decorative fleurons
- `'Cormorant Garamond', serif` — Elegant, thin strokes

**Body Text (CJK-compatible):**
- `'Noto Serif SC', serif` — Chinese serif, pairs with Cinzel
- `'Noto Sans SC', sans-serif` — Chinese sans, for UI labels

**Monospace (Stats/Numbers):**
- `'Cinzel', serif` with `font-variant-numeric: tabular-nums` — For ability scores
- `'JetBrains Mono', monospace` — For code/data

### Import String
```css
@import url('https://fonts.googleapis.com/css2?family=Cinzel:wght@400;500;600;700;900&family=Cinzel+Decorative:wght@400;700;900&family=Crimson+Text:ital,wght@0,400;0,600;0,700;1,400&family=Noto+Serif+SC:wght@400;600;700;900&family=Noto+Sans+SC:wght@300;400;500;600;700&display=swap');
```

### Type Scale
```
App Title:     48px / Cinzel Decorative / 900 / letter-spacing: 4px
Page Title:    28-32px / Cinzel / 700 / letter-spacing: 2px
Section Head:  20px / Cinzel / 600
Subsection:    16px / Noto Serif SC / 700
Body:          14px / Noto Sans SC / 400 / line-height: 1.7
Caption:       12px / Noto Sans SC / 400
Micro/Tag:     10px / Cinzel / 500 / letter-spacing: 1-2px / uppercase
Stat Number:   32-40px / Cinzel / 900
Modifier:      14-16px / Cinzel / 700
```

## 3. Glassmorphism Values

### Dark Glass Panel (Primary)
```css
.glass-panel {
  background: rgba(22, 18, 30, 0.55);
  backdrop-filter: blur(20px) saturate(140%);
  -webkit-backdrop-filter: blur(20px) saturate(140%);
  border: 1px solid rgba(201, 168, 76, 0.1);
  border-radius: 16px;
}
```

### Card Glass (Lighter)
```css
.glass-card {
  background: rgba(22, 18, 30, 0.35);
  backdrop-filter: blur(8px);
  -webkit-backdrop-filter: blur(8px);
  border: 1px solid rgba(201, 168, 76, 0.06);
  border-radius: 10px;
}
```

### Selected Card Glass
```css
.glass-card-selected {
  background: rgba(201, 168, 76, 0.08);
  border: 1.5px solid rgba(201, 168, 76, 0.35);
  box-shadow:
    0 0 20px rgba(201, 168, 76, 0.1),
    inset 0 0 20px rgba(201, 168, 76, 0.03);
}
```

## 4. Shadows & Depth (Dark Theme)

On dark backgrounds, pure black shadows are invisible. Use colored shadows:

```css
/* Elevation 1 — Cards at rest */
box-shadow:
  0 2px 4px rgba(8, 6, 11, 0.4),
  0 4px 12px rgba(8, 6, 11, 0.2);

/* Elevation 2 — Hover / floating */
box-shadow:
  0 4px 8px rgba(8, 6, 11, 0.5),
  0 8px 32px rgba(8, 6, 11, 0.3),
  0 0 12px rgba(201, 168, 76, 0.06);

/* Elevation 3 — Modals / overlays */
box-shadow:
  0 8px 16px rgba(8, 6, 11, 0.6),
  0 24px 64px rgba(8, 6, 11, 0.4);

/* Glow effect (for selected/active items) */
box-shadow:
  0 0 16px rgba(201, 168, 76, 0.15),
  0 0 40px rgba(201, 168, 76, 0.05);
```

## 5. SVG Ornaments

### Rune Diamond Divider
```html
<svg width="200" height="16" viewBox="0 0 200 16">
  <defs>
    <linearGradient id="fadeL" x2="1"><stop offset="0" stop-color="#c9a84c" stop-opacity="0"/><stop offset="1" stop-color="#c9a84c" stop-opacity=".4"/></linearGradient>
    <linearGradient id="fadeR" x2="1"><stop offset="0" stop-color="#c9a84c" stop-opacity=".4"/><stop offset="1" stop-color="#c9a84c" stop-opacity="0"/></linearGradient>
  </defs>
  <line x1="0" y1="8" x2="80" y2="8" stroke="url(#fadeL)" stroke-width=".5"/>
  <line x1="120" y1="8" x2="200" y2="8" stroke="url(#fadeR)" stroke-width=".5"/>
  <path d="M90 8 L100 2 L110 8 L100 14 Z" fill="none" stroke="#c9a84c" stroke-width=".8" opacity=".5"/>
  <circle cx="100" cy="8" r="2" fill="#c9a84c" opacity=".4"/>
</svg>
```

### Corner Decorations
```html
<!-- Top-left corner -->
<div style="position:absolute;top:8px;left:8px;width:20px;height:20px;border-top:1.5px solid rgba(201,168,76,0.3);border-left:1.5px solid rgba(201,168,76,0.3)"/>
```
Apply to all four corners by flipping border-top/bottom/left/right.

### Portrait Frame (Hexagonal)
```html
<svg width="120" height="140" viewBox="0 0 120 140">
  <defs>
    <clipPath id="hex"><polygon points="60,5 110,30 110,110 60,135 10,110 10,30"/></clipPath>
  </defs>
  <polygon points="60,5 110,30 110,110 60,135 10,110 10,30" fill="none" stroke="#c9a84c" stroke-width="1.5" opacity=".4"/>
  <polygon points="60,12 104,34 104,106 60,128 16,106 16,34" fill="none" stroke="#c9a84c" stroke-width=".5" opacity=".2"/>
</svg>
```

## 6. Texture CSS

### Parchment Effect
```css
.parchment {
  background:
    linear-gradient(135deg, #f4e4c1 0%, #e8d5a3 30%, #f0e0b8 60%, #e8d5a3 100%);
  border: 1px solid rgba(139, 109, 43, 0.2);
  box-shadow:
    inset 0 0 60px rgba(139, 109, 43, 0.08),
    0 8px 40px rgba(0, 0, 0, 0.5);
}
/* Add noise overlay */
.parchment::before {
  content: '';
  position: absolute; inset: 0;
  opacity: 0.04;
  background-image: url("data:image/svg+xml,%3Csvg width='200' height='200' xmlns='http://www.w3.org/2000/svg'%3E%3Cfilter id='n'%3E%3CfeTurbulence baseFrequency='.8' numOctaves='4'/%3E%3C/filter%3E%3Crect width='200' height='200' filter='url(%23n)'/%3E%3C/svg%3E");
  pointer-events: none;
}
```

### Metallic Gold Text
```css
.gold-text {
  background: linear-gradient(135deg, #c9a84c, #f0d060, #c9a84c);
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
  background-clip: text;
  filter: drop-shadow(0 1px 2px rgba(0,0,0,0.5));
}
```

## 7. Animation Presets

```css
/* Entrance */
@keyframes fadeInUp {
  from { opacity: 0; transform: translateY(16px); }
  to { opacity: 1; transform: translateY(0); }
}

/* Floating (for icons, decorations) */
@keyframes float {
  0%, 100% { transform: translateY(0); }
  50% { transform: translateY(-8px); }
}

/* Breathing glow (for selected items) */
@keyframes breathe {
  0%, 100% { box-shadow: 0 0 12px rgba(201,168,76,0.15); }
  50% { box-shadow: 0 0 30px rgba(201,168,76,0.3), 0 0 60px rgba(201,168,76,0.08); }
}

/* Shimmer loading bar */
@keyframes shimmer {
  0% { background-position: -200% 0; }
  100% { background-position: 200% 0; }
}
.shimmer {
  background: linear-gradient(90deg, transparent, var(--gold), transparent);
  background-size: 200% 100%;
  animation: shimmer 1.5s infinite;
}

/* God rays (atmospheric background) */
@keyframes godRay {
  0%, 100% { opacity: 0.03; transform: scaleY(1); }
  50% { opacity: 0.06; transform: scaleY(1.1); }
}

/* Particle rise (floating embers) */
@keyframes particleRise {
  0% { transform: translateY(0) scale(1); opacity: 0; }
  10% { opacity: 0.6; }
  80% { opacity: 0.1; }
  100% { transform: translateY(-100vh) scale(0.2); opacity: 0; }
}

/* Pulse ring (around important elements) */
@keyframes pulseRing {
  0% { transform: scale(1); opacity: 0.4; }
  100% { transform: scale(1.5); opacity: 0; }
}

/* Typewriter cursor */
@keyframes blink {
  0%, 100% { opacity: 1; }
  50% { opacity: 0; }
}
```

## 8. Atmospheric Background System

Full-page atmospheric background component pattern:

```jsx
// Fixed position, pointer-events: none, z-index: 0
// Layer 1: Radial gradient ambient light (top-center warm glow, side cool accents)
// Layer 2: 1-2 diagonal "god ray" divs with slow breathing animation
// Layer 3: 15-25 floating particles rising from bottom
// Layer 4: Vignette overlay (radial-gradient from transparent center to dark edges)
```

Key values:
- God rays: `width: 25-40%, height: 70-80%`, rotated 5-15deg, opacity 0.03-0.06
- Particles: `border-radius: 50%`, size 1-4px, `rgba(201,168,76, 0.2-0.6)`, duration 6-14s staggered
- Vignette: `radial-gradient(ellipse 70% 70% at 50% 50%, transparent 0%, rgba(8,6,11,0.4) 100%)`

## 9. Character Portrait Frame Patterns

### Basic Framed Portrait
```
┌─ corner ──────────── corner ─┐
│                               │
│   ┌─────────────────────┐    │
│   │                     │    │
│   │  Emoji / Image      │    │
│   │  Centered            │    │
│   │                     │    │
│   └─────────────────────┘    │
│   Class Name (uppercase)      │
│                               │
└─ corner ──────────── corner ─┘
```

- Outer: `border: 2px solid rgba(201,168,76,0.15); border-radius: 12px;`
- Inner gradient: `linear-gradient(135deg, classColor + "22", classColor + "08")`
- Corner decorations: 4 absolute-positioned divs with partial borders
- Class label: `font-family: Cinzel; font-size: 10px; letter-spacing: 2px; text-transform: uppercase`

## 10. Prompt-to-UI Style Guide

When the user describes a visual style, map to these CSS strategies:

| User Says | CSS Strategy |
|-----------|-------------|
| "暗黑" / "dark fantasy" | Dark Eldritch palette + glassmorphism + atmospheric BG |
| "酒馆" / "tavern" | Warm Parchment palette + wood textures + candlelight glow |
| "魔法" / "arcane" | Arcane palette + particle effects + rune ornaments |
| "极简" / "minimal" | Dark Eldritch but remove ornaments, wider spacing, fewer colors |
| "专业" / "professional" | Clean glassmorphism, tight type hierarchy, no particles |
| "像 BG3" | Left-right split layout, portrait frame, stat bars, warm gold accents |
| "像 D&D Beyond" | Content-first, tabbed interface, clean serif typography |
