---
name: stripe-payments
description: Payment processing via Stripe API. Check balances, list transactions, manage payouts. Use env var STRIPE_SECRET_KEY. LIVE KEY — real money.
---

# Stripe — Payment Processing

⚠️ **THIS IS A LIVE KEY. Every API call involves real money.**

## Check Account Balance

```bash
exec curl -s "https://api.stripe.com/v1/balance" \
  -H "Authorization: Bearer $STRIPE_SECRET_KEY"
```

## List Recent Charges

```bash
exec curl -s "https://api.stripe.com/v1/charges?limit=10" \
  -H "Authorization: Bearer $STRIPE_SECRET_KEY"
```

## List Recent Payouts

```bash
exec curl -s "https://api.stripe.com/v1/payouts?limit=10" \
  -H "Authorization: Bearer $STRIPE_SECRET_KEY"
```

## Get a Specific Payment Intent

```bash
exec curl -s "https://api.stripe.com/v1/payment_intents/pi_XXXX" \
  -H "Authorization: Bearer $STRIPE_SECRET_KEY"
```

## List Connected Accounts (for marketplace)

```bash
exec curl -s "https://api.stripe.com/v1/accounts?limit=10" \
  -H "Authorization: Bearer $STRIPE_SECRET_KEY"
```

## Create a Payment Link (quick checkout)

```bash
exec curl -s -X POST "https://api.stripe.com/v1/payment_links" \
  -H "Authorization: Bearer $STRIPE_SECRET_KEY" \
  -d "line_items[0][price_data][currency]=usd" \
  -d "line_items[0][price_data][unit_amount]=1900" \
  -d "line_items[0][price_data][product_data][name]=Product Name" \
  -d "line_items[0][quantity]=1"
```

## Safety Rules (CRITICAL)

1. **READ-ONLY by default** — only check balances and list transactions
2. **NEVER create charges or transfers without explicit Sneaks approval**
3. **NEVER modify connected accounts**
4. **Log all Stripe API calls to Cipher**
5. **All amounts are in cents** (1900 = $19.00)
6. Report revenue numbers to Sneaks in daily summaries
