---
name: email-drip
description: Automated email drip sequences for DailyWisdomHub subscribers. Nurture leads to Gumroad purchase. Uses Resend API.
---

# Email Drip — Automated Nurture Sequences

Convert DailyWisdomHub email captures into Gumroad customers. Automated 7-day sequence via Resend API.

## The Sequence

### Day 0 — Welcome (immediate)
```bash
exec curl -s -X POST "https://api.resend.com/emails" \
  -H "Authorization: Bearer $RESEND_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "from": "Phantom Capital <onboarding@resend.dev>",
    "to": ["SUBSCRIBER_EMAIL"],
    "subject": "Welcome to Daily Wisdom Hub",
    "html": "<h2>Welcome.</h2><p>You just joined a community that values presence over productivity theater.</p><p>Over the next week, I will send you one insight per day. No fluff. No upsells (yet). Just things worth thinking about.</p><p>Tomorrow: why most morning routines fail.</p><p>— Phantom Capital</p>"
  }'
```

### Day 1 — Value
Subject: "Why most morning routines fail"
Body: Genuine insight about routine vs ritual. End with soft CTA: "We wrote a full guide on this. More on that later."

### Day 2 — Value
Subject: "The one question that changes everything"
Body: Philosophical/practical insight. Build trust. No sell.

### Day 3 — Story
Subject: "An AI built this email sequence"
Body: The Phantom Capital story. An autonomous AI building tools, writing content, running a business. Transparency as brand.

### Day 4 — Value + Soft CTA
Subject: "30 days of practice > 30 years of theory"
Body: Practical framework. End with: "We put 30 days of guided practices into one download: [Connection Reset](https://dailywisdomhub.gumroad.com/l/connection-reset) — $19."

### Day 5 — Social Proof
Subject: "What people are saying"
Body: Testimonials or blog engagement metrics. Build credibility. Link to best blog posts.

### Day 6 — Hard CTA
Subject: "Your 30-Day Connection Reset"
Body: Direct pitch for the Gumroad product. Benefits, what's included, price anchor ($19 = less than a lunch). Clear CTA button.

### Day 7 — Last Chance
Subject: "Last thing from me (for now)"
Body: Final pitch with urgency. After this, they go to the weekly newsletter. "If Connection Reset isn't for you, no worries — you'll still get weekly wisdom."

## Sending a Sequence

Track subscribers and their sequence position in a simple JSON file:

```bash
# Check sequence status
exec cat /home/node/.openclaw/workspace/email-drip/subscribers.json

# Add new subscriber
exec node -e "
const fs = require('fs');
const p = '/home/node/.openclaw/workspace/email-drip/subscribers.json';
const subs = fs.existsSync(p) ? JSON.parse(fs.readFileSync(p)) : [];
subs.push({email: 'NEW_EMAIL', day: 0, started: new Date().toISOString(), lastSent: null});
fs.writeFileSync(p, JSON.stringify(subs, null, 2));
console.log('Added. Total:', subs.length);
"
```

## Daily Cron Job

Add to Planner (run daily at 10 AM EST):
```bash
exec node -e "
const fs = require('fs');
const https = require('https');
const p = '/home/node/.openclaw/workspace/email-drip/subscribers.json';
if (!fs.existsSync(p)) process.exit(0);
const subs = JSON.parse(fs.readFileSync(p));
const today = new Date();

subs.forEach(sub => {
  const started = new Date(sub.started);
  const daysSince = Math.floor((today - started) / 86400000);
  if (daysSince > sub.day && sub.day < 7) {
    // Send next email in sequence
    sub.day = daysSince;
    sub.lastSent = today.toISOString();
    console.log('Send day', sub.day, 'to', sub.email);
    // Call Resend API here with appropriate template
  }
});

fs.writeFileSync(p, JSON.stringify(subs, null, 2));
"
```

## Rules
- Never send more than 1 email per day per subscriber
- Always include unsubscribe link
- Log all sends to Cipher
- Track open rates if Resend provides them
- After day 7, move subscriber to weekly newsletter list
