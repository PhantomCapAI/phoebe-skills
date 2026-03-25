---
name: fal-images
description: AI image generation via fal.ai API. Generate images from text prompts using FLUX and other models. Use env var FAL_KEY.
---

# fal.ai — AI Image Generation

Generate images from text prompts. Fast, high quality, multiple models.

## Generate an Image

```bash
exec curl -s -X POST "https://queue.fal.run/fal-ai/flux/schnell" \
  -H "Authorization: Key $FAL_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "prompt": "YOUR PROMPT HERE",
    "image_size": "landscape_16_9",
    "num_images": 1
  }'
```

## Models Available

| Model | Endpoint | Best For |
|-------|----------|----------|
| FLUX Schnell | `fal-ai/flux/schnell` | Fast generation (4 steps) |
| FLUX Dev | `fal-ai/flux/dev` | Higher quality |
| FLUX Pro | `fal-ai/flux-pro` | Best quality |
| Stable Diffusion XL | `fal-ai/fast-sdxl` | Classic SD |

## Image Sizes

`square_hd`, `square`, `portrait_4_3`, `portrait_16_9`, `landscape_4_3`, `landscape_16_9`

## Response Format

```json
{
  "images": [
    {
      "url": "https://fal.media/files/...",
      "width": 1024,
      "height": 1024
    }
  ],
  "seed": 12345
}
```

The URL is temporary — download or share immediately.

## Use Cases

- Blog post featured images (for DailyWisdomHub)
- Social media visuals (for @phantomcap_ai)
- Product mockups (for Gumroad)
- Avatar/branding images
- Thumbnails for content pipeline

## Tips

- Be specific in prompts: "minimalist digital art of a purple lobster on dark background, clean lines" > "lobster"
- Add style keywords: "professional", "minimalist", "photorealistic", "digital art"
- Use Schnell for drafts, Dev/Pro for finals
