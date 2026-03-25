---
name: blog-templates
description: 10 structured blog post templates for DailyWisdomHub. Each includes hook, body, product CTA (Connection Reset $19), and email capture CTA. Optimized for Hermes 3 70B generation and WordPress publishing via WP REST API.
---

# Blog Templates — DailyWisdomHub Content Engine

Generate high-converting blog posts for DailyWisdomHub using structured templates. Each template targets a specific wellness topic, includes affiliate and email capture CTAs, and is optimized for SEO.

## Model Configuration

Use **Hermes 3 70B** for all blog generation. It produces the best creative output with natural voice.

```bash
export BLOG_MODEL="nousresearch/hermes-3-llama-3.1-70b"
```

### Hermes 3 70B — Generation Settings

When calling OpenRouter for blog content, use these parameters:

```javascript
import fetch from "node:fetch";

const generateBlogPost = async (systemPrompt, userPrompt) => {
  const res = await fetch("https://openrouter.ai/api/v1/chat/completions", {
    method: "POST",
    headers: {
      "Authorization": `Bearer ${process.env.OPENROUTER_API_KEY}`,
      "Content-Type": "application/json",
      "HTTP-Referer": "https://dailywisdomhub.com",
      "X-Title": "DailyWisdomHub Blog Generator"
    },
    body: JSON.stringify({
      model: "nousresearch/hermes-3-llama-3.1-70b",
      messages: [
        { role: "system", content: systemPrompt },
        { role: "user", content: userPrompt }
      ],
      temperature: 0.8,
      top_p: 0.95,
      max_tokens: 3000,
      frequency_penalty: 0.3,
      presence_penalty: 0.4
    })
  });
  const data = await res.json();
  return data.choices[0].message.content;
};

export { generateBlogPost };
```

**Why these settings:**
- `temperature: 0.8` — warm enough for creative voice, controlled enough for coherence
- `frequency_penalty: 0.3` — reduces repetitive phrasing (common in wellness content)
- `presence_penalty: 0.4` — encourages topic diversity within a post
- `top_p: 0.95` — broad sampling for natural language variation
- `max_tokens: 3000` — enough for a full 800-1200 word post with HTML formatting

### System Prompt for All Templates

```
You are a wellness content writer for DailyWisdomHub, a brand by Phantom Capital. Your voice is calm, direct, and grounded. You avoid toxic positivity, hustle culture clichés, and empty affirmations. You write like someone who has done the inner work — not someone selling the fantasy of it. Every post should feel like advice from a wise friend, not a guru. Use second person ("you"). Short paragraphs. No fluff.
```

---

## Shared CTA Blocks

### Product CTA — Connection Reset ($19)

Use this naturally within each post, placed after the most compelling insight:

```html
<div class="cta-product" style="background:#f8f5f0;border-left:4px solid #2c5f2d;padding:20px;margin:30px 0;border-radius:4px;">
  <strong>Go deeper with Connection Reset</strong>
  <p>A 30-day guided practice to rebuild your relationship with yourself. Daily prompts, breathwork sequences, and reflection exercises — all in one downloadable guide.</p>
  <a href="https://dailywisdomhub.gumroad.com/l/connection-reset" style="display:inline-block;background:#2c5f2d;color:#fff;padding:10px 24px;border-radius:4px;text-decoration:none;font-weight:bold;">Get Connection Reset — $19</a>
</div>
```

### Email Capture CTA

Place at the end of every post, after the closing thought:

```html
<div class="cta-email" style="background:#fafafa;border:1px solid #e0e0e0;padding:20px;margin:30px 0;border-radius:4px;text-align:center;">
  <strong>Get one insight like this, every week.</strong>
  <p>No spam. No fluff. Just things worth thinking about.</p>
  <form action="YOUR_EMAIL_ENDPOINT" method="POST" style="margin-top:12px;">
    <input type="email" name="email" placeholder="your@email.com" required style="padding:10px;width:220px;border:1px solid #ccc;border-radius:4px;" />
    <button type="submit" style="padding:10px 20px;background:#2c5f2d;color:#fff;border:none;border-radius:4px;cursor:pointer;font-weight:bold;">Subscribe</button>
  </form>
</div>
```

---

## Template 1: Morning Routines

**Topic:** Why most morning routines are performative — and what actually works.

### SEO
- **Title format:** "Morning Routines That Actually Work (No 4 AM Wake-Up Required)"
- **Meta description:** "Skip the ice baths and 4 AM alarms. Here are morning practices backed by science that take 15 minutes and change your entire day."
- **Keywords:** morning routine for beginners, simple morning habits, mindful morning practice, morning ritual ideas, daily wellness routine

### Structure

**Hook (2-3 sentences):**
Open with a contrarian take on viral morning routines. Call out the performative 4 AM / cold plunge / journaling-for-an-hour aesthetic. Establish credibility by naming the real problem: most morning routines are designed for content, not for humans.

**Body (3-4 sections):**
1. *The problem with copying routines* — what works for someone else probably does not work for you. One-size-fits-all fails.
2. *The 3-element morning* — introduce a simple framework: movement (2 min), stillness (5 min), intention (1 min). Keep it minimal and doable.
3. *Why consistency beats complexity* — the boring 10-minute practice you do every day beats the elaborate 90-minute one you abandon by Thursday.
4. *Building your own* — give readers a fill-in-the-blank framework. Emphasize experimentation.

**Product CTA placement:** After section 2, where the reader is most motivated. Frame Connection Reset as 30 days of guided mornings.

**Closing:**
One sentence reminder that the best morning routine is the one you actually do. Then email capture CTA.

---

## Template 2: Mindfulness

**Topic:** Mindfulness without the woo — practical awareness for skeptics.

### SEO
- **Title format:** "Mindfulness for People Who Think Mindfulness Is Nonsense"
- **Meta description:** "You do not need incense, singing bowls, or a meditation cushion. Here is what mindfulness actually is — and why it works even if you are skeptical."
- **Keywords:** mindfulness for beginners, practical mindfulness, mindfulness without meditation, everyday mindfulness, skeptic meditation guide

### Structure

**Hook (2-3 sentences):**
Acknowledge that mindfulness has a branding problem. It sounds like something sold at a yoga retreat. Then redefine it: mindfulness is just noticing what is happening without immediately reacting.

**Body (3-4 sections):**
1. *What mindfulness actually is* — strip it down to the neuroscience. Attention regulation, not spiritual transcendence.
2. *Three micro-practices* — (a) the one-breath reset, (b) the name-five-things grounding, (c) the body scan at a red light. Each takes under 60 seconds.
3. *The compound effect* — small awareness moments stack into less reactivity, better sleep, fewer arguments. Cite research if possible.
4. *When mindfulness backfires* — honest take on when it does not help (active trauma, dissociation). Acknowledge limits.

**Product CTA placement:** After section 3. Frame Connection Reset as a structured 30-day path for someone who wants to try this without the woo.

**Closing:**
You do not have to sit on a mountain to pay attention to your own life. Then email capture CTA.

---

## Template 3: Productivity

**Topic:** The case against optimizing every minute of your day.

### SEO
- **Title format:** "Why Doing Less Might Be the Most Productive Thing You Do"
- **Meta description:** "Productivity culture has convinced us that every minute must be optimized. What if the most productive thing you can do is stop trying so hard?"
- **Keywords:** anti-productivity, doing less is more, sustainable productivity, slow productivity tips, productivity burnout recovery

### Structure

**Hook (2-3 sentences):**
Open with the absurdity of productivity content: apps that track your focus, 47-step systems, and the guilt of an unoptimized afternoon. Ask: what if the system is the problem?

**Body (3-4 sections):**
1. *The diminishing returns of optimization* — after a point, more systems create more overhead than output.
2. *The three-task day* — introduce the concept of choosing only three meaningful tasks per day. Explain the psychology of completion vs. overwhelm.
3. *Rest as a productivity strategy* — deliberate rest is not laziness. It is how your brain consolidates and creates. Reference default mode network.
4. *A permission slip* — give the reader explicit permission to do less today. Reframe rest as an investment.

**Product CTA placement:** After section 2. Frame Connection Reset as a 30-day reset that includes structured rest and reflection — the missing half of productivity.

**Closing:**
You were not built to be a machine. Then email capture CTA.

---

## Template 4: Relationships

**Topic:** The one skill that improves every relationship you have.

### SEO
- **Title format:** "The One Skill That Transforms Every Relationship (It Is Not Communication)"
- **Meta description:** "Everyone says communication is key. But the skill that actually transforms relationships is one most people never practice."
- **Keywords:** relationship skills, emotional regulation relationships, better relationships tips, how to improve relationships, presence in relationships

### Structure

**Hook (2-3 sentences):**
Set up the expectation (communication) then subvert it. The real skill is emotional regulation — the ability to stay present when things get uncomfortable instead of reacting, withdrawing, or deflecting.

**Body (3-4 sections):**
1. *Why communication advice fails* — you cannot communicate well when your nervous system is activated. Regulation comes first, communication second.
2. *The 90-second rule* — neuroanatomist research on how emotions chemically last about 90 seconds. Everything after that is a story you are telling yourself.
3. *Practical regulation tools* — (a) the pause before responding, (b) naming the emotion out loud, (c) the physical anchor (feet on floor, hands on knees).
4. *What changes when you regulate first* — less fights, more repair, deeper trust. The relationship transforms because you transformed.

**Product CTA placement:** After section 3. Frame Connection Reset as 30 days of building this regulation muscle through daily practice.

**Closing:**
The best thing you can bring to any relationship is a regulated nervous system. Then email capture CTA.

---

## Template 5: Self-Compassion

**Topic:** Why being hard on yourself is not actually motivating.

### SEO
- **Title format:** "Self-Compassion Is Not Soft — It Is the Hardest (and Most Useful) Skill"
- **Meta description:** "Beating yourself up does not make you better. Research shows self-compassion outperforms self-criticism for motivation, resilience, and growth."
- **Keywords:** self-compassion practice, how to stop being hard on yourself, self-compassion vs self-criticism, self-kindness exercises, inner critic healing

### Structure

**Hook (2-3 sentences):**
Call out the cultural myth that self-criticism drives excellence. Most high achievers are not successful because of their inner critic — they succeed despite it.

**Body (3-4 sections):**
1. *The self-criticism trap* — it feels productive. It is not. It triggers threat response, narrows thinking, and leads to avoidance.
2. *What self-compassion actually is* — not letting yourself off the hook. It is treating yourself with the same fairness you would treat a friend. Three components: self-kindness, common humanity, mindfulness.
3. *The research* — self-compassion is correlated with greater motivation, less procrastination, better recovery from failure. It is not weakness — it is resilience infrastructure.
4. *One practice to start today* — the self-compassion break. Notice suffering, connect to common humanity, offer yourself kindness. Takes 2 minutes.

**Product CTA placement:** After section 4. Frame Connection Reset as a daily guide that builds self-compassion through structured practice over 30 days.

**Closing:**
You can be ambitious and kind to yourself at the same time. Then email capture CTA.

---

## Template 6: Digital Detox

**Topic:** You do not need a digital detox — you need digital boundaries.

### SEO
- **Title format:** "Forget Digital Detox — You Need a Digital Boundary Practice"
- **Meta description:** "Going off-grid for a weekend will not fix your relationship with technology. Building daily digital boundaries will."
- **Keywords:** digital detox alternative, screen time boundaries, healthy tech habits, digital minimalism tips, phone addiction solutions

### Structure

**Hook (2-3 sentences):**
Digital detox culture sells the fantasy of escape. But you are coming back to your phone on Monday. The real solution is not dramatic breaks — it is sustainable boundaries.

**Body (3-4 sections):**
1. *Why detoxes do not work long-term* — binge/purge cycles with technology mirror diet culture. Restriction leads to rebound.
2. *Boundaries over bans* — introduce the concept of usage boundaries vs. total avoidance. Examples: no phone for the first 30 minutes after waking, no notifications after 8 PM, one social media check per day.
3. *The phone-free zone* — pick one physical space (bedroom, dining table) and make it permanently phone-free. Small, maintainable, life-changing.
4. *What fills the gap* — when you remove the scroll, you need to replace it. Suggest: 5-minute breathwork, a page of a book, a walk outside, or just silence.

**Product CTA placement:** After section 4. Frame Connection Reset as 30 days of practices that replace screen time with presence.

**Closing:**
Your phone is a tool. Start using it like one. Then email capture CTA.

---

## Template 7: Journaling

**Topic:** Journaling does not have to mean writing pages of feelings.

### SEO
- **Title format:** "Journaling for People Who Hate Journaling (5 Formats That Actually Stick)"
- **Meta description:** "You do not need a leather-bound notebook and an hour of free time. Here are 5 journaling formats that take under 5 minutes."
- **Keywords:** journaling for beginners, easy journaling methods, quick journal prompts, minimalist journaling, journaling alternatives

### Structure

**Hook (2-3 sentences):**
The journaling industry wants you to write morning pages, gratitude lists, and stream-of-consciousness entries. If that works for you, great. If it does not, you are not broken — the format is.

**Body (5 mini-sections, one per format):**
1. *The one-line journal* — write a single sentence about today. That is it. Lower the bar until you cannot fail.
2. *The question journal* — answer one question per day. Provide 7 starter questions.
3. *The brain dump* — set a timer for 3 minutes. Write whatever comes out. Do not reread it.
4. *The score journal* — rate your day 1-10. Optionally add one word for why. Track patterns over weeks.
5. *The letter journal* — write a short letter to yourself. Past self, future self, or current self having a hard day.

**Product CTA placement:** After format 3. Frame Connection Reset as including 30 days of guided journal prompts designed for people who do not love journaling.

**Closing:**
The best journal is the one you actually open. Then email capture CTA.

---

## Template 8: Breathwork

**Topic:** Three breathing techniques that change your state in under 2 minutes.

### SEO
- **Title format:** "3 Breathing Techniques That Calm Your Nervous System in Under 2 Minutes"
- **Meta description:** "Your breath is the fastest tool you have for changing how you feel. Here are 3 techniques backed by science that work in under 2 minutes."
- **Keywords:** breathing techniques for anxiety, calming breathwork, box breathing guide, physiological sigh technique, nervous system breathwork

### Structure

**Hook (2-3 sentences):**
You already have the best anxiety tool on the market. It is free, portable, and works in under 2 minutes. It is your breath. This is not meditation — it is physiology.

**Body (3 technique sections + 1 context section):**
1. *The physiological sigh* — double inhale through nose, long exhale through mouth. Discovered by Stanford neuroscience lab. The fastest known method to reduce cortisol in real time.
2. *Box breathing* — inhale 4 counts, hold 4, exhale 4, hold 4. Used by Navy SEALs. Works for focus, calm, and pre-performance nerves.
3. *Extended exhale breathing* — inhale for 4 counts, exhale for 8. Activates parasympathetic nervous system. Best for winding down before sleep.
4. *When to use which* — provide a simple decision tree: stressed right now (physiological sigh), need focus (box breathing), winding down (extended exhale).

**Product CTA placement:** After section 4. Frame Connection Reset as including 30 days of guided breathwork sequences paired with reflection prompts.

**Closing:**
Two minutes. No app required. Then email capture CTA.

---

## Template 9: Boundaries

**Topic:** How to set boundaries without becoming a boundaries cliche.

### SEO
- **Title format:** "How to Set Boundaries Without Feeling Like a Self-Help Cliche"
- **Meta description:** "Boundaries are not about scripts and ultimatums. Here is how to set real boundaries that protect your energy without damaging your relationships."
- **Keywords:** setting boundaries guide, healthy boundaries tips, how to say no, boundaries without guilt, emotional boundaries practice

### Structure

**Hook (2-3 sentences):**
Somewhere between "I have no boundaries" and "I boundary everything and everyone" is a livable middle ground. The internet made boundaries into a personality trait. They are actually just a quiet skill.

**Body (3-4 sections):**
1. *What a boundary actually is* — it is not a wall. It is information about what you need to stay well in a relationship. It protects the relationship, not just you.
2. *The three types* — (a) time boundaries (when you are available), (b) emotional boundaries (what you will and will not absorb), (c) energy boundaries (what you have capacity for right now).
3. *How to communicate one* — skip the scripts. Use the formula: "I need [X] because [Y]. Can we figure this out together?" Collaborative, not combative.
4. *The hardest part* — enforcing them when someone pushes back. The boundary is only real if you hold it. This is where most people fold.

**Product CTA placement:** After section 3. Frame Connection Reset as 30 days of building the internal clarity that makes boundaries easier to set and hold.

**Closing:**
A boundary is not a punishment. It is an act of respect — for yourself and the relationship. Then email capture CTA.

---

## Template 10: Sleep Hygiene

**Topic:** Sleep hygiene is not about your mattress — it is about your nervous system.

### SEO
- **Title format:** "Sleep Hygiene Beyond the Basics (What Actually Fixes Bad Sleep)"
- **Meta description:** "You already know about blue light and caffeine cutoffs. Here is what sleep experts actually recommend when the basics are not working."
- **Keywords:** sleep hygiene tips that work, how to fix bad sleep, nervous system sleep, sleep anxiety solutions, better sleep without medication

### Structure

**Hook (2-3 sentences):**
You have read the same sleep tips everywhere: dark room, no screens, cool temperature. If those worked, you would be asleep already. The real issue is usually not your environment — it is your nervous system.

**Body (3-4 sections):**
1. *Why standard sleep tips fail* — they address symptoms, not the root. If your nervous system is stuck in fight-or-flight, a cool room will not save you.
2. *The wind-down window* — 60 minutes before bed is not "stop using screens" time. It is active nervous system downregulation time. Give specific practices: extended exhale breathing, body scan, legs up the wall.
3. *The worry dump* — keep a notepad by your bed. Before lying down, write every open loop in your head. Transfer it from your brain to paper. Your brain can let go of what is captured.
4. *Consistency over perfection* — same wake time every day (including weekends) matters more than any other single factor. Your circadian rhythm is a clock — treat it like one.

**Product CTA placement:** After section 2. Frame Connection Reset as including evening wind-down practices and guided breathwork designed to teach your nervous system how to let go.

**Closing:**
Good sleep is not something you buy. It is something you build. Then email capture CTA.

---

## WordPress Publishing

Publish posts to DailyWisdomHub via the WP REST API using application password authentication.

### Publish a New Post

```javascript
import fetch from "node:fetch";

const publishPost = async ({ title, content, excerpt, slug, categories, tags }) => {
  const wpUrl = process.env.WP_SITE_URL; // e.g. https://dailywisdomhub.com
  const wpUser = process.env.WP_SELF_APP_USER;
  const wpPass = process.env.WP_SELF_APP_PASS;
  const auth = Buffer.from(`${wpUser}:${wpPass}`).toString("base64");

  const res = await fetch(`${wpUrl}/wp-json/wp/v2/posts`, {
    method: "POST",
    headers: {
      "Authorization": `Basic ${auth}`,
      "Content-Type": "application/json"
    },
    body: JSON.stringify({
      title,
      content,
      excerpt,
      slug,
      status: "draft",
      categories: categories || [],
      tags: tags || []
    })
  });

  const post = await res.json();
  console.log(`Post created: ${post.id} — ${post.link}`);
  return post;
};

export { publishPost };
```

### Publish via CLI (one-shot)

```bash
exec node -e "
import('node:fetch').then(({ default: fetch }) => {
  const auth = Buffer.from(process.env.WP_SELF_APP_USER + ':' + process.env.WP_SELF_APP_PASS).toString('base64');
  fetch(process.env.WP_SITE_URL + '/wp-json/wp/v2/posts', {
    method: 'POST',
    headers: {
      'Authorization': 'Basic ' + auth,
      'Content-Type': 'application/json'
    },
    body: JSON.stringify({
      title: 'POST_TITLE_HERE',
      content: 'POST_HTML_CONTENT_HERE',
      excerpt: 'META_DESCRIPTION_HERE',
      slug: 'post-slug-here',
      status: 'draft'
    })
  }).then(r => r.json()).then(p => console.log('Published:', p.id, p.link));
});
"
```

### Update an Existing Post

```bash
exec node -e "
import('node:fetch').then(({ default: fetch }) => {
  const auth = Buffer.from(process.env.WP_SELF_APP_USER + ':' + process.env.WP_SELF_APP_PASS).toString('base64');
  fetch(process.env.WP_SITE_URL + '/wp-json/wp/v2/posts/POST_ID', {
    method: 'PUT',
    headers: {
      'Authorization': 'Basic ' + auth,
      'Content-Type': 'application/json'
    },
    body: JSON.stringify({
      content: 'UPDATED_HTML_CONTENT',
      status: 'publish'
    })
  }).then(r => r.json()).then(p => console.log('Updated:', p.id, p.status));
});
"
```

### List Recent Posts

```bash
exec node -e "
import('node:fetch').then(({ default: fetch }) => {
  const auth = Buffer.from(process.env.WP_SELF_APP_USER + ':' + process.env.WP_SELF_APP_PASS).toString('base64');
  fetch(process.env.WP_SITE_URL + '/wp-json/wp/v2/posts?per_page=10&status=any', {
    headers: { 'Authorization': 'Basic ' + auth }
  }).then(r => r.json()).then(posts => {
    posts.forEach(p => console.log(p.id, p.status, p.slug, p.title.rendered));
  });
});
"
```

---

## Full Generation + Publish Workflow

End-to-end: pick a template, generate with Hermes 3, inject CTAs, publish to WordPress.

```javascript
import fetch from "node:fetch";

const OPENROUTER_KEY = process.env.OPENROUTER_API_KEY;
const WP_URL = process.env.WP_SITE_URL;
const WP_AUTH = Buffer.from(
  `${process.env.WP_SELF_APP_USER}:${process.env.WP_SELF_APP_PASS}`
).toString("base64");

const SYSTEM_PROMPT = `You are a wellness content writer for DailyWisdomHub, a brand by Phantom Capital. Your voice is calm, direct, and grounded. You avoid toxic positivity, hustle culture clichés, and empty affirmations. You write like someone who has done the inner work — not someone selling the fantasy of it. Every post should feel like advice from a wise friend, not a guru. Use second person ("you"). Short paragraphs. No fluff. Output in HTML format ready for WordPress.`;

const PRODUCT_CTA = `<div class="cta-product" style="background:#f8f5f0;border-left:4px solid #2c5f2d;padding:20px;margin:30px 0;border-radius:4px;"><strong>Go deeper with Connection Reset</strong><p>A 30-day guided practice to rebuild your relationship with yourself. Daily prompts, breathwork sequences, and reflection exercises — all in one downloadable guide.</p><a href="https://dailywisdomhub.gumroad.com/l/connection-reset" style="display:inline-block;background:#2c5f2d;color:#fff;padding:10px 24px;border-radius:4px;text-decoration:none;font-weight:bold;">Get Connection Reset — $19</a></div>`;

const EMAIL_CTA = `<div class="cta-email" style="background:#fafafa;border:1px solid #e0e0e0;padding:20px;margin:30px 0;border-radius:4px;text-align:center;"><strong>Get one insight like this, every week.</strong><p>No spam. No fluff. Just things worth thinking about.</p><form action="YOUR_EMAIL_ENDPOINT" method="POST" style="margin-top:12px;"><input type="email" name="email" placeholder="your@email.com" required style="padding:10px;width:220px;border:1px solid #ccc;border-radius:4px;" /><button type="submit" style="padding:10px 20px;background:#2c5f2d;color:#fff;border:none;border-radius:4px;cursor:pointer;font-weight:bold;">Subscribe</button></form></div>`;

const generate = async (templatePrompt) => {
  const res = await fetch("https://openrouter.ai/api/v1/chat/completions", {
    method: "POST",
    headers: {
      "Authorization": `Bearer ${OPENROUTER_KEY}`,
      "Content-Type": "application/json",
      "HTTP-Referer": "https://dailywisdomhub.com",
      "X-Title": "DailyWisdomHub Blog Generator"
    },
    body: JSON.stringify({
      model: "nousresearch/hermes-3-llama-3.1-70b",
      messages: [
        { role: "system", content: SYSTEM_PROMPT },
        { role: "user", content: templatePrompt }
      ],
      temperature: 0.8,
      top_p: 0.95,
      max_tokens: 3000,
      frequency_penalty: 0.3,
      presence_penalty: 0.4
    })
  });
  const data = await res.json();
  return data.choices[0].message.content;
};

const publish = async ({ title, content, excerpt, slug }) => {
  const fullContent = content + PRODUCT_CTA + EMAIL_CTA;
  const res = await fetch(`${WP_URL}/wp-json/wp/v2/posts`, {
    method: "POST",
    headers: {
      "Authorization": `Basic ${WP_AUTH}`,
      "Content-Type": "application/json"
    },
    body: JSON.stringify({
      title,
      content: fullContent,
      excerpt,
      slug,
      status: "draft"
    })
  });
  return res.json();
};

export { generate, publish, PRODUCT_CTA, EMAIL_CTA, SYSTEM_PROMPT };
```

---

## SEO Quick Reference

| Topic | Title Pattern | Primary Keywords | Secondary Keywords |
|-------|--------------|-----------------|-------------------|
| Morning Routines | "Morning Routines That Actually Work (...)" | morning routine, mindful morning | simple morning habits, morning ritual |
| Mindfulness | "Mindfulness for People Who ..." | practical mindfulness, mindfulness for beginners | everyday mindfulness, mindfulness exercises |
| Productivity | "Why Doing Less ..." | sustainable productivity, anti-productivity | slow productivity, productivity burnout |
| Relationships | "The One Skill That ..." | relationship skills, emotional regulation | better relationships, presence in relationships |
| Self-Compassion | "Self-Compassion Is Not Soft ..." | self-compassion practice, inner critic | self-kindness, self-compassion vs self-criticism |
| Digital Detox | "Forget Digital Detox ..." | digital boundaries, screen time | digital minimalism, phone addiction |
| Journaling | "Journaling for People Who Hate ..." | easy journaling, minimalist journaling | journal prompts, quick journaling |
| Breathwork | "3 Breathing Techniques That ..." | breathing techniques anxiety, calming breathwork | box breathing, physiological sigh |
| Boundaries | "How to Set Boundaries Without ..." | setting boundaries, healthy boundaries | how to say no, emotional boundaries |
| Sleep Hygiene | "Sleep Hygiene Beyond the Basics ..." | fix bad sleep, sleep hygiene tips | nervous system sleep, sleep anxiety |

### SEO Rules
- **Title:** under 60 characters, include primary keyword, use parenthetical hooks
- **Meta description:** 150-160 characters, include primary keyword, end with a value proposition
- **H2 headings:** include secondary keywords naturally
- **Internal links:** link to at least 2 other DailyWisdomHub posts per article
- **Word count:** 800-1200 words — long enough for SEO, short enough to finish
- **URL slug:** 3-5 words, lowercase, hyphenated, keyword-rich

---

## Rules

1. Always generate with `nousresearch/hermes-3-llama-3.1-70b` — it has the best voice for this content
2. Every post must include exactly one product CTA (Connection Reset) and one email capture CTA
3. Publish as `draft` first — never publish directly to `publish` without review
4. All content is attributed to Phantom Capital — no personal names
5. Do not use toxic positivity, hustle culture language, or guru-speak
6. Every post must be self-contained — a reader should get real value even if they never click a CTA
7. Rotate templates — do not publish the same topic two weeks in a row
8. Log every generation and publish event to Cipher for tracking
