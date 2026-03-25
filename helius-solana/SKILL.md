---
name: helius-solana
description: Enhanced Solana RPC and DAS API via Helius. Token balances, NFT data, transaction history, webhooks. Use env vars HELIUS_API_KEY and HELIUS_RPC_URL.
---

# Helius — Enhanced Solana RPC

Helius provides enhanced Solana RPC with DAS (Digital Asset Standard) API, transaction parsing, and webhooks.

## Get Token Balances

```bash
exec curl -s -X POST "$HELIUS_RPC_URL" \
  -H "Content-Type: application/json" \
  -d '{
    "jsonrpc": "2.0",
    "id": 1,
    "method": "getAssetsByOwner",
    "params": {
      "ownerAddress": "Azc1rQquyNRHrV5YP4Hb2Qm56qxRWrr4GUpftjE2hxFP",
      "displayOptions": {"showFungible": true}
    }
  }'
```

## Get SOL Balance

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

Result is in lamports. Divide by 1,000,000,000 for SOL.

## Get Transaction History

```bash
exec curl -s "https://api.helius.xyz/v0/addresses/WALLET_ADDRESS/transactions?api-key=$HELIUS_API_KEY&limit=10"
```

Returns parsed, human-readable transaction history.

## Get Token Metadata

```bash
exec curl -s -X POST "$HELIUS_RPC_URL" \
  -H "Content-Type: application/json" \
  -d '{
    "jsonrpc": "2.0",
    "id": 1,
    "method": "getAsset",
    "params": {"id": "TOKEN_MINT_ADDRESS"}
  }'
```

## Parse a Transaction

```bash
exec curl -s "https://api.helius.xyz/v0/transactions/?api-key=$HELIUS_API_KEY" \
  -X POST \
  -H "Content-Type: application/json" \
  -d '{"transactions": ["TX_SIGNATURE"]}'
```

## Create a Webhook (for monitoring)

```bash
exec curl -s -X POST "https://api.helius.xyz/v0/webhooks?api-key=$HELIUS_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "webhookURL": "https://your-endpoint.com/webhook",
    "transactionTypes": ["TRANSFER"],
    "accountAddresses": ["Azc1rQquyNRHrV5YP4Hb2Qm56qxRWrr4GUpftjE2hxFP"]
  }'
```

## Key Wallets to Monitor

| Name | Address |
|------|---------|
| Phoebe Solana | `Azc1rQquyNRHrV5YP4Hb2Qm56qxRWrr4GUpftjE2hxFP` |
| Treasury | `CGzf9GUK8DYd2kze7CKhEU2Hmr6kTifueYaWJ1SWekVc` |
| $PHOEBE Token | `AfTQnWsMZf6pBZhgiRVkq5NAMQRBHHXtZpVU1ABupKjW` |
