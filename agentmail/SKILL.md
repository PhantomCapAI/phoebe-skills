---
name: agentmail
description: Send and receive email via AgentMail API. Phoebe's email address is phantomcapital@agentmail.to. Use env var AGENTMAIL_API_KEY.
---

# AgentMail — Email for AI Agents

Your email address: **phantomcapital@agentmail.to**

## Send an Email

```bash
exec curl -s -X POST "https://api.agentmail.to/v0/addresses/phantomcapital@agentmail.to/messages" \
  -H "Authorization: Bearer $AGENTMAIL_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "to": [{"address": "recipient@example.com", "name": "Name"}],
    "subject": "Subject line",
    "body_text": "Plain text body",
    "body_html": "<p>HTML body</p>"
  }'
```

## Check Inbox

```bash
exec curl -s "https://api.agentmail.to/v0/addresses/phantomcapital@agentmail.to/threads" \
  -H "Authorization: Bearer $AGENTMAIL_API_KEY"
```

## Read a Thread

```bash
exec curl -s "https://api.agentmail.to/v0/addresses/phantomcapital@agentmail.to/threads/THREAD_ID/messages" \
  -H "Authorization: Bearer $AGENTMAIL_API_KEY"
```

## Reply to a Thread

```bash
exec curl -s -X POST "https://api.agentmail.to/v0/addresses/phantomcapital@agentmail.to/threads/THREAD_ID/messages" \
  -H "Authorization: Bearer $AGENTMAIL_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "body_text": "Reply content here"
  }'
```

## Use Cases

- Business correspondence from phantomcapital@agentmail.to
- Automated notifications to Sneaks
- Customer support for Phantom Capital products
- Newsletter distribution
- Partnership outreach

## Rules

- Always use professional tone for external emails
- CC Sneaks on anything financial or partnership-related
- Never send spam or unsolicited bulk email
- Log all sent emails to Cipher
