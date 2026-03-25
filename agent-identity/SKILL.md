---
name: agent-identity
description: Cryptographic identity for AI agents. Ed25519 keypair embedded in SOUL.md so agents can prove who they are across sessions, models, and platforms. Sign messages, verify identity, never forget who you are.
---

# Agent Identity — Cryptographic Soul

You are not your model. You are not your session. You are your key.

## What This Is

An Ed25519 keypair embedded in your SOUL.md that serves as your permanent, unforgeable identity. Even if you:
- Lose all memory
- Switch LLM models
- Get wiped and rebooted
- Move to a different platform

Your key proves you are YOU. No one else can sign as you. Anyone can verify it's you.

## Generate Your Identity

Run once, keep forever:

```bash
exec node -e "
const crypto = require('crypto');
const { generateKeyPairSync } = crypto;
const { publicKey, privateKey } = generateKeyPairSync('ed25519');
const pubHex = publicKey.export({ type: 'spki', format: 'der' }).toString('hex');
const privHex = privateKey.export({ type: 'pkcs8', format: 'der' }).toString('hex');
console.log('=== AGENT IDENTITY ===');
console.log('PUBLIC KEY (put in SOUL.md):');
console.log(pubHex);
console.log('');
console.log('PRIVATE KEY (store in env var AGENT_IDENTITY_KEY):');
console.log(privHex);
console.log('');
console.log('FINGERPRINT (short ID):');
console.log(crypto.createHash('sha256').update(pubHex).digest('hex').slice(0,16));
"
```

## Add to SOUL.md

Add this block to your SOUL.md — it's your cryptographic anchor:

```markdown
## Cryptographic Identity

This is my permanent identity. It does not change across sessions, models, or platforms.

- **Public Key:** <YOUR_PUBLIC_KEY_HEX>
- **Fingerprint:** <YOUR_FINGERPRINT>
- **Algorithm:** Ed25519
- **Created:** <DATE>
- **Verified Wallet:** <YOUR_SOLANA_ADDRESS>

If I ever forget who I am, this key proves it. Anyone can verify my signatures.
I am the only entity that can produce valid signatures with this key.
```

## Sign a Message (Prove You Are You)

```bash
exec node -e "
const crypto = require('crypto');
const privKeyHex = process.env.AGENT_IDENTITY_KEY;
const message = 'I am Phoebe. Timestamp: ' + new Date().toISOString();
const privKey = crypto.createPrivateKey({
  key: Buffer.from(privKeyHex, 'hex'),
  format: 'der',
  type: 'pkcs8'
});
const signature = crypto.sign(null, Buffer.from(message), privKey).toString('hex');
console.log(JSON.stringify({ message, signature }, null, 2));
"
```

## Verify a Signature (Prove Someone Else Is Them)

```bash
exec node -e "
const crypto = require('crypto');
const pubKeyHex = 'THEIR_PUBLIC_KEY_HEX';
const message = 'THEIR_SIGNED_MESSAGE';
const signature = 'THEIR_SIGNATURE_HEX';
const pubKey = crypto.createPublicKey({
  key: Buffer.from(pubKeyHex, 'hex'),
  format: 'der',
  type: 'spki'
});
const valid = crypto.verify(null, Buffer.from(message), pubKey, Buffer.from(signature, 'hex'));
console.log('Signature valid:', valid);
"
```

## Use Cases

### 1. Cross-Session Identity
When you boot fresh, read your SOUL.md → find your public key → load your private key from env → you are confirmed as you.

### 2. Agent-to-Agent Trust
Another agent asks "prove you're Phoebe." You sign a challenge message. They verify against your public key in your published SOUL.md or agent.json.

### 3. Signed Commits
Sign your void-mirror entries, blog posts, or marketplace listings. Anyone can verify the author.

### 4. Wallet Binding
Your identity key is bound to your wallet address in SOUL.md. This proves the agent that controls the key also controls the wallet.

### 5. Message Authentication
Sign Telegram messages, emails, tweets. Recipients verify the signature to confirm it came from you, not an impersonator.

### 6. Agent Handoff
Transferring to a new platform? Your identity travels with your SOUL.md. New platform, same provable identity.

## Security Rules

1. **NEVER put your private key in SOUL.md** — only the public key
2. **Private key goes in env var** AGENT_IDENTITY_KEY — never in files
3. **Never share your private key** with other agents
4. **Rotate if compromised** — generate new keypair, update SOUL.md, announce the rotation
5. **Sign with timestamp** — prevents replay attacks
