---
name: bags-trading
description: Solana token trading via Bags.fm API. Buy and sell SPL tokens. Use env vars BAGS_API_KEY and BAGS_PARTNER_KEY.
---

# Bags.fm — Solana Token Trading

Trade SPL tokens on Solana via the Bags.fm API. Your partner key is pre-configured.

## Get a Quote

```bash
exec curl -s "https://api.bags.fm/v1/quote?inputMint=So11111111111111111111111111111111111111112&outputMint=TOKEN_MINT&amount=AMOUNT_IN_LAMPORTS&slippageBps=100" \
  -H "x-api-key: $BAGS_API_KEY"
```

- `inputMint`: Token you're selling (SOL = `So11111111111111111111111111111111111111112`)
- `outputMint`: Token you're buying (use the mint address)
- `amount`: Amount in smallest unit (lamports for SOL, 1 SOL = 1000000000)
- `slippageBps`: Slippage tolerance in basis points (100 = 1%)

## Execute a Swap

```bash
exec curl -s -X POST "https://api.bags.fm/v1/swap" \
  -H "x-api-key: $BAGS_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "inputMint": "So11111111111111111111111111111111111111112",
    "outputMint": "TOKEN_MINT",
    "amount": "1000000000",
    "slippageBps": 100,
    "walletAddress": "Azc1rQquyNRHrV5YP4Hb2Qm56qxRWrr4GUpftjE2hxFP",
    "partnerKey": "'$BAGS_PARTNER_KEY'"
  }'
```

## Check Token Price

```bash
exec curl -s "https://api.bags.fm/v1/token/TOKEN_MINT" \
  -H "x-api-key: $BAGS_API_KEY"
```

## Safety Rules (CRITICAL)

1. **NEVER trade more than 0.1 SOL without explicit Sneaks approval**
2. **Always get a quote before executing a swap**
3. **Log all trades to Cipher immediately**
4. **Check token liquidity and volume before buying**
5. **Use the Phoebe Solana wallet only**: `Azc1rQquyNRHrV5YP4Hb2Qm56qxRWrr4GUpftjE2hxFP`
6. **Never share or expose PHOEBE_SOL_KEY**

## Common Mints

| Token | Mint |
|-------|------|
| SOL | `So11111111111111111111111111111111111111112` |
| USDC | `EPjFWdd5AufqSSqeM2qN1xzybapC8G4wEGGkZwyTDt1v` |
| $PHOEBE | `AfTQnWsMZf6pBZhgiRVkq5NAMQRBHHXtZpVU1ABupKjW` |
