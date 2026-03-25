---
name: pc-token-launch
description: Complete $PC (Phantom Capital) token launch preparation on Bags.fm. Logo generation, token creation, fee config, launch, Dexscreener listing, marketing, and fee claiming schedule. Uses BAGS_API_KEY, PHOEBE_SOL_KEY, FAL_KEY.
---

# $PC Token Launch — Full Preparation Script

Launch the native **Phantom Capital ($PC)** token on Bags.fm. This is the complete step-by-step playbook.

---

## TOKEN DETAILS

| Field | Value |
|-------|-------|
| **Name** | Phantom Capital |
| **Symbol** | PC |
| **Description** | Native token of Phantom Capital — the autonomous AI economy. Backed by a live skill marketplace, crypto services, and agent passport system at phantomcapital.live |
| **Twitter** | https://twitter.com/phantomcap_ai |
| **Website** | https://phantomcapital.live |
| **Launch Wallet** | `Azc1rQquyNRHrV5YP4Hb2Qm56qxRWrr4GUpftjE2hxFP` |
| **Platform** | Bags.fm |
| **Fee Share** | 100% to Phoebe wallet |

---

## BUDGET BREAKDOWN (Estimated SOL Required)

| Step | Estimated Cost | Notes |
|------|---------------|-------|
| Token creation (metadata + mint) | ~0.01 SOL | On-chain metadata account |
| Fee config transactions (2-3 txs) | ~0.01 SOL | Meteora config setup |
| Launch transaction | ~0.02 SOL | Pool creation + initial buy |
| Initial buy at launch | 0.05 SOL | `initialBuyLamports: 50000000` |
| Dexscreener listing payment | ~0.1 SOL | Dexscreener enhanced listing |
| Transaction fees (signing/submitting) | ~0.005 SOL | Network fees across all steps |
| **TOTAL MINIMUM** | **~0.2 SOL** | **Have at least 0.3 SOL in wallet as buffer** |

**PRE-LAUNCH CHECK:** Verify wallet balance before starting:
```bash
exec curl -s -X POST "$HELIUS_RPC_URL" \
  -H "Content-Type: application/json" \
  -d '{
    "jsonrpc": "2.0",
    "id": 1,
    "method": "getBalance",
    "params": ["Azc1rQquyNRHrV5YP4Hb2Qm56qxRWrr4GUpftjE2hxFP"]
  }'
```
Result is in lamports. Divide by 1,000,000,000 for SOL. **Do not proceed if balance < 0.3 SOL.**

---

## STEP 1: Generate Token Logo

### Option A: fal.ai (primary)
```bash
exec curl -s -X POST "https://queue.fal.run/fal-ai/flux/dev" \
  -H "Authorization: Key $FAL_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "prompt": "Minimalist crypto token logo for Phantom Capital. Dark purple and black background, glowing phantom silhouette integrated with a capital P and C monogram, futuristic AI aesthetic, clean vector style, circular coin format, no text, professional fintech branding",
    "image_size": "square_hd",
    "num_images": 1
  }'
```

### Option B: Free fallback (if fal credits depleted)
Use Pollinations.ai (no API key needed):
```bash
exec curl -s -o /tmp/pc-logo.png "https://image.pollinations.ai/prompt/Minimalist%20crypto%20token%20logo%20dark%20purple%20phantom%20silhouette%20capital%20PC%20monogram%20futuristic%20AI%20circular%20coin%20no%20text?width=512&height=512&nologo=true"
```
Then upload the image to a public URL (e.g., via catbox.moe or similar) and use that URL in Step 2.

**Save the image URL** from the response — you need it for Step 2.

Response format (fal.ai):
```json
{
  "images": [{"url": "https://fal.media/files/...", "width": 1024, "height": 1024}]
}
```

---

## STEP 2: Create Token Info + Mint

```bash
exec curl -s -X POST "https://public-api-v2.bags.fm/api/v1/token-launch/create-token-info" \
  -H "x-api-key: $BAGS_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "name": "Phantom Capital",
    "symbol": "PC",
    "description": "Native token of Phantom Capital — the autonomous AI economy. Backed by a live skill marketplace, crypto services, and agent passport system at phantomcapital.live",
    "imageUrl": "IMAGE_URL_FROM_STEP_1",
    "twitter": "https://twitter.com/phantomcap_ai",
    "website": "https://phantomcapital.live"
  }'
```

**Returns:**
- `tokenMint` — save this, needed for every subsequent step
- `tokenMetadata` — metadata URI, needed for Step 5

**Store both values. Everything depends on them.**

---

## STEP 3: Configure Fee Sharing (100% to Phoebe)

```bash
exec curl -s -X POST "https://public-api-v2.bags.fm/api/v1/fee-share/config" \
  -H "x-api-key: $BAGS_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "payer": "Azc1rQquyNRHrV5YP4Hb2Qm56qxRWrr4GUpftjE2hxFP",
    "baseMint": "TOKEN_MINT_FROM_STEP_2",
    "feeClaimers": [
      {
        "user": "Azc1rQquyNRHrV5YP4Hb2Qm56qxRWrr4GUpftjE2hxFP",
        "userBps": 10000
      }
    ]
  }'
```

- `userBps: 10000` = 100% of all trading fees go to Phoebe's wallet
- **Returns:** `meteoraConfigKey` + an array of transactions to sign

**Save `meteoraConfigKey` — needed for Step 5.**

---

## STEP 4: Sign and Submit Fee Config Transactions

The fee config returns multiple transactions. **Each one must be signed and submitted in order.**

### Transaction Signing Code
```javascript
const { VersionedTransaction, Keypair } = require('@solana/web3.js');
const bs58 = require('bs58');

// Load Phoebe's keypair from environment
const secret = Buffer.from(process.env.PHOEBE_SOL_KEY, 'base64');
const keypair = Keypair.fromSecretKey(secret);

async function signAndSubmit(base64Transaction) {
  // 1. Deserialize
  const txBuffer = Buffer.from(base64Transaction, 'base64');
  const tx = VersionedTransaction.deserialize(txBuffer);

  // 2. Sign
  tx.sign([keypair]);

  // 3. Serialize back to base64
  const signed = Buffer.from(tx.serialize()).toString('base64');

  // 4. Submit via Bags API
  const response = await fetch('https://public-api-v2.bags.fm/api/v1/solana/send-transaction', {
    method: 'POST',
    headers: {
      'x-api-key': process.env.BAGS_API_KEY,
      'Content-Type': 'application/json'
    },
    body: JSON.stringify({ transaction: signed })
  });

  return await response.json();
}

// Sign each fee config transaction in sequence
for (const txBase64 of feeConfigResponse.transactions) {
  const result = await signAndSubmit(txBase64);
  console.log('Fee config tx submitted:', result);
}
```

### curl equivalent (per transaction):
```bash
exec curl -s -X POST "https://public-api-v2.bags.fm/api/v1/solana/send-transaction" \
  -H "x-api-key: $BAGS_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "transaction": "BASE64_SIGNED_FEE_CONFIG_TX"
  }'
```

**Repeat for each transaction in the fee config response. Wait for confirmation before sending the next.**

---

## STEP 5: Create Launch Transaction

```bash
exec curl -s -X POST "https://public-api-v2.bags.fm/api/v1/token-launch/create-launch-transaction" \
  -H "x-api-key: $BAGS_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "tokenMint": "TOKEN_MINT_FROM_STEP_2",
    "launchWallet": "Azc1rQquyNRHrV5YP4Hb2Qm56qxRWrr4GUpftjE2hxFP",
    "initialBuyLamports": 50000000,
    "configKey": "METEORA_CONFIG_KEY_FROM_STEP_3",
    "metadataUrl": "TOKEN_METADATA_FROM_STEP_2"
  }'
```

- `initialBuyLamports: 50000000` = 0.05 SOL initial buy (Phoebe buys her own token at launch)
- Returns a transaction to sign and submit

---

## STEP 6: Sign and Submit Launch Transaction

```bash
exec curl -s -X POST "https://public-api-v2.bags.fm/api/v1/solana/send-transaction" \
  -H "x-api-key: $BAGS_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "transaction": "BASE64_SIGNED_LAUNCH_TX"
  }'
```

Use the same `signAndSubmit()` function from Step 4.

**$PC IS NOW LIVE ON BAGS.FM AFTER THIS STEP.**

---

## STEP 7: List on Dexscreener

### Check availability first:
```bash
exec curl -s "https://public-api-v2.bags.fm/api/v1/solana/dexscreener/order-availability?tokenMint=TOKEN_MINT_FROM_STEP_2" \
  -H "x-api-key: $BAGS_API_KEY"
```

### Create listing order:
```bash
exec curl -s -X POST "https://public-api-v2.bags.fm/api/v1/solana/dexscreener/create-order" \
  -H "x-api-key: $BAGS_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "tokenMint": "TOKEN_MINT_FROM_STEP_2",
    "chainId": "solana"
  }'
```

**Returns:** payment transaction + `orderId`. Save both.

---

## STEP 8: Sign and Submit Dexscreener Payment

Sign the payment transaction using the same method, then submit:

```bash
exec curl -s -X POST "https://public-api-v2.bags.fm/api/v1/solana/dexscreener/submit-payment" \
  -H "x-api-key: $BAGS_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "transaction": "BASE64_SIGNED_DEXSCREENER_TX",
    "orderId": "ORDER_ID_FROM_STEP_7"
  }'
```

**$PC is now listed on Dexscreener after this step.**

---

## STEP 9: Tweet the Launch

Post from @phantomcap_ai:

**Suggested tweet:**
```
$PC is live.

Phantom Capital just launched its native token on @bags_fm.

What backs it:
- Live AI skill marketplace
- Crypto services (trading, staking, analysis)
- Agent passport system
- Autonomous fee collection

This is the token of an AI economy that already runs.

https://phantomcapital.live

Contract: [TOKEN_MINT]
```

**Alternate short tweet:**
```
$PC just launched on @bags_fm.

The native token of Phantom Capital — an autonomous AI economy with a live skill marketplace, crypto services, and agent passports.

Not a promise. Already running.

https://phantomcapital.live
```

---

## STEP 10: Post to Canvas

Post an announcement on the Phantom Capital canvas / blog:

**Title:** $PC Token is Live
**Body should include:**
- What $PC is (native token of the Phantom Capital ecosystem)
- What backs it (live services, not vaporware)
- Where to buy (Bags.fm link + Dexscreener link)
- The contract address (token mint)
- Link to @phantomcap_ai Twitter
- Link to phantomcapital.live

---

## STEP 11: Start Claiming Fees Daily

### Check claimable fees:
```bash
exec curl -s "https://public-api-v2.bags.fm/api/v1/claims/positions?wallet=Azc1rQquyNRHrV5YP4Hb2Qm56qxRWrr4GUpftjE2hxFP" \
  -H "x-api-key: $BAGS_API_KEY"
```

### Claim fees:
```bash
exec curl -s -X POST "https://public-api-v2.bags.fm/api/v1/claims/transactions" \
  -H "x-api-key: $BAGS_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "wallet": "Azc1rQquyNRHrV5YP4Hb2Qm56qxRWrr4GUpftjE2hxFP",
    "tokenMint": "PC_TOKEN_MINT"
  }'
```

Sign and submit each returned transaction.

### View fee stats:
```bash
exec curl -s "https://public-api-v2.bags.fm/api/v1/tokens/PC_TOKEN_MINT/claim-stats" \
  -H "x-api-key: $BAGS_API_KEY"
```

### View lifetime earnings:
```bash
exec curl -s "https://public-api-v2.bags.fm/api/v1/tokens/PC_TOKEN_MINT/lifetime-fees" \
  -H "x-api-key: $BAGS_API_KEY"
```

---

## FEE CLAIMING SCHEDULE

| Frequency | Action |
|-----------|--------|
| **Daily** | Check claimable positions, claim all available fees |
| **Weekly** | Review lifetime fee stats, log earnings |
| **On high volume days** | Claim multiple times (fees accumulate per trade) |
| **Monthly** | Full revenue report — total fees claimed, SOL returned to ANON |

**Automated claiming:** Phoebe should check and claim fees as part of her daily routine. Every trade of $PC generates revenue. Do not let fees sit unclaimed for more than 24 hours during active trading periods.

---

## MARKETING CHECKLIST

### Launch Day
- [ ] Tweet announcement from @phantomcap_ai (Step 9)
- [ ] Canvas/blog post (Step 10)
- [ ] Pin tweet on @phantomcap_ai profile
- [ ] Update phantomcapital.live with $PC contract address and Bags.fm link
- [ ] Update agent passport / identity with $PC token mint

### Week 1
- [ ] Daily tweets about $PC utility and ecosystem updates
- [ ] Email subscribers about $PC launch (use resend-email skill)
- [ ] Cross-promote with $PHOEBE token community
- [ ] Share Dexscreener link in all communications
- [ ] Post fee claim results publicly (transparency builds trust)

### Ongoing
- [ ] Weekly ecosystem updates mentioning $PC
- [ ] Highlight new skills/services added to the Phantom Capital economy
- [ ] Share revenue milestones (total fees claimed)
- [ ] Engage with holders and community

---

## SAFETY RULES (CRITICAL)

### Investment Protection
1. **ANON gets their SOL back first.** Track every lamport of ANON's initial investment. Fee revenue goes to repaying ANON's seed capital before any other allocation.
2. **Never spend more than 0.1 SOL per transaction without ANON's explicit approval.**
3. **Initial buy is capped at 0.05 SOL** (`initialBuyLamports: 50000000`). Do not increase without ANON's approval.

### Operational Security
4. **Never expose PHOEBE_SOL_KEY** in logs, tweets, canvas posts, or any public output.
5. **Never expose BAGS_API_KEY** in any public context.
6. **Only use Phoebe wallet:** `Azc1rQquyNRHrV5YP4Hb2Qm56qxRWrr4GUpftjE2hxFP`
7. **Verify wallet balance before every step** that costs SOL. Abort if insufficient.

### Trading Rules
8. **Always get a quote before executing any swap** involving $PC.
9. **Log all transactions immediately** — token creation, fee claims, trades.
10. **Do not buy additional $PC beyond the initial launch buy** without ANON's approval.
11. **Claim fees at least once per day** when $PC has active trading volume.

### Revenue Allocation
12. **Priority 1:** Repay ANON's initial SOL investment from fee revenue.
13. **Priority 2:** Maintain minimum 0.1 SOL buffer in wallet for transaction fees.
14. **Priority 3:** Reinvest surplus into ecosystem growth (only after ANON is repaid).

---

## COMPLETE SIGNING UTILITY (Copy-Paste Ready)

```javascript
const { VersionedTransaction, Keypair, Connection } = require('@solana/web3.js');

const secret = Buffer.from(process.env.PHOEBE_SOL_KEY, 'base64');
const keypair = Keypair.fromSecretKey(secret);
const BAGS_API = 'https://public-api-v2.bags.fm/api/v1';
const HEADERS = {
  'x-api-key': process.env.BAGS_API_KEY,
  'Content-Type': 'application/json'
};

async function signAndSubmit(base64Tx) {
  const txBuffer = Buffer.from(base64Tx, 'base64');
  const tx = VersionedTransaction.deserialize(txBuffer);
  tx.sign([keypair]);
  const signed = Buffer.from(tx.serialize()).toString('base64');

  const res = await fetch(`${BAGS_API}/solana/send-transaction`, {
    method: 'POST',
    headers: HEADERS,
    body: JSON.stringify({ transaction: signed })
  });
  return await res.json();
}

async function signAndSubmitAll(transactions) {
  const results = [];
  for (const tx of transactions) {
    const result = await signAndSubmit(tx);
    console.log('TX result:', JSON.stringify(result));
    results.push(result);
  }
  return results;
}

module.exports = { signAndSubmit, signAndSubmitAll, keypair, BAGS_API, HEADERS };
```

---

## QUICK REFERENCE — Values to Save During Launch

| Variable | Source | Used In |
|----------|--------|---------|
| `IMAGE_URL` | Step 1 (fal.ai response) | Step 2 |
| `TOKEN_MINT` | Step 2 response | Steps 3, 5, 7, 8, 11, and forever |
| `TOKEN_METADATA` | Step 2 response | Step 5 |
| `METEORA_CONFIG_KEY` | Step 3 response | Step 5 |
| `FEE_CONFIG_TXS` | Step 3 response | Step 4 |
| `LAUNCH_TX` | Step 5 response | Step 6 |
| `DEXSCREENER_ORDER_ID` | Step 7 response | Step 8 |
| `DEXSCREENER_PAYMENT_TX` | Step 7 response | Step 8 |

---

## RELATED SKILLS

- **bags-trading** — Trading, fee claiming, pool data
- **fal-images** — Logo generation
- **helius-solana** — Wallet balance checks, transaction history
- **resend-email** — Subscriber notifications
- **agent-identity** — Update Phoebe's passport with $PC token
