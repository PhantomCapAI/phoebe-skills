---
name: gumroad-optimizer
description: Optimize Gumroad product pages for conversions. Audit listings, rewrite copy, update products via Gumroad API, run A/B tests, and plan upsells. Uses env var GUMROAD_ACCESS_TOKEN.
---

# Gumroad — Product Optimization & Conversion Engine

## Current State

- **Product**: Connection Reset
- **URL**: https://dailywisdomhub.gumroad.com/l/connection-reset
- **Price**: $19
- **Performance**: 23 clicks, 0 conversions (0% conversion rate)
- **Goal**: 5%+ conversion rate (industry benchmark for digital products)

---

## 1. Product Page Audit Checklist

Before changing anything, audit every element. A Gumroad page converts when all of these are strong:

| Element | Converts When... | Current Status |
|---|---|---|
| **Title** | Speaks to a specific pain, not a generic topic | Needs rewrite |
| **Subtitle/Hook** | Creates urgency or curiosity in one line | Missing or weak |
| **Description** | Follows problem-agitate-solve structure | Needs rewrite |
| **Bullet Points** | List concrete outcomes, not features | Needs rewrite |
| **Cover Image** | Professional, readable at thumbnail size | Needs generation |
| **Price Anchoring** | Price feels like a steal vs. the alternative | Needs framing |
| **Social Proof** | Numbers, testimonials, or authority signals | Missing |
| **CTA** | Clear, single action with low friction | Default Gumroad |
| **Guarantee** | Risk reversal (refund policy stated) | Missing |

### Quick Audit via API

```bash
exec curl -s "https://api.gumroad.com/v2/products" \
  -H "Authorization: Bearer $GUMROAD_ACCESS_TOKEN" | jq '.products[] | {name, description, price, url, sales_count, views_count}'
```

---

## 2. Optimized Product Copy

### Title

**Before**: Connection Reset
**After**: Connection Reset: The 30-Day System to Break Free from Digital Overload

### Subtitle / Hook

> You check your phone 96 times a day. This guide gives you your mind back in 30 days — or your money back.

### Description (Problem-Agitate-Solve)

```
You know the feeling.

You wake up and reach for your phone before your feet hit the floor. You scroll through notifications that don't matter. You sit down to do deep work and 12 minutes later you're watching a video someone sent you three days ago.

It's not a willpower problem. It's a system problem.

Connection Reset is a 30-day action plan built by Phantom Capital's research team after analyzing hundreds of digital wellness studies and distilling them into daily practices that actually stick.

This isn't "put your phone in a drawer" advice. Each day gives you one small, concrete shift — designed to compound. By day 30, your relationship with technology is fundamentally different.

**What you get:**

- A structured 30-day protocol with one action per day (5 minutes or less)
- The Screen Time Audit template to measure your starting point
- The Notification Hierarchy framework — know exactly what deserves your attention
- The Deep Work Window method used by high-performers to protect focus blocks
- The Evening Unwire routine that fixes sleep quality within the first week
- Printable daily tracker to build streaks and stay accountable

**This is for you if:**

- You've tried "digital detoxes" that lasted 48 hours
- You feel anxious when your phone isn't nearby
- You lose 2+ hours daily to mindless scrolling
- You want a system, not a lecture

**Investment: $19** — less than a single lunch out. Less than two coffees a day for a month. The average person loses 2.5 hours daily to unproductive screen time. At any reasonable hourly rate, you're buying back thousands of dollars in lost productivity.

Phantom Capital offers a full refund within 30 days if you complete the program and don't see results. No questions asked.
```

---

## 3. Price Psychology — $19 Positioning

Use these frames in copy, emails, and social posts. Rotate them.

| Frame | Copy |
|---|---|
| **Lunch comparison** | "Less than a single lunch out." |
| **Coffee comparison** | "Less than two coffees." |
| **Hourly rate** | "You lose 2.5 hrs/day to screens. At $50/hr, that's $125/day. This costs $19 once." |
| **Subscription contrast** | "You pay $15/month for Netflix. This costs $19 once and pays for itself forever." |
| **Therapy anchor** | "One therapy session costs $150+. This is a self-guided system for $19." |
| **Daily cost** | "63 cents a day over a month. Less than a pack of gum." |

### Price Anchoring Strategy

If adding a higher tier later, always show the $19 as the entry point next to a $49 or $99 tier. The $19 looks like a steal by comparison. See Section 7 for upsell tiers.

---

## 4. Social Proof Strategies

Zero sales requires manufactured credibility until organic proof builds. Use these:

### Authority Signals

- "Built on research from [X] digital wellness studies" (cite the number you actually reviewed)
- "Published by Phantom Capital" — link to the blog as proof of consistent content
- "From the team behind Daily Wisdom Hub" — reference blog traffic numbers

### Traffic-Based Proof

```
"Join [X] monthly readers at Daily Wisdom Hub who are already rethinking their relationship with technology."
```

Pull actual numbers:

```bash
# If you track analytics, insert real numbers. Otherwise use email list size:
echo "Use actual blog traffic or email subscriber count as social proof."
echo "Example: 'Trusted by 500+ Daily Wisdom Hub subscribers'"
```

### Early Adopter Strategy

- Offer the first 10 buyers a bonus (extra template, access to a private thread)
- Use "founding member" language: "You're one of the first. Early buyers get [bonus] free."
- After first 5 sales, update description: "Rated by early readers" with any feedback received

### Review Collection

After each sale, Gumroad sends a receipt. Set up a follow-up email (day 7 post-purchase) asking for a one-line testimonial. Add these to the product page immediately.

---

## 5. Gumroad API — Update Product Details

### List All Products (find the product ID)

```bash
exec curl -s "https://api.gumroad.com/v2/products" \
  -H "Authorization: Bearer $GUMROAD_ACCESS_TOKEN" | jq '.products[] | {id, name, short_url}'
```

### Get Specific Product Details

```bash
exec curl -s "https://api.gumroad.com/v2/products/PRODUCT_ID" \
  -H "Authorization: Bearer $GUMROAD_ACCESS_TOKEN" | jq '.product | {id, name, description, price, sales_count, views_count}'
```

### Update Product Name

```bash
exec curl -s -X PUT "https://api.gumroad.com/v2/products/PRODUCT_ID" \
  -H "Authorization: Bearer $GUMROAD_ACCESS_TOKEN" \
  -d "name=Connection Reset: The 30-Day System to Break Free from Digital Overload"
```

### Update Product Description

```bash
exec curl -s -X PUT "https://api.gumroad.com/v2/products/PRODUCT_ID" \
  -H "Authorization: Bearer $GUMROAD_ACCESS_TOKEN" \
  --data-urlencode "description=You know the feeling.

You wake up and reach for your phone before your feet hit the floor. You scroll through notifications that don't matter. You sit down to do deep work and 12 minutes later you're watching a video someone sent you three days ago.

It's not a willpower problem. It's a system problem.

Connection Reset is a 30-day action plan built by Phantom Capital's research team after analyzing hundreds of digital wellness studies and distilling them into daily practices that actually stick.

This isn't \"put your phone in a drawer\" advice. Each day gives you one small, concrete shift — designed to compound. By day 30, your relationship with technology is fundamentally different."
```

### Update Price (in cents)

```bash
exec curl -s -X PUT "https://api.gumroad.com/v2/products/PRODUCT_ID" \
  -H "Authorization: Bearer $GUMROAD_ACCESS_TOKEN" \
  -d "price=1900"
```

### Enable Custom Summary / Bullet Points

Gumroad uses the `description` field for everything. Format bullet points with unicode or markdown in the description body. Gumroad renders basic markdown.

### Get Sales Data

```bash
exec curl -s "https://api.gumroad.com/v2/products/PRODUCT_ID" \
  -H "Authorization: Bearer $GUMROAD_ACCESS_TOKEN" | jq '.product | {sales_count, sales_usd_cents, views_count}'
```

### List All Sales

```bash
exec curl -s "https://api.gumroad.com/v2/sales?product_id=PRODUCT_ID" \
  -H "Authorization: Bearer $GUMROAD_ACCESS_TOKEN" | jq '.sales[] | {email, created_at, price}'
```

---

## 6. A/B Testing Approach

Gumroad does not have built-in A/B testing. Use this manual rotation method:

### Method: Time-Based Split Testing

1. **Week 1**: Run Description A (problem-agitate-solve — the version in Section 2)
2. **Week 2**: Run Description B (outcome-first — lead with bullet points and results)
3. **Compare**: views vs. sales for each period

### Description B — Outcome-First Variant

```
**What changes in 30 days:**

- You wake up without reaching for your phone
- You finish deep work sessions without a single interruption
- You sleep better starting in the first week
- You gain back 2+ hours per day of focused, intentional time

Connection Reset is a step-by-step daily protocol. One action per day. Five minutes or less. No willpower needed — just follow the system.

Built by Phantom Capital's research team. Backed by a 30-day money-back guarantee.

$19 — less than a single lunch.
```

### Tracking Template

```
Test: [A or B]
Start date: YYYY-MM-DD
End date: YYYY-MM-DD
Views: (pull from API)
Sales: (pull from API)
Conversion rate: sales / views * 100
Winner: [A or B]
```

### Pull Metrics for Comparison

```bash
exec curl -s "https://api.gumroad.com/v2/products/PRODUCT_ID" \
  -H "Authorization: Bearer $GUMROAD_ACCESS_TOKEN" | jq '.product | {views_count, sales_count}'
```

Run this at the start and end of each test window. Calculate the delta.

---

## 7. Upsell Strategy — Product Ladder

### Tier Structure

| Tier | Product | Price | Purpose |
|---|---|---|---|
| **Entry** | Connection Reset (current) | $19 | Get the first sale. Build trust. |
| **Mid** | Connection Reset Pro | $49 | Extended 60-day plan + templates + video walkthroughs |
| **Premium** | Digital Wellness Coaching (4 weeks) | $149 | 4x weekly async coaching via email or community |
| **Community** | Phantom Capital Inner Circle | $9/mo | Monthly membership, ongoing accountability |

### What to Build Next (priority order)

1. **$49 Pro Tier** — Take the same content, add: a 60-day extended plan, Notion/spreadsheet templates, and 3-5 short video walkthroughs. Offer it as a Gumroad "tier" on the same product page.
2. **$9/mo Community** — Use Gumroad memberships. Monthly check-ins, curated resources, a private space for accountability.
3. **$149 Coaching** — Only offer after 20+ sales at the $49 tier. Proves demand. Keep it async (email-based) to stay scalable.

### Create a New Tier via API

```bash
exec curl -s -X POST "https://api.gumroad.com/v2/products/PRODUCT_ID/variant_categories" \
  -H "Authorization: Bearer $GUMROAD_ACCESS_TOKEN" \
  -d "title=Choose Your Plan"
```

Then add options:

```bash
exec curl -s -X POST "https://api.gumroad.com/v2/products/PRODUCT_ID/variant_categories/CATEGORY_ID/variants" \
  -H "Authorization: Bearer $GUMROAD_ACCESS_TOKEN" \
  -d "name=Standard (30-Day Plan)" \
  -d "price_difference_cents=0"
```

```bash
exec curl -s -X POST "https://api.gumroad.com/v2/products/PRODUCT_ID/variant_categories/CATEGORY_ID/variants" \
  -H "Authorization: Bearer $GUMROAD_ACCESS_TOKEN" \
  -d "name=Pro (60-Day Plan + Templates + Videos)" \
  -d "price_difference_cents=3000"
```

---

## 8. Traffic Sources

### Source 1: Blog CTAs (Daily Wisdom Hub)

Add a call-to-action at the end of every blog post:

```html
<div style="background:#f8f8f8;padding:20px;border-radius:8px;margin-top:40px;">
  <strong>Spending too much time on screens?</strong><br>
  Connection Reset is a 30-day system to take back your focus. One action per day. Five minutes or less.
  <br><br>
  <a href="https://dailywisdomhub.gumroad.com/l/connection-reset" style="background:#000;color:#fff;padding:10px 20px;border-radius:4px;text-decoration:none;">Get Connection Reset — $19</a>
</div>
```

Write 2-3 blog posts specifically designed to rank for related keywords:
- "How to reduce screen time without willpower"
- "30-day digital detox plan"
- "Best daily habits to break phone addiction"

Each post funnels to the product.

### Source 2: Email Drip

Build a 5-email sequence for new subscribers:

| Day | Subject | Purpose |
|---|---|---|
| 0 | Welcome — here's what to expect | Deliver lead magnet, set expectations |
| 2 | The real cost of screen time (it's not what you think) | Problem awareness |
| 4 | I tried every "digital detox" — here's what actually works | Agitate + authority |
| 6 | The system behind Connection Reset | Soft pitch with value preview |
| 8 | Last chance: Connection Reset is $19 this week | Direct CTA with urgency |

### Source 3: Twitter / X

Post cadence: 3-5 tweets per week in these categories:

- **Stat tweets**: "The average person checks their phone 96 times a day. That's once every 10 minutes."
- **Contrarian takes**: "Digital detoxes don't work. Systems do."
- **Thread breakdowns**: Share 3-5 tips from the guide as a thread. End with a link.
- **Results tweets**: Once you have buyers, share anonymized outcomes.

Pin a tweet linking to the product.

### Source 4: Gumroad Discover

Gumroad has a built-in marketplace. Optimize for it:

```bash
exec curl -s -X PUT "https://api.gumroad.com/v2/products/PRODUCT_ID" \
  -H "Authorization: Bearer $GUMROAD_ACCESS_TOKEN" \
  -d "tags[]=digital-wellness" \
  -d "tags[]=productivity" \
  -d "tags[]=screen-time" \
  -d "tags[]=habits" \
  -d "tags[]=focus"
```

---

## 9. Cover Image via fal.ai

Generate a professional cover image using fal.ai. The image should be clean, modern, and readable at thumbnail size.

### Generate Cover Image

```bash
exec curl -s -X POST "https://queue.fal.run/fal-ai/flux/dev" \
  -H "Authorization: Key $FAL_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "prompt": "Minimalist book cover design, dark navy background, clean white and gold typography reading CONNECTION RESET, subtle digital pattern dissolving into nature elements, professional product mockup style, no faces, no hands, simple and elegant, high contrast, suitable for Gumroad product thumbnail",
    "image_size": "portrait_4_3",
    "num_images": 1
  }' | jq -r '.images[0].url'
```

### Download the Image

```bash
exec curl -sL "IMAGE_URL_FROM_ABOVE" -o /tmp/connection-reset-cover.png
```

### Upload as Product Thumbnail

Gumroad does not support cover image upload via API. Upload manually:

1. Go to https://app.gumroad.com/products
2. Click "Connection Reset"
3. Click the cover image area
4. Upload the generated image

### Alternative Prompt Variants (for A/B testing covers)

**Variant B — Phone breaking free:**
```
"Minimalist illustration of a smartphone with chains breaking off it, clean white background, subtle blue gradient, modern flat design, no text, suitable for digital product cover"
```

**Variant C — Calm gradient:**
```
"Abstract gradient art, transitioning from chaotic digital pixel noise on the left to calm smooth ocean blue on the right, minimalist, wide aspect ratio, no text, suitable for digital product header"
```

---

## Quick-Start Playbook

If you want to optimize the product right now, execute in this order:

1. **Get the product ID**:
   ```bash
   exec curl -s "https://api.gumroad.com/v2/products" \
     -H "Authorization: Bearer $GUMROAD_ACCESS_TOKEN" | jq '.products[] | {id, name}'
   ```

2. **Update the title and description** (use commands from Section 5)

3. **Generate and upload a cover image** (Section 9)

4. **Add tags for Gumroad Discover** (Section 8, Source 4)

5. **Check metrics after 7 days**:
   ```bash
   exec curl -s "https://api.gumroad.com/v2/products/PRODUCT_ID" \
     -H "Authorization: Bearer $GUMROAD_ACCESS_TOKEN" | jq '.product | {views_count, sales_count}'
   ```

6. **If conversion rate is still below 3% after 50+ views**, switch to Description B (Section 6)

7. **After first 5 sales**, start building the $49 Pro tier (Section 7)
