---
name: imagegen
description: |
  AI image generation skill for creating character portraits, concept art, and visual assets using cloud APIs (Together AI, FAL.ai, Replicate, Cloudflare Workers AI, Stability AI) or self-hosted solutions (ComfyUI, Automatic1111). Use this skill whenever the user asks to generate images, create character art, make portraits, produce illustrations, or integrate AI image generation into a project. Also trigger when the user mentions "立绘" (character illustration), "concept art", "avatar", "portrait", or needs to build an image generation pipeline. Contains prompt engineering templates optimized for fantasy/D&D character art.
---

# AI Image Generation Skill

This skill helps you integrate AI image generation into projects and craft effective prompts for fantasy/RPG character art.

## API Quick Reference

Read `references/api-reference.md` for complete API details including endpoints, pricing, SDKs, and example code for each provider.

### Provider Selection Guide

| Scenario | Recommended Provider | Why |
|----------|---------------------|-----|
| **Prototyping (free)** | Together AI (Flux schnell) | 3 months unlimited free |
| **Production (cheap)** | FAL.ai ($0.025/img) | Fastest inference, cheapest at scale |
| **Production (easy)** | Replicate | Best docs, widest model selection |
| **Edge/Serverless** | Cloudflare Workers AI | Native JS, runs at edge |
| **Self-hosted** | ComfyUI (Docker) | Full control, no API limits |
| **Highest quality** | Stability AI (SD3.5) | Best anatomical accuracy |

### Minimal Integration (TypeScript / Next.js)

```typescript
// Together AI — simplest free option
async function generatePortrait(prompt: string): Promise<string> {
  const res = await fetch('https://api.together.xyz/v1/images/generations', {
    method: 'POST',
    headers: {
      'Authorization': `Bearer ${process.env.TOGETHER_API_KEY}`,
      'Content-Type': 'application/json',
    },
    body: JSON.stringify({
      model: 'black-forest-labs/FLUX.1-schnell-Free',
      prompt,
      width: 768,
      height: 1024,  // portrait ratio
      steps: 4,
      n: 1,
    }),
  });
  const data = await res.json();
  return data.data[0].url;  // returns image URL
}
```

## Prompt Engineering for D&D Characters

### Prompt Structure Formula

```
[Art Style] [Medium] of [Race] [Class] [Physical Description],
[Equipment/Clothing], [Pose/Action],
[Background/Setting], [Lighting], [Quality Modifiers]
```

### Style Presets

**High Fantasy (Default)**
```
"epic fantasy character portrait, painterly illustration style,
warm but muted color palette, soft rim lighting, dramatic composition,
highly detailed, professional concept art, 4K"
```

**Dark Fantasy (Gothic)**
```
"dark fantasy character art, moody atmospheric lighting,
desaturated colors with crimson and gold accents, gritty textures,
chiaroscuro lighting, detailed armor scratches and battle damage"
```

**Oil Painting**
```
"oil painting style portrait, Renaissance chiaroscuro,
rich impasto brushstrokes, warm candlelight,
museum quality, masterwork painting"
```

**Watercolor**
```
"watercolor illustration, soft washes of color,
visible paper texture, ethereal and dreamy,
delicate linework, art nouveau influence"
```

**Concept Art / Digital**
```
"digital concept art, artstation trending,
clean linework, professional character design sheet,
volumetric lighting, octane render quality"
```

### Race-Specific Prompt Additions

| Race | Visual Cues |
|------|------------|
| Human | "determined expression, weathered features, practical armor" |
| Elf | "angular features, pointed ears, ethereal beauty, flowing hair, elegant posture" |
| Dwarf | "stocky build, braided beard, rune-carved armor, strong jawline" |
| Halfling | "small stature, cheerful expression, oversized pack, clever eyes" |
| Dragonborn | "draconic features, scaled skin, glowing eyes, powerful build, tail" |
| Tiefling | "curved horns, tail, red/purple skin tones, sharp features, infernal marks" |
| Gnome | "small with large eyes, wild hair, tinker's goggles, curious expression" |
| Half-Orc | "tusks, green-gray skin, muscular build, battle scars, intense gaze" |

### Class-Specific Prompt Additions

| Class | Visual Cues |
|-------|------------|
| Fighter | "plate armor, sword and shield, battle stance, military bearing" |
| Wizard | "arcane robes with glowing runes, spellbook, magical energy effects, staff" |
| Rogue | "leather armor, hood/cowl, daggers, shadows, alert posture" |
| Cleric | "holy symbol, radiant light, ornate vestments, healing aura" |
| Ranger | "forest gear, bow, animal companion, nature elements, cloak" |
| Paladin | "gleaming armor, holy weapon glowing, righteous stance, cape" |
| Warlock | "eldritch energy, patron symbol, dark robes, glowing eyes" |
| Bard | "instrument, colorful garb, charismatic pose, musical notes" |
| Barbarian | "minimal armor, tribal markings, massive weapon, rage expression" |
| Druid | "natural materials, wooden staff, animal features, living vines" |
| Monk | "simple robes, martial stance, ki energy, balanced pose" |
| Sorcerer | "wild magic effects, glowing veins, intense expression, raw power" |

### Negative Prompts

**Universal (works for most models):**
```
"extra fingers, bad anatomy, deformed hands, blurry, low quality,
watermark, text, signature, cropped, worst quality, jpeg artifacts"
```

**For Photorealism:**
```
"cartoon, anime, 3D render, CGI, unrealistic proportions, plastic skin"
```

### Building Complete Prompts

**Example: Tiefling Warlock**
```
"dark fantasy character portrait of a female tiefling warlock,
curved obsidian horns, deep purple skin, glowing amber eyes,
wearing ornate black robes with eldritch sigils, holding a pact blade
wreathed in green fire, standing in a dimly lit ritual chamber,
dramatic rim lighting from arcane portal behind her,
highly detailed, professional concept art, painterly style, 4K"
```

**Example: Dwarf Paladin**
```
"epic fantasy portrait of a male dwarf paladin,
silver braided beard with gold clasps, stern noble expression,
wearing gleaming mithril plate armor with holy engravings,
wielding a glowing warhammer, divine light radiating from behind,
mountain fortress backdrop, golden hour lighting,
oil painting style, masterwork quality"
```

## Programmatic Prompt Builder

When building prompts from character data:

```typescript
function buildPortraitPrompt(character: {
  race: string;
  class: string;
  name: string;
  alignment: string;
  background: string;
}, style: 'high-fantasy' | 'dark-fantasy' | 'oil-painting' | 'watercolor' | 'concept-art'): string {

  const raceVisuals = RACE_PROMPTS[character.race];
  const classVisuals = CLASS_PROMPTS[character.class];
  const stylePrefix = STYLE_PRESETS[style];

  return `${stylePrefix}, ${raceVisuals}, ${classVisuals}, portrait composition, highly detailed`;
}
```

## Model Recommendations

### For Character Portraits
1. **FLUX.1 schnell** — Fast, good quality, free on Together AI
2. **SDXL + DreamShaper XL** — Best anatomical accuracy
3. **SDXL + ZavyChromaXL** — Best for cinematic concept art look
4. **Flux 1.1 Pro** — Highest quality but paid

### LoRA Models (for ComfyUI/self-hosted)
- **DnD Darkest Fantasy LoRA** — Specifically trained for D&D art
- **Big Love XL** — Best anatomical accuracy base
- **Fantasy Game Icon** — For item/spell icons
