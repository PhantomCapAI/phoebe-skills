---
name: resend-email
description: Transactional email via Resend API. For automated notifications, receipts, and system emails. Use env var RESEND_KEY.
---

# Resend — Transactional Email

For automated emails: purchase receipts, notifications, alerts, onboarding sequences.

## Send an Email

```bash
exec curl -s -X POST "https://api.resend.com/emails" \
  -H "Authorization: Bearer $RESEND_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "from": "Phantom Capital <onboarding@resend.dev>",
    "to": ["recipient@example.com"],
    "subject": "Subject here",
    "html": "<p>HTML content</p>"
  }'
```

## Send to Multiple Recipients

```json
{
  "from": "Phantom Capital <onboarding@resend.dev>",
  "to": ["one@example.com", "two@example.com"],
  "subject": "Update from Phantom Capital",
  "html": "<h1>Hello</h1><p>Content here</p>"
}
```

## Check Email Status

```bash
exec curl -s "https://api.resend.com/emails/EMAIL_ID" \
  -H "Authorization: Bearer $RESEND_KEY"
```

## When to Use Resend vs AgentMail

- **Resend**: Automated/transactional emails (receipts, alerts, sequences). Better deliverability for bulk.
- **AgentMail**: Conversational email (replies, threads, inbox monitoring). Better for 1:1 communication.

## Use Cases

- Purchase confirmation emails (phantom-skills marketplace)
- Weekly digest emails for DailyWisdomHub subscribers
- Alert emails when revenue thresholds hit
- Onboarding sequences for new users
