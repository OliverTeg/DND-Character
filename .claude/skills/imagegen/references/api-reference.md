# AI Image Generation API Reference

Complete integration guide for each supported provider. All examples use TypeScript/fetch for consistency.

## Table of Contents
1. Together AI (FLUX)
2. FAL.ai
3. Replicate
4. Cloudflare Workers AI
5. Stability AI
6. ComfyUI (Self-hosted)
7. Automatic1111 (Self-hosted)

---

## 1. Together AI

**Best for**: Prototyping, free tier, fast iteration

### Endpoint
```
POST https://api.together.xyz/v1/images/generations
```

### Authentication
```
Authorization: Bearer $TOGETHER_API_KEY
```

### Models
| Model ID | Speed | Quality | Cost |
|----------|-------|---------|------|
| `black-forest-labs/FLUX.1-schnell-Free` | ~1s | Good | Free (3mo) |
| `black-forest-labs/FLUX.1-schnell` | ~1s | Good | $0.003/img |
| `black-forest-labs/FLUX.1.1-pro` | ~5s | Excellent | $0.04/img |
| `stabilityai/stable-diffusion-xl-base-1.0` | ~3s | Good | $0.006/img |

### Request Body
```typescript
interface TogetherImageRequest {
  model: string;           // Model ID from table above
  prompt: string;          // Text prompt, max ~2000 chars
  negative_prompt?: string; // What to avoid
  width: number;           // 256-1440, must be multiple of 64
  height: number;          // 256-1440, must be multiple of 64
  steps?: number;          // 1-50, default 4 for schnell, 20 for SDXL
  n?: number;              // 1-4, number of images
  seed?: number;           // Reproducibility
  response_format?: 'url' | 'b64_json'; // default: url
}
```

### Response
```typescript
interface TogetherImageResponse {
  id: string;
  model: string;
  data: Array<{
    url?: string;        // Temporary URL (~1hr expiry)
    b64_json?: string;   // Base64 encoded image
    index: number;
  }>;
}
```

### Full Example
```typescript
async function generateWithTogether(prompt: string, options?: {
  model?: string;
  width?: number;
  height?: number;
  negative_prompt?: string;
  steps?: number;
}): Promise<string> {
  const {
    model = 'black-forest-labs/FLUX.1-schnell-Free',
    width = 768,
    height = 1024,
    negative_prompt = 'extra fingers, bad anatomy, blurry, low quality, watermark',
    steps = 4,
  } = options ?? {};

  const res = await fetch('https://api.together.xyz/v1/images/generations', {
    method: 'POST',
    headers: {
      'Authorization': `Bearer ${process.env.TOGETHER_API_KEY}`,
      'Content-Type': 'application/json',
    },
    body: JSON.stringify({ model, prompt, negative_prompt, width, height, steps, n: 1 }),
  });

  if (!res.ok) {
    const err = await res.json();
    throw new Error(`Together AI error: ${err.error?.message ?? res.statusText}`);
  }

  const data = await res.json();
  return data.data[0].url;
}
```

### SDK Alternative
```bash
npm install together-ai
```
```typescript
import Together from 'together-ai';

const together = new Together({ apiKey: process.env.TOGETHER_API_KEY });
const response = await together.images.create({
  model: 'black-forest-labs/FLUX.1-schnell-Free',
  prompt: 'a dwarf paladin in golden armor',
  width: 768,
  height: 1024,
  steps: 4,
  n: 1,
});
const imageUrl = response.data[0].url;
```

### Portrait Aspect Ratios
- **768×1024** — Standard portrait (3:4)
- **832×1216** — Tall portrait (close to 2:3)
- **1024×1024** — Square (token/avatar)

---

## 2. FAL.ai

**Best for**: Production at scale, fastest inference, queue system

### Endpoint (Queue-based)
```
POST https://queue.fal.run/{model-id}
GET  https://queue.fal.run/{model-id}/requests/{request_id}/status
GET  https://queue.fal.run/{model-id}/requests/{request_id}
```

### Authentication
```
Authorization: Key $FAL_KEY
```

### Models
| Model ID | Speed | Cost |
|----------|-------|------|
| `fal-ai/flux/schnell` | ~0.5s | $0.003/img |
| `fal-ai/flux-pro/v1.1` | ~3s | $0.05/img |
| `fal-ai/stable-diffusion-v35-large` | ~4s | $0.04/img |

### Request (Flux)
```typescript
interface FalFluxRequest {
  prompt: string;
  image_size?: 'square_hd' | 'portrait_4_3' | 'portrait_16_9' | 'landscape_4_3' | 'landscape_16_9' | { width: number; height: number };
  num_inference_steps?: number;  // 1-50
  num_images?: number;           // 1-4
  seed?: number;
  enable_safety_checker?: boolean;
  output_format?: 'jpeg' | 'png';
}
```

### Response
```typescript
interface FalFluxResponse {
  images: Array<{
    url: string;
    width: number;
    height: number;
    content_type: string;
  }>;
  timings: { inference: number };
  seed: number;
  has_nsfw_concepts: boolean[];
  prompt: string;
}
```

### SDK (Recommended)
```bash
npm install @fal-ai/serverless-client
```
```typescript
import * as fal from '@fal-ai/serverless-client';

fal.config({ credentials: process.env.FAL_KEY });

const result = await fal.subscribe('fal-ai/flux/schnell', {
  input: {
    prompt: 'epic fantasy portrait of an elf ranger',
    image_size: 'portrait_4_3',
    num_inference_steps: 4,
    num_images: 1,
  },
  logs: true,
  onQueueUpdate: (update) => {
    if (update.status === 'IN_PROGRESS') {
      console.log(update.logs?.map(l => l.message));
    }
  },
});

const imageUrl = result.images[0].url;
```

### Next.js Proxy (to hide API key)
```typescript
// app/api/fal/proxy/route.ts
import { route } from '@fal-ai/serverless-proxy/nextjs';
export const { GET, POST } = route;
```

---

## 3. Replicate

**Best for**: Widest model selection, excellent documentation, pay-per-use

### Endpoint
```
POST https://api.replicate.com/v1/predictions
GET  https://api.replicate.com/v1/predictions/{id}
```

### Authentication
```
Authorization: Bearer $REPLICATE_API_TOKEN
```

### Popular Models for Character Art
| Model | Version | Cost |
|-------|---------|------|
| `black-forest-labs/flux-schnell` | latest | ~$0.003/img |
| `black-forest-labs/flux-1.1-pro` | latest | ~$0.04/img |
| `stability-ai/sdxl` | latest | ~$0.01/img |

### SDK (Recommended)
```bash
npm install replicate
```
```typescript
import Replicate from 'replicate';

const replicate = new Replicate({ auth: process.env.REPLICATE_API_TOKEN });

const output = await replicate.run('black-forest-labs/flux-schnell', {
  input: {
    prompt: 'dark fantasy character portrait of a tiefling warlock, dramatic rim lighting',
    aspect_ratio: '3:4',
    num_outputs: 1,
    output_format: 'webp',
    output_quality: 90,
  },
});

// output is an array of URLs
const imageUrl = output[0];
```

### Webhook Support
```typescript
const prediction = await replicate.predictions.create({
  model: 'black-forest-labs/flux-schnell',
  input: { prompt: '...' },
  webhook: 'https://your-app.com/api/replicate-webhook',
  webhook_events_filter: ['completed'],
});
```

---

## 4. Cloudflare Workers AI

**Best for**: Edge deployment, serverless, native JS/TS, global CDN

### Endpoint
```
POST https://api.cloudflare.com/client/v4/accounts/{account_id}/ai/run/{model}
```

### Authentication
```
Authorization: Bearer $CLOUDFLARE_API_TOKEN
```

### Models
| Model | Notes |
|-------|-------|
| `@cf/stabilityai/stable-diffusion-xl-base-1.0` | General purpose |
| `@cf/black-forest-labs/flux-1-schnell` | Fast, good quality |

### Workers Script Example
```typescript
// src/index.ts (Cloudflare Worker)
export default {
  async fetch(request: Request, env: Env): Promise<Response> {
    const { prompt } = await request.json<{ prompt: string }>();

    const response = await env.AI.run(
      '@cf/black-forest-labs/flux-1-schnell',
      { prompt, num_steps: 4 }
    );

    return new Response(response, {
      headers: { 'Content-Type': 'image/png' },
    });
  },
};
```

### REST API Example
```typescript
async function generateWithCloudflare(prompt: string): Promise<ArrayBuffer> {
  const res = await fetch(
    `https://api.cloudflare.com/client/v4/accounts/${process.env.CF_ACCOUNT_ID}/ai/run/@cf/black-forest-labs/flux-1-schnell`,
    {
      method: 'POST',
      headers: {
        'Authorization': `Bearer ${process.env.CLOUDFLARE_API_TOKEN}`,
        'Content-Type': 'application/json',
      },
      body: JSON.stringify({ prompt, num_steps: 4 }),
    }
  );
  return res.arrayBuffer(); // Returns raw image bytes
}
```

---

## 5. Stability AI

**Best for**: Highest anatomical accuracy, professional quality

### Endpoint
```
POST https://api.stability.ai/v2beta/stable-image/generate/sd3
POST https://api.stability.ai/v2beta/stable-image/generate/ultra
```

### Authentication
```
Authorization: Bearer $STABILITY_API_KEY
```

### Models & Pricing
| Endpoint | Model | Cost |
|----------|-------|------|
| `/generate/sd3` | SD 3.5 Large | $0.065/img |
| `/generate/sd3` | SD 3.5 Medium | $0.035/img |
| `/generate/ultra` | Ultra | $0.08/img |

### Request (multipart/form-data)
```typescript
async function generateWithStability(prompt: string, options?: {
  model?: string;
  negative_prompt?: string;
  aspect_ratio?: '1:1' | '16:9' | '21:9' | '2:3' | '3:2' | '4:5' | '5:4' | '9:16' | '9:21';
  output_format?: 'jpeg' | 'png' | 'webp';
}): Promise<Buffer> {
  const {
    model = 'sd3.5-large',
    negative_prompt = 'extra fingers, bad anatomy, blurry',
    aspect_ratio = '2:3',
    output_format = 'png',
  } = options ?? {};

  const formData = new FormData();
  formData.append('prompt', prompt);
  formData.append('model', model);
  formData.append('negative_prompt', negative_prompt);
  formData.append('aspect_ratio', aspect_ratio);
  formData.append('output_format', output_format);

  const res = await fetch('https://api.stability.ai/v2beta/stable-image/generate/sd3', {
    method: 'POST',
    headers: {
      'Authorization': `Bearer ${process.env.STABILITY_API_KEY}`,
      'Accept': 'image/*',
    },
    body: formData,
  });

  if (!res.ok) throw new Error(`Stability error: ${res.statusText}`);
  return Buffer.from(await res.arrayBuffer());
}
```

---

## 6. ComfyUI (Self-hosted)

**Best for**: Full control, custom workflows, LoRA models, no API limits

### Setup
```bash
docker run -d --gpus all -p 8188:8188 \
  -v $(pwd)/models:/opt/ComfyUI/models \
  -v $(pwd)/output:/opt/ComfyUI/output \
  ghcr.io/ai-dock/comfyui:latest
```

### API Endpoint
```
POST http://localhost:8188/prompt
GET  http://localhost:8188/history/{prompt_id}
GET  http://localhost:8188/view?filename={name}&subfolder={sub}&type=output
```

### Workflow API Example
```typescript
async function generateWithComfyUI(prompt: string): Promise<Buffer> {
  // ComfyUI uses a node-graph workflow format
  const workflow = {
    '3': {
      class_type: 'KSampler',
      inputs: {
        seed: Math.floor(Math.random() * 2 ** 32),
        steps: 20,
        cfg: 7,
        sampler_name: 'euler',
        scheduler: 'normal',
        denoise: 1,
        model: ['4', 0],
        positive: ['6', 0],
        negative: ['7', 0],
        latent_image: ['5', 0],
      },
    },
    '4': { class_type: 'CheckpointLoaderSimple', inputs: { ckpt_name: 'dreamshaperXL.safetensors' } },
    '5': { class_type: 'EmptyLatentImage', inputs: { width: 768, height: 1024, batch_size: 1 } },
    '6': { class_type: 'CLIPTextEncode', inputs: { text: prompt, clip: ['4', 1] } },
    '7': { class_type: 'CLIPTextEncode', inputs: { text: 'bad quality, blurry', clip: ['4', 1] } },
    '8': { class_type: 'VAEDecode', inputs: { samples: ['3', 0], vae: ['4', 2] } },
    '9': { class_type: 'SaveImage', inputs: { images: ['8', 0], filename_prefix: 'portrait' } },
  };

  const res = await fetch('http://localhost:8188/prompt', {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify({ prompt: workflow }),
  });

  const { prompt_id } = await res.json();

  // Poll for completion
  let history;
  while (true) {
    const histRes = await fetch(`http://localhost:8188/history/${prompt_id}`);
    history = await histRes.json();
    if (history[prompt_id]) break;
    await new Promise(r => setTimeout(r, 1000));
  }

  const outputs = history[prompt_id].outputs['9'].images[0];
  const imgRes = await fetch(
    `http://localhost:8188/view?filename=${outputs.filename}&subfolder=${outputs.subfolder}&type=output`
  );
  return Buffer.from(await imgRes.arrayBuffer());
}
```

### Recommended Models for D&D Art
- **DreamShaper XL** — Best all-round for character portraits
- **ZavyChromaXL** — Cinematic lighting, concept art style
- **DnD Darkest Fantasy LoRA** — Trained specifically on D&D artwork

---

## 7. Automatic1111 (Self-hosted)

**Best for**: Familiar UI, extensive extension ecosystem

### Setup
```bash
docker run -d --gpus all -p 7860:7860 \
  -e COMMANDLINE_ARGS="--api" \
  ghcr.io/ai-dock/stable-diffusion-webui:latest
```

### API Endpoint
```
POST http://localhost:7860/sdapi/v1/txt2img
```

### Request
```typescript
interface A1111Request {
  prompt: string;
  negative_prompt?: string;
  width?: number;       // default 512
  height?: number;      // default 512
  steps?: number;       // default 20
  cfg_scale?: number;   // default 7
  sampler_name?: string; // 'Euler a', 'DPM++ 2M Karras', etc.
  seed?: number;
  batch_size?: number;
  n_iter?: number;
}
```

### Response
```typescript
interface A1111Response {
  images: string[];     // Base64 encoded
  parameters: object;
  info: string;
}
```

### Example
```typescript
async function generateWithA1111(prompt: string): Promise<Buffer> {
  const res = await fetch('http://localhost:7860/sdapi/v1/txt2img', {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify({
      prompt,
      negative_prompt: 'extra fingers, bad anatomy, blurry, low quality',
      width: 768,
      height: 1024,
      steps: 25,
      cfg_scale: 7,
      sampler_name: 'DPM++ 2M Karras',
    }),
  });

  const data = await res.json();
  return Buffer.from(data.images[0], 'base64');
}
```

---

## Quick Decision Matrix

| Need | Provider | Why |
|------|----------|-----|
| Free prototyping | Together AI (Flux schnell Free) | 3 months free, simple REST API |
| Cheapest at scale | FAL.ai ($0.003/img schnell) | Queue system handles bursts |
| Best developer experience | Replicate | Webhook support, great docs |
| Edge/serverless | Cloudflare Workers AI | Native JS, global CDN |
| Highest quality | Stability AI (SD 3.5 Large) | Best anatomy, professional |
| Full control | ComfyUI | Custom LoRAs, no limits |
| Extension ecosystem | Automatic1111 | ControlNet, ADetailer, etc. |

## Error Handling Patterns

```typescript
// Unified wrapper with retry + fallback
async function generateImage(
  prompt: string,
  providers: Array<'together' | 'fal' | 'replicate'> = ['together', 'fal']
): Promise<string> {
  for (const provider of providers) {
    try {
      switch (provider) {
        case 'together': return await generateWithTogether(prompt);
        case 'fal': return await generateWithFal(prompt);
        case 'replicate': return await generateWithReplicate(prompt);
      }
    } catch (err) {
      console.warn(`${provider} failed:`, err);
      continue; // Try next provider
    }
  }
  throw new Error('All image generation providers failed');
}
```

## Rate Limits Summary

| Provider | Free Tier | Rate Limit |
|----------|-----------|------------|
| Together AI | 3 months unlimited (schnell-Free) | 60 req/min |
| FAL.ai | $10 free credits | Concurrency-based |
| Replicate | None (pay-per-use) | 600 req/min |
| Cloudflare | 10k neurons/day free | Per-account |
| Stability AI | 25 free credits | 150 req/10s |
