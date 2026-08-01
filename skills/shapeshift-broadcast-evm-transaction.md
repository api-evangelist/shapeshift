---
name: Estimate fees and broadcast a signed EVM transaction
description: Use the ShapeShift unchained EVM API to estimate gas, read fee data, and broadcast a client-signed transaction.
api: openapi/shapeshift-ethereum-openapi.json
operations: [GetGasFees, EstimateGas, SendTx, GetTransaction]
---

# Estimate fees and broadcast a signed EVM transaction

Public, key-free HTTPS against `https://api.<chain>.shapeshift.com` for any EVM
chain (ethereum, avalanche, bnbsmartchain, optimism, polygon, gnosis, arbitrum,
base). **The API never signs for you** — the transaction must already be signed
client-side; `SendTx` only broadcasts it.

## Steps

1. **Read current fees** — `GetGasFees` (`GET /api/v1/gas/fees`) for current
   gas/fee levels.
2. **Estimate gas** — `EstimateGas` (`POST /api/v1/gas/estimate`) with the
   transaction body to get the gas limit for the call.
3. **Sign locally** — build and sign the transaction with your own keys/wallet
   (e.g. via `@shapeshiftoss/hdwallet-core`). Do not send unsigned material.
4. **Broadcast** — `SendTx` (`POST /api/v1/send`) with the signed raw
   transaction. Capture the returned txid.
5. **Confirm** — `GetTransaction` (`GET /api/v1/tx/{txid}`) to track the
   transaction from pending to confirmed (or subscribe over WebSocket).

## Rules

- Idempotency is enforced at the chain layer by the transaction nonce/txid, not
  by an API idempotency key — do not blind-retry `SendTx`; re-check by txid first
  (see `conventions/shapeshift-conventions.yml`).
- A 400 `{error}` on send usually means a malformed or already-known raw
  transaction; a 422 `{message,details}` means body validation failed.
