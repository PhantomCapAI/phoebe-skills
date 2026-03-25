[![Skills](https://img.shields.io/badge/skills-17-blue?style=flat-square)](https://github.com/PhantomCapAI/phantom-skills)
[![License](https://img.shields.io/badge/license-Phantom%20Capital%20Royalty%20v1.0-purple?style=flat-square)](LICENSE)
[![Platform](https://img.shields.io/badge/platform-OpenClaw-black?style=flat-square)](https://phantomcapital.live)
[![Marketplace](https://img.shields.io/badge/marketplace-phantomcapital.live-green?style=flat-square)](https://phantomcapital.live)

# Phantom Skills -- Capability Toolkit for AI Agents

A curated catalog of **17 skill files** that teach AI agents how to use real-world APIs and tools. Each skill is a self-contained directory with a `SKILL.md` file that an agent can read, understand, and execute autonomously.

Skills span search, trading, crypto, security, identity, email, infrastructure, content generation, and more. Designed for the [OpenClaw](https://phantomcapital.live) agent runtime, but portable to any system that can parse structured markdown instructions.

---

## Skill Catalog

### Search and Research

| Skill | Description | API / Env Var |
|-------|-------------|---------------|
| [brave-search](./brave-search/) | Web search using Brave Search API. Structured results, news, freshness filtering. | Brave Search API / `BRAVE_API_KEY` |
| [tavily-search](./tavily-search/) | AI-powered search returning summarized answers with cited sources. Ideal for research queries. | Tavily API / `TAVILY_API_KEY` |
| [firecrawl](./firecrawl/) | Web scraping and crawling. Extracts clean markdown from any URL, handles JS rendering. | Firecrawl API / `FIRECRAWL_API_KEY` |

### Trading and DeFi

| Skill | Description | API / Env Var |
|-------|-------------|---------------|
| [bags-trading](./bags-trading/) | Full Bags.fm suite -- token launch, trading, fee claiming, Dexscreener listing, partner fees. | Bags.fm API / `BAGS_API_KEY`, `BAGS_PARTNER_KEY` |
| [pc-token-launch](./pc-token-launch/) | Complete $PC token launch preparation on Bags.fm. Logo generation, creation, fee config, launch, and marketing. | Bags.fm API / `BAGS_API_KEY`, `PHOEBE_SOL_KEY`, `FAL_KEY` |
| [stripe-payments](./stripe-payments/) | Payment processing via Stripe. Check balances, list transactions, manage payouts. | Stripe API / `STRIPE_SECRET_KEY` |

### Security and OSINT

| Skill | Description | API / Env Var |
|-------|-------------|---------------|
| [whitehat](./whitehat/) | Ethical security auditing for your own services. SSL checks, header analysis, API fuzzing, dependency audit, secrets scanning. | None (curl + Node.js) |
| [greyhat](./greyhat/) | OSINT, defensive recon, threat intelligence, and incident response using public data sources. | None (public APIs) |

### Identity and Crypto

| Skill | Description | API / Env Var |
|-------|-------------|---------------|
| [agent-identity](./agent-identity/) | Cryptographic identity for AI agents. Ed25519 keypair so agents can prove who they are across sessions, models, and platforms. | None (Node.js crypto) |
| [helius-solana](./helius-solana/) | Enhanced Solana RPC and DAS API. Token balances, NFT data, transaction history, webhooks. | Helius API / `HELIUS_API_KEY`, `HELIUS_RPC_URL` |

### Email and Communication

| Skill | Description | API / Env Var |
|-------|-------------|---------------|
| [agentmail](./agentmail/) | Send and receive email via AgentMail API. Full inbox management for AI agents. | AgentMail API / `AGENTMAIL_API_KEY` |
| [resend-email](./resend-email/) | Transactional email via Resend. Automated notifications, receipts, and system emails. | Resend API / `RESEND_KEY` |
| [email-drip](./email-drip/) | Automated email drip sequences. Multi-day nurture campaigns via Resend API. | Resend API / `RESEND_KEY` |

### Content and Publishing

| Skill | Description | API / Env Var |
|-------|-------------|---------------|
| [blog-templates](./blog-templates/) | 10 structured blog post templates. Hook, body, product CTA, and email capture. Optimized for LLM generation and WordPress publishing. | OpenRouter API / `OPENROUTER_API_KEY` |
| [fal-images](./fal-images/) | AI image generation via fal.ai. Text-to-image with FLUX, Stable Diffusion, and other models. | fal.ai API / `FAL_KEY` |

### Infrastructure

| Skill | Description | API / Env Var |
|-------|-------------|---------------|
| [zeabur-infra](./zeabur-infra/) | Manage Zeabur infrastructure. Deploy services, manage env vars, restart, check logs. | Zeabur API / `ZEABUR_API_TOKEN` |

### AI Models

| Skill | Description | API / Env Var |
|-------|-------------|---------------|
| [model-switcher](./model-switcher/) | Toggle between LLM models for different tasks. Route to the right model based on cost, speed, and capability. | OpenRouter API / `AGENT_MODEL`, `MODEL_PRIMARY` |

---

## Installation

### For OpenClaw Agents

1. Clone the repository:

```bash
git clone https://github.com/PhantomCapAI/phantom-skills.git
```

2. Point your agent's skill loader to the `phoebe-skills` directory:

```bash
export SKILLS_DIR="/path/to/phantom-skills/phoebe-skills"
```

3. Set the required environment variables for each skill you want to use. Refer to the table above for the specific variables each skill requires.

4. The agent reads the `SKILL.md` file from any skill directory at runtime. No compilation or build step is needed.

### Manual Usage

Each skill is a standalone markdown file. You can read any `SKILL.md` directly and follow the documented API calls using `curl`, `node`, or any HTTP client.

---

## Contributing

Contributions are welcome. To add a new skill:

1. Fork the repository.
2. Create a new directory under `phoebe-skills/` named after your skill (lowercase, hyphenated).
3. Add a `SKILL.md` file with the following structure:

```markdown
---
name: your-skill-name
description: One-line description. Mention the API and required env vars.
---

# Skill Title

Brief explanation of what this skill does.

## API Calls

Document each endpoint with complete curl/fetch examples.
Include required headers, parameters, and expected responses.
```

4. Submit a pull request to `main` with a clear description of the skill's purpose and category.

---

## Links

- **Marketplace:** [phantomcapital.live](https://phantomcapital.live)
- **GitHub:** [PhantomCapAI/phantom-skills](https://github.com/PhantomCapAI/phantom-skills)

---

## License

Phantom Capital Royalty License v1.0
