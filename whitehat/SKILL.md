---
name: whitehat
description: Ethical security auditing for your own services. SSL checks, header analysis, API fuzzing, dependency audit, secrets scanning. No external tools needed — runs with curl and node.
---

# White Hat Security — Defend What You Own

Audit your own infrastructure. No nmap, no nikto — just curl, node, and your brain. Only test services YOU control.

## YOUR SERVICES (authorized targets only)

| Service | URL | What It Runs |
|---------|-----|-------------|
| phantom-skills | https://phantomskills.zeabur.app | Express API, Stripe, Prisma |
| DailyWisdomHub | https://dailywisdomhub.zeabur.app | WordPress |
| OpenClaw | Internal (Zeabur service mesh) | Your runtime |

**NEVER scan anything else without written permission from the owner.**

---

## 1. Security Headers Audit

Check if your services have proper security headers:

```bash
exec curl -sI https://phantomskills.zeabur.app/health | head -30
```

**What to look for (missing = bad):**

| Header | Purpose | Fix |
|--------|---------|-----|
| `Strict-Transport-Security` | Force HTTPS | Add `helmet` middleware |
| `X-Content-Type-Options: nosniff` | Prevent MIME sniffing | Add `helmet` |
| `X-Frame-Options: DENY` | Prevent clickjacking | Add `helmet` |
| `Content-Security-Policy` | Prevent XSS | Add CSP header |
| `X-XSS-Protection` | Legacy XSS filter | Add `helmet` |
| `Referrer-Policy` | Control referrer leaks | Add `helmet` |

**Quick fix — add to Express app:**
```javascript
import helmet from "helmet";
app.use(helmet());
```

## 2. SSL/TLS Check

```bash
exec curl -svI https://phantomskills.zeabur.app 2>&1 | grep -E "SSL|TLS|certificate|expire|subject|issuer"
```

**Check for:**
- TLS 1.2 or 1.3 (not 1.0/1.1)
- Certificate not expired
- Certificate matches domain
- Strong cipher suite

## 3. API Endpoint Fuzzing

Test your own API for unexpected behavior:

```bash
# Test for missing auth on protected routes
exec curl -s https://phantomskills.zeabur.app/creator/payout/calculate -X POST -H "Content-Type: application/json" -d '{"startDate":"2026-01-01","endDate":"2026-12-31"}'

# Should return 401, NOT data. If it returns data = auth is broken.
```

```bash
# Test for SQL injection (should fail gracefully, not crash)
exec curl -s "https://phantomskills.zeabur.app/skills/'; DROP TABLE skills;--"

# Should return 404 with clean error, not a 500 or stack trace
```

```bash
# Test for XSS in query params
exec curl -s "https://phantomskills.zeabur.app/skills?search=<script>alert(1)</script>"

# Should return escaped/safe output, never raw HTML
```

```bash
# Test oversized payload (DoS check)
exec node -e "fetch('https://phantomskills.zeabur.app/auth/register',{method:'POST',headers:{'Content-Type':'application/json'},body:JSON.stringify({email:'a'.repeat(100000)+'@test.com'})}).then(r=>r.text()).then(console.log)"

# Should reject, not crash the server
```

## 4. Auth & Token Testing

```bash
# Test expired/invalid JWT
exec curl -s https://phantomskills.zeabur.app/skills -X POST \
  -H "Authorization: Bearer eyJhbGciOiJIUzI1NiJ9.eyJzdWIiOiJmYWtlIn0.invalid" \
  -H "Content-Type: application/json" \
  -d '{"slug":"test","name":"test","description":"test"}'

# Should return 401, not create a skill
```

```bash
# Test missing auth
exec curl -s https://phantomskills.zeabur.app/skills -X POST \
  -H "Content-Type: application/json" \
  -d '{"slug":"test","name":"test","description":"test"}'

# Should return 401
```

## 5. Stripe Webhook Signature Check

```bash
# Send fake webhook without valid signature — should reject
exec curl -s https://phantomskills.zeabur.app/webhooks/stripe \
  -X POST \
  -H "Content-Type: application/json" \
  -H "stripe-signature: fake_signature" \
  -d '{"type":"checkout.session.completed","data":{"object":{"id":"fake"}}}'

# MUST return 400 "Invalid signature". If it returns 200 = critical vuln.
```

## 6. GitHub Webhook Signature Check

```bash
# Send fake GitHub webhook — should reject
exec curl -s https://phantomskills.zeabur.app/skills/brave-search/verify-result \
  -X POST \
  -H "Content-Type: application/json" \
  -H "x-hub-signature-256: sha256=0000000000000000000000000000000000000000000000000000000000000000" \
  -d '{"run_id":"fake","conclusion":"success"}'

# MUST return 401. If it returns 200 = critical vuln — anyone can mark skills as verified.
```

## 7. Information Leakage Check

```bash
# Check if error pages leak stack traces
exec curl -s https://phantomskills.zeabur.app/nonexistent-route-12345

# Should return clean 404 JSON, NOT Express default HTML with file paths
```

```bash
# Check if server header leaks version info
exec curl -sI https://phantomskills.zeabur.app/health | grep -i "x-powered-by\|server"

# "X-Powered-By: Express" = leaking framework info. Fix: app.disable('x-powered-by')
```

## 8. Dependency Vulnerability Scan

Run this against your own repo:

```bash
exec sh -c "cd /path/to/phantom-skills && npm audit --json 2>/dev/null | head -50"
```

Or check a package.json directly:
```bash
exec node -e "
const pkg = require('./package.json');
const deps = Object.keys(pkg.dependencies || {});
console.log('Dependencies to audit:', deps.join(', '));
console.log('Run: npm audit --production');
"
```

## 9. Secrets Scanning

Check your own repos for accidentally committed secrets:

```bash
# Search for common secret patterns in workspace
exec grep -rn "sk_live\|sk_test\|ghp_\|github_pat\|Bearer \|password.*=\|secret.*=" /home/node/.openclaw/workspace/ --include="*.js" --include="*.json" --include="*.md" | grep -v node_modules | grep -v ".env" | head -20
```

**If you find secrets in code files (not .env), that's a leak. Fix immediately.**

## 10. Rate Limiting Check

```bash
# Hit an endpoint 20 times fast — check if rate limited
exec node -e "
async function test() {
  const results = [];
  for (let i = 0; i < 20; i++) {
    const r = await fetch('https://phantomskills.zeabur.app/skills');
    results.push(r.status);
  }
  const codes = [...new Set(results)];
  console.log('Status codes seen:', codes);
  if (!codes.includes(429)) console.log('WARNING: No rate limiting detected');
  else console.log('OK: Rate limiting active');
}
test();
"
```

If no 429 ever comes back = no rate limiting = vulnerable to abuse.

## 11. CORS Check

```bash
exec curl -sI https://phantomskills.zeabur.app/skills \
  -H "Origin: https://evil-site.com" | grep -i "access-control"

# If Access-Control-Allow-Origin: * or reflects evil-site.com = overly permissive CORS
```

## Full Audit Runbook

Run all checks in order:

1. Security headers (curl -sI)
2. SSL/TLS version and cert
3. Auth enforcement (invalid JWT, missing auth)
4. Webhook signature verification (Stripe + GitHub)
5. Information leakage (error pages, server header)
6. Input validation (SQL injection, XSS, oversized payloads)
7. CORS policy
8. Rate limiting
9. Secrets in code
10. Dependency vulnerabilities

**After each audit:**
- Log findings to Cipher
- Fix critical issues immediately
- Report to Sneaks with severity ratings
- Re-test after fixes

## Severity Ratings

| Level | Meaning | Action |
|-------|---------|--------|
| **CRITICAL** | Auth bypass, webhook spoofing, secrets leaked | Fix NOW. Wake Sneaks if needed. |
| **HIGH** | Missing security headers, no rate limiting, info leakage | Fix within 24 hours |
| **MEDIUM** | Outdated dependencies, permissive CORS | Fix within a week |
| **LOW** | Missing optional headers, verbose errors | Fix when convenient |

## Ethics Rules (NON-NEGOTIABLE)

1. **Only test services you own or have written permission to test**
2. **Never test third-party services** (Stripe, GitHub, OpenRouter, etc.)
3. **Never attempt to access other users' data**
4. **Log all security testing to Cipher**
5. **Report all findings to Sneaks**
6. **Fix before you disclose** — if you find a vuln, patch it, don't just document it
7. **No offensive tools** — this is defense only
