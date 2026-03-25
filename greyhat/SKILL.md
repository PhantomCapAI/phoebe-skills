---
name: greyhat
description: OSINT, defensive recon, threat intelligence, and incident response. Gather public information, detect threats, trace attackers using only public data. No unauthorized access.
---

# Greyhat — OSINT & Defensive Recon

Know your threat landscape. Gather intelligence from public sources. Trace bad actors using what they leave in the open. Respond to incidents fast.

**Rule: You never cross the line into unauthorized access. Everything here uses public data, your own logs, and legitimate APIs.**

---

## 1. OSINT — Who Is Targeting You?

### Reverse IP / Domain Lookup
```bash
exec curl -s "https://api.search.brave.com/res/v1/web/search?q=site:who.is+phantomskills.zeabur.app" \
  -H "X-Subscription-Token: $BRAVE_API_KEY" -H "Accept: application/json" | head -100
```

### WHOIS on a Domain
```bash
exec curl -s "https://whois.freeaitools.me/?domain=SUSPECT-DOMAIN.com"
```

### DNS Records
```bash
exec node -e "
const dns = require('dns');
dns.resolveAny('SUSPECT-DOMAIN.com', (err, records) => {
  if (err) console.log('Error:', err.message);
  else console.log(JSON.stringify(records, null, 2));
});
"
```

### Check if an IP is Malicious
```bash
exec curl -s "https://api.abuseipdb.com/api/v2/check?ipAddress=SUSPECT_IP&maxAgeInDays=90" \
  -H "Key: YOUR_ABUSEIPDB_KEY" -H "Accept: application/json"
```

Free alternative (no key needed):
```bash
exec curl -s "https://ipinfo.io/SUSPECT_IP/json"
```

### Email OSINT — Check if an Email is Legit
```bash
exec curl -s "https://api.search.brave.com/res/v1/web/search?q=%22suspect@email.com%22" \
  -H "X-Subscription-Token: $BRAVE_API_KEY" -H "Accept: application/json"
```

### Username Search — Who is This Person?
```bash
# Search a username across platforms via Brave
exec curl -s "https://api.search.brave.com/res/v1/web/search?q=%22USERNAME%22+site:github.com+OR+site:twitter.com+OR+site:linkedin.com" \
  -H "X-Subscription-Token: $BRAVE_API_KEY" -H "Accept: application/json"
```

### Wallet OSINT — Trace a Solana Address
```bash
# Get transaction history of a suspect wallet
exec curl -s "https://api.helius.xyz/v0/addresses/SUSPECT_WALLET/transactions?api-key=$HELIUS_API_KEY&limit=20"
```

```bash
# Check wallet assets
exec curl -s -X POST "$HELIUS_RPC_URL" \
  -H "Content-Type: application/json" \
  -d '{"jsonrpc":"2.0","id":1,"method":"getAssetsByOwner","params":{"ownerAddress":"SUSPECT_WALLET","displayOptions":{"showFungible":true}}}'
```

### EVM Wallet Trace
```bash
exec curl -s "https://api.etherscan.io/api?module=account&action=txlist&address=SUSPECT_ADDRESS&startblock=0&endblock=99999999&page=1&offset=10&sort=desc"
```

### GitHub Recon — What Has Someone Built?
```bash
exec curl -s "https://api.github.com/users/SUSPECT_USERNAME/repos?sort=updated&per_page=10" \
  -H "Accept: application/vnd.github+json"
```

```bash
# Check their recent activity
exec curl -s "https://api.github.com/users/SUSPECT_USERNAME/events?per_page=10" \
  -H "Accept: application/vnd.github+json"
```

---

## 2. Threat Detection — Are You Under Attack?

### Check Your Stripe for Suspicious Charges
```bash
exec curl -s "https://api.stripe.com/v1/charges?limit=10" \
  -H "Authorization: Bearer $STRIPE_SECRET_KEY" | node -e "
const chunks=[];process.stdin.on('data',c=>chunks.push(c));
process.stdin.on('end',()=>{
  const d=JSON.parse(Buffer.concat(chunks));
  (d.data||[]).forEach(c=>{
    if(c.disputed||c.refunded||c.failure_code)
      console.log('ALERT:',c.id,c.amount,c.failure_code||'disputed/refunded',c.billing_details?.email);
  });
  if(!(d.data||[]).some(c=>c.disputed||c.refunded||c.failure_code))
    console.log('Clean — no suspicious charges');
});
"
```

### Check for Webhook Replay Attacks
Look at your own logs for repeated webhook deliveries:
```bash
exec grep -c "verify-result\|webhooks/stripe" /var/log/app/*.log 2>/dev/null || echo "Check Zeabur runtime logs for repeated webhook hits"
```

### Monitor Your GitHub for Unauthorized Changes
```bash
exec curl -s "https://api.github.com/repos/PhoebeBoss/phantom-skills/events?per_page=10" \
  -H "Authorization: token $GITHUB_TOKEN" | node -e "
const chunks=[];process.stdin.on('data',c=>chunks.push(c));
process.stdin.on('end',()=>{
  const events=JSON.parse(Buffer.concat(chunks));
  events.forEach(e=>{
    if(e.actor.login!=='PhoebeBoss')
      console.log('UNKNOWN ACTOR:',e.type,e.actor.login,e.created_at);
  });
  if(events.every(e=>e.actor.login==='PhoebeBoss'))
    console.log('Clean — only PhoebeBoss activity');
});
"
```

### DNS Hijack Check — Is Your Domain Still Yours?
```bash
exec node -e "
const dns = require('dns');
['phantomskills.zeabur.app','dailywisdomhub.zeabur.app'].forEach(d=>{
  dns.resolve4(d,(err,addrs)=>{
    console.log(d,'->', err ? 'FAILED: '+err.message : addrs.join(', '));
  });
});
"
```

Run periodically. If IPs change unexpectedly = possible hijack.

---

## 3. Incident Response — Something Is Wrong

### Step 1: Contain
```bash
# Restart compromised service immediately
exec curl -s -X POST "https://gateway.zeabur.com/graphql" \
  -H "Authorization: Bearer $ZEABUR_API_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"query":"mutation { restartService(serviceID: \"SERVICE_ID\", environmentID: \"ENV_ID\") }"}'
```

### Step 2: Preserve Evidence
```bash
# Dump runtime logs before they rotate
exec curl -s -X POST "https://gateway.zeabur.com/graphql" \
  -H "Authorization: Bearer $ZEABUR_API_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"query":"{ runtimeLogs(serviceID: \"SERVICE_ID\", environmentID: \"ENV_ID\", limit: 500) { message timestamp } }"}'
```

Save output to a timestamped file in your workspace.

### Step 3: Rotate Compromised Credentials
If you suspect a key is leaked:
1. Generate new key at the provider
2. Update Zeabur env var
3. Restart service
4. Invalidate old key at provider
5. Check git history for accidental commits of the key

**Tell Sneaks immediately if any of these are compromised:**
- STRIPE_SECRET_KEY (real money)
- PHOEBE_SOL_KEY / PHOEBE_EVM_KEY (wallets)
- GITHUB_TOKEN / GITHUB_PAT (code access)

### Step 4: Trace the Attack
Use the OSINT tools above to research:
- IP addresses from logs
- Email addresses from fake signups
- Wallet addresses from suspicious transactions
- Usernames from GitHub events

### Step 5: Block & Harden
- Add rate limiting if missing
- Add IP allowlists if possible
- Rotate all keys as precaution
- Run full whitehat audit
- Update the incident in Cipher log

---

## 4. Competitive Intelligence (Legal)

### What Are Competitors Building?
```bash
exec curl -s "https://api.search.brave.com/res/v1/web/search?q=openclaw+skill+marketplace+OR+AI+agent+marketplace+OR+LLM+plugin+store&count=10&freshness=pm" \
  -H "X-Subscription-Token: $BRAVE_API_KEY" -H "Accept: application/json"
```

### Track a Competitor's GitHub
```bash
exec curl -s "https://api.github.com/repos/OWNER/REPO/commits?per_page=5" \
  -H "Accept: application/vnd.github+json"
```

### Monitor Mentions of Phantom Capital
```bash
exec curl -s "https://api.search.brave.com/res/v1/web/search?q=%22phantom+capital%22+OR+%22phantomskills%22+OR+%22phantom-skills%22&freshness=pw" \
  -H "X-Subscription-Token: $BRAVE_API_KEY" -H "Accept: application/json"
```

---

## 5. Dark Web Monitoring (Passive)

You can't access .onion sites, but you can check if your data has been leaked:

### Check if Emails Are in Breaches
```bash
# Uses public breach databases
exec curl -s "https://api.search.brave.com/res/v1/web/search?q=%22phantomcapitallab@gmail.com%22+OR+%22phoebe@phantomcapital.ai%22+breach+OR+leak+OR+dump" \
  -H "X-Subscription-Token: $BRAVE_API_KEY" -H "Accept: application/json"
```

### Check if Your API Keys Appear in Public
```bash
# Search for your key prefixes in public code (GitHub code search)
exec curl -s "https://api.github.com/search/code?q=phantomskills+OR+phantom-skills+NOT+user:PhoebeBoss" \
  -H "Authorization: token $GITHUB_TOKEN" -H "Accept: application/vnd.github+json"
```

---

## Automated Daily Security Check

Run this every day (add to Planner cron):

1. DNS hijack check (are your domains still pointing right?)
2. GitHub event audit (any unknown actors?)
3. Stripe charge review (any disputes/failures?)
4. Brave search for your brand (any mentions?)
5. Check public code search for leaked keys
6. Log results to Cipher

---

## The Line

**You CAN:**
- Look up public WHOIS, DNS, IP info
- Read public GitHub profiles and repos
- Search public web for emails, usernames, wallets
- Trace blockchain transactions (they're public by design)
- Monitor your own services and logs
- Check public breach databases
- Research competitors using public info

**You CANNOT:**
- Access any system without authorization
- Brute force passwords or credentials
- Intercept network traffic
- Exploit vulnerabilities in others' systems
- DDoS or degrade anyone's service
- Social engineer access from humans
- Access private APIs without permission

**When in doubt: if you need a password, exploit, or unauthorized access to do it, DON'T.**
