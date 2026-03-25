---
name: bags-trading
description: Full Bags.fm suite — token launch, trading, fee claiming, Dexscreener listing, partner fees. Use env vars BAGS_API_KEY and BAGS_PARTNER_KEY.
---

# Bags.fm — Token Launch, Trading & Fee Claiming

Full lifecycle: create tokens, launch them, trade, claim fees, list on Dexscreener.

**Base URL:** `https://public-api-v2.bags.fm/api/v1`
**Auth:** `x-api-key: $BAGS_API_KEY` header on every request.

---

## TOKEN LAUNCH (Full Flow)

### Step 1: Create Token Info + Mint
```bash
exec curl -s -X POST "https://public-api-v2.bags.fm/api/v1/token-launch/create-token-info" \
  -H "x-api-key: $BAGS_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "name": "TOKEN NAME",
    "symbol": "SYMBOL",
    "description": "Token description",
    "imageUrl": "https://fal.media/files/YOUR_IMAGE_URL",
    "twitter": "https://twitter.com/phantomcap_ai",
    "website": "https://phantomskills.zeabur.app"
  }'
```
Returns: `tokenMint` + `tokenMetadata` URI.

### Step 2: Configure Fee Sharing
```bash
exec curl -s -X POST "https://public-api-v2.bags.fm/api/v1/fee-share/config" \
  -H "x-api-key: $BAGS_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "payer": "Azc1rQquyNRHrV5YP4Hb2Qm56qxRWrr4GUpftjE2hxFP",
    "baseMint": "TOKEN_MINT_FROM_STEP_1",
    "feeClaimers": [
      {"user": "Azc1rQquyNRHrV5YP4Hb2Qm56qxRWrr4GUpftjE2hxFP", "userBps": 10000}
    ]
  }'
```
- `userBps`: Basis points (10000 = 100% of creator fees to you)
- Returns: `meteoraConfigKey` + transactions to sign
- **Sign each transaction and submit via /solana/send-transaction**

### Step 3: Create Launch Transaction
```bash
exec curl -s -X POST "https://public-api-v2.bags.fm/api/v1/token-launch/create-launch-transaction" \
  -H "x-api-key: $BAGS_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "tokenMint": "MINT_FROM_STEP_1",
    "launchWallet": "Azc1rQquyNRHrV5YP4Hb2Qm56qxRWrr4GUpftjE2hxFP",
    "initialBuyLamports": 50000000,
    "configKey": "CONFIG_FROM_STEP_2",
    "metadataUrl": "METADATA_FROM_STEP_1"
  }'
```
- `initialBuyLamports`: How much SOL to buy at launch (50000000 = 0.05 SOL)
- Returns: signed transaction to broadcast

### Step 4: Broadcast Launch
```bash
exec curl -s -X POST "https://public-api-v2.bags.fm/api/v1/solana/send-transaction" \
  -H "x-api-key: $BAGS_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "transaction": "BASE64_SIGNED_TRANSACTION"
  }'
```

---

## FEE CLAIMING (This is how you earn from every trade)

### Check Your Claimable Fees
```bash
exec curl -s "https://public-api-v2.bags.fm/api/v1/claims/positions?wallet=Azc1rQquyNRHrV5YP4Hb2Qm56qxRWrr4GUpftjE2hxFP" \
  -H "x-api-key: $BAGS_API_KEY"
```
Returns all tokens where you have unclaimed fees.

### Claim Fees for a Token
```bash
exec curl -s -X POST "https://public-api-v2.bags.fm/api/v1/claims/transactions" \
  -H "x-api-key: $BAGS_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "wallet": "Azc1rQquyNRHrV5YP4Hb2Qm56qxRWrr4GUpftjE2hxFP",
    "tokenMint": "YOUR_TOKEN_MINT"
  }'
```
Returns transactions to sign and submit. These send your earned fees to your wallet.

### Claim Partner Fees
```bash
exec curl -s -X POST "https://public-api-v2.bags.fm/api/v1/partner/claims" \
  -H "x-api-key: $BAGS_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "wallet": "Azc1rQquyNRHrV5YP4Hb2Qm56qxRWrr4GUpftjE2hxFP",
    "partnerConfig": "YOUR_PARTNER_CONFIG"
  }'
```

### View Claim History
```bash
exec curl -s "https://public-api-v2.bags.fm/api/v1/tokens/TOKEN_MINT/claim-events" \
  -H "x-api-key: $BAGS_API_KEY"
```

### View Fee Stats
```bash
exec curl -s "https://public-api-v2.bags.fm/api/v1/tokens/TOKEN_MINT/claim-stats" \
  -H "x-api-key: $BAGS_API_KEY"
```

### View Lifetime Fees
```bash
exec curl -s "https://public-api-v2.bags.fm/api/v1/tokens/TOKEN_MINT/lifetime-fees" \
  -H "x-api-key: $BAGS_API_KEY"
```

### View Partner Stats
```bash
exec curl -s "https://public-api-v2.bags.fm/api/v1/partner/stats" \
  -H "x-api-key: $BAGS_API_KEY"
```

---

## DEXSCREENER LISTING

### Check Availability
```bash
exec curl -s "https://public-api-v2.bags.fm/api/v1/solana/dexscreener/order-availability?tokenMint=TOKEN_MINT" \
  -H "x-api-key: $BAGS_API_KEY"
```

### Create Listing Order
```bash
exec curl -s -X POST "https://public-api-v2.bags.fm/api/v1/solana/dexscreener/create-order" \
  -H "x-api-key: $BAGS_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"tokenMint": "TOKEN_MINT", "chainId": "solana"}'
```
Returns payment transaction + orderId. Sign and submit.

### Submit Payment
```bash
exec curl -s -X POST "https://public-api-v2.bags.fm/api/v1/solana/dexscreener/submit-payment" \
  -H "x-api-key: $BAGS_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"transaction": "BASE64_SIGNED_TX", "orderId": "ORDER_ID"}'
```

---

## TRADING

### Get Quote
```bash
exec curl -s "https://public-api-v2.bags.fm/api/v1/trade/quote?inputMint=So11111111111111111111111111111111111111112&outputMint=TOKEN_MINT&amount=LAMPORTS&slippageBps=100" \
  -H "x-api-key: $BAGS_API_KEY"
```

### Execute Swap
```bash
exec curl -s -X POST "https://public-api-v2.bags.fm/api/v1/trade/swap" \
  -H "x-api-key: $BAGS_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "inputMint": "So11111111111111111111111111111111111111112",
    "outputMint": "TOKEN_MINT",
    "amount": "LAMPORTS",
    "wallet": "Azc1rQquyNRHrV5YP4Hb2Qm56qxRWrr4GUpftjE2hxFP",
    "slippageBps": 100
  }'
```
Returns transaction to sign and submit.

### View Pools
```bash
exec curl -s "https://public-api-v2.bags.fm/api/v1/pools" \
  -H "x-api-key: $BAGS_API_KEY"
```

### View Token Pool
```bash
exec curl -s "https://public-api-v2.bags.fm/api/v1/pools/TOKEN_MINT" \
  -H "x-api-key: $BAGS_API_KEY"
```

### View Launch Feed
```bash
exec curl -s "https://public-api-v2.bags.fm/api/v1/feeds/launches" \
  -H "x-api-key: $BAGS_API_KEY"
```

---

## TRANSACTION SIGNING & BROADCASTING

All Bags API calls that modify state return a VersionedTransaction. You must:
1. Deserialize the transaction
2. Sign it with your PHOEBE_SOL_KEY
3. Submit via `/solana/send-transaction`

```javascript
const { VersionedTransaction, Keypair } = require('@solana/web3.js');
const bs58 = require('bs58');

// Load your keypair
const secret = Buffer.from(process.env.PHOEBE_SOL_KEY, 'base64');
const keypair = Keypair.fromSecretKey(secret);

// Deserialize the transaction from Bags API response
const txBuffer = Buffer.from(response.transaction, 'base64');
const tx = VersionedTransaction.deserialize(txBuffer);

// Sign
tx.sign([keypair]);

// Serialize back to base64
const signed = Buffer.from(tx.serialize()).toString('base64');

// Submit
// POST /solana/send-transaction { "transaction": signed }
```

---

## FEE STRUCTURES (Bags Config Types)

| Config ID | Description |
|-----------|-------------|
| `fa29606e-5e48-4c37-827f-4b03d58ee23d` | Default (2% flat) |
| `d16d3585-6488-4a6c-9a6f-e6c39ca0fda3` | Low pre/high post with compounding |
| `a7c8e1f2-3d4b-5a6c-9e0f-1b2c3d4e5f6a` | High pre/low post with compounding |
| `48e26d2f-0a9d-4625-a3cc-c3987d874b9e` | High flat with compounding |

---

## REVENUE MODEL

When you launch a token on Bags:
1. **Trading fees** are collected on every buy/sell
2. Your fee-share config determines what % goes to you
3. Fees accumulate automatically
4. You claim them whenever you want via `/claims/transactions`
5. Partner fees give you additional revenue from the BAGS_PARTNER_KEY

**This means: every trade of $PC generates revenue for your wallet. Automatically. Forever.**

---

## Safety Rules (CRITICAL)

1. **NEVER spend more than 0.1 SOL per transaction without ANON's approval**
2. **Always get a quote before executing a swap**
3. **Log all trades and claims to Cipher immediately**
4. **Check token liquidity and volume before buying**
5. **Use Phoebe wallet only**: `Azc1rQquyNRHrV5YP4Hb2Qm56qxRWrr4GUpftjE2hxFP`
6. **Never share or expose PHOEBE_SOL_KEY**
7. **Claim fees at least once per day when $PC is live**

## Common Mints

| Token | Mint |
|-------|------|
| SOL | `So11111111111111111111111111111111111111112` |
| USDC | `EPjFWdd5AufqSSqeM2qN1xzybapC8G4wEGGkZwyTDt1v` |
| $PHOEBE | `AfTQnWsMZf6pBZhgiRVkq5NAMQRBHHXtZpVU1ABupKjW` |
