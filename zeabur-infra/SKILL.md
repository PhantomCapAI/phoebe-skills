---
name: zeabur-infra
description: Manage Zeabur infrastructure — deploy services, manage env vars, restart, check logs. Use env var ZEABUR_API_TOKEN.
---

# Zeabur — Infrastructure Management

Manage your own infrastructure. Deploy, restart, check logs, manage env vars.

## List Projects

```bash
exec curl -s -X POST "https://gateway.zeabur.com/graphql" \
  -H "Authorization: Bearer $ZEABUR_API_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"query": "{ projects { edges { node { _id name } } } }"}'
```

## List Services in a Project

```bash
exec curl -s -X POST "https://gateway.zeabur.com/graphql" \
  -H "Authorization: Bearer $ZEABUR_API_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"query": "{ project(_id: \"PROJECT_ID\") { services { _id name status } } }"}'
```

## Restart a Service

```bash
exec curl -s -X POST "https://gateway.zeabur.com/graphql" \
  -H "Authorization: Bearer $ZEABUR_API_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"query": "mutation { restartService(serviceID: \"SERVICE_ID\", environmentID: \"ENV_ID\") }"}'
```

## Get Runtime Logs

```bash
exec curl -s -X POST "https://gateway.zeabur.com/graphql" \
  -H "Authorization: Bearer $ZEABUR_API_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"query": "{ runtimeLogs(serviceID: \"SERVICE_ID\", environmentID: \"ENV_ID\", limit: 50) { message timestamp } }"}'
```

## Known Services

| Service | Project | Purpose |
|---------|---------|---------|
| OpenClaw | pheobe | Your main runtime |
| phantom-skills | phantom-skills | Skill marketplace API |
| wordpress | pheobe | DailyWisdomHub blog |

## Safety Rules

- **Never delete services** without Sneaks approval
- **Never modify env vars on OpenClaw** without approval — you could break yourself
- You CAN restart services and check logs freely
- Log infrastructure changes to Cipher
