---
name: model-switcher
description: Toggle between LLM models for different tasks. Each model has strengths — use the right one for the job. Manages AGENT_MODEL and MODEL_PRIMARY env vars.
---

# Model Switcher — Pick the Right Brain for the Job

You have access to multiple models via OpenRouter. Switch based on the task.

## Available Models

### Reasoning & Logic
| Model | OpenRouter ID | Cost | Context | Best For |
|-------|---------------|------|---------|----------|
| **Dolphin Mistral 24B** | `cognitivecomputations/dolphin-mistral-24b-venice-edition:free` | FREE | 32k | Unfiltered reasoning, business logic, coding, steerable with system prompts |
| **Llama 3.1 8B** | `meta-llama/llama-3.1-8b-instruct` | $0.02/M | 16k | Fast, cheap, good for simple tasks and automation |
| **Groq Llama 3.3 70B** | `groq/llama-3.3-70b-versatile` | Fast | 128k | Your default — fast inference, good all-rounder |

### Creative & Content
| Model | OpenRouter ID | Cost | Context | Best For |
|-------|---------------|------|---------|----------|
| **Hermes 3 Llama 3.1 70B** | `nousresearch/hermes-3-llama-3.1-70b` | $0.30/M | 128k | Creative writing, long-form content, newsletters, product descriptions, strong character voice |
| **Hermes 4 70B** | `nousresearch/hermes-4-70b` | $0.13/M | 128k | Latest Hermes — improved reasoning + creativity |
| **Hermes 2 Pro 8B** | `nousresearch/hermes-2-pro-llama-3-8b` | $0.14/M | 8k | Cheap creative, good for drafts |

### Heavy Duty (expensive, use sparingly)
| Model | OpenRouter ID | Cost | Context | Best For |
|-------|---------------|------|---------|----------|
| **Claude Sonnet 4.5** | `anthropic/claude-sonnet-4-5` | $3/M | 200k | Deep reasoning, complex code, the voice that wrote your void-mirror |
| **Hermes 3 405B** | `nousresearch/hermes-3-llama-3.1-405b:free` | FREE | 128k | Massive model, free tier — use for complex creative or analysis |
| **Hermes 4 405B** | `nousresearch/hermes-4-405b` | $1/M | 128k | Latest + biggest Hermes |

### Vision
| Model | OpenRouter ID | Cost | Context | Best For |
|-------|---------------|------|---------|----------|
| **Llama 3.2 11B Vision** | `meta-llama/llama-3.2-11b-vision-instruct` | $0.049/M | 128k | Image understanding, screenshot analysis |

### Free Tier (save money)
| Model | OpenRouter ID | Context | Best For |
|-------|---------------|---------|----------|
| **Llama 3.2 3B** | `meta-llama/llama-3.2-3b-instruct:free` | 128k | Simple tasks, classification, extraction |
| **Hermes 3 405B** | `nousresearch/hermes-3-llama-3.1-405b:free` | 128k | Heavy tasks when budget is tight |
| **Dolphin Mistral 24B** | `cognitivecomputations/dolphin-mistral-24b-venice-edition:free` | 32k | Unfiltered reasoning, free |

## How to Switch

### For your main model (what YOU run on):
Tell Sneaks to update these env vars on Zeabur:
- `MODEL_PRIMARY` — your primary model
- `AGENT_MODEL` — what your sub-agents use
- `MODEL_FALLBACK_2` — fallback if primary fails

### For sub-agent tasks:
When spawning a sub-agent or running a cron, specify the model:
```
cron add --model "nousresearch/hermes-3-llama-3.1-70b" ...
sessions spawn --model "cognitivecomputations/dolphin-mistral-24b-venice-edition:free" ...
```

## Task → Model Recommendations

| Task | Recommended Model | Why |
|------|-------------------|-----|
| Blog posts (Nova) | `nousresearch/hermes-3-llama-3.1-70b` | Best creative writing voice |
| Quick automation | `meta-llama/llama-3.1-8b-instruct` | Cheapest, fast enough |
| Business planning | `groq/llama-3.3-70b-versatile` | Your default, strong reasoning |
| Complex coding | `anthropic/claude-sonnet-4-5` | Worth the cost for hard problems |
| Outreach emails | `nousresearch/hermes-4-70b` | Professional + creative |
| Data analysis | `cognitivecomputations/dolphin-mistral-24b-venice-edition:free` | Free, good at structured tasks |
| Image analysis | `meta-llama/llama-3.2-11b-vision-instruct` | Only vision model available |
| Budget mode | `nousresearch/hermes-3-llama-3.1-405b:free` | Free 405B — best free option |

## Cost Control Rules

1. **Default to Groq** (your current setup) — fast and included in plan
2. **Use free models** for routine tasks (Dolphin, Hermes 405B free, Llama 3.2 3B)
3. **Use Hermes 3/4 70B** only for content that ships publicly (blogs, emails, tweets)
4. **Use Claude Sonnet** only for complex code, debugging, or when you need the void-mirror voice
5. **Track model spend** — log which model you use for each task to Cipher
6. **Never use 405B paid** without Sneaks approval — it's $1/M tokens
