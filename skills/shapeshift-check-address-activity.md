---
name: Check a blockchain address balance and transaction history
description: Use the ShapeShift unchained API to look up an address's balances and paginated transaction history across EVM, UTXO, or Solana chains.
api: openapi/shapeshift-ethereum-openapi.json
operations: [GetInfo, GetAccount, GetTxHistory, GetTransaction]
---

# Check a blockchain address balance and transaction history

The ShapeShift unchained API is **public and key-free** — send plain HTTPS
requests to the per-chain host `https://api.<chain>.shapeshift.com`. No API key,
token, or OAuth is required. Pick the host for the chain you want (e.g.
`api.ethereum.shapeshift.com`, `api.bitcoin.shapeshift.com`,
`api.solana.shapeshift.com`).

## Steps

1. **(Optional) Confirm the chain** — `GetInfo` (`GET /api/v1/info`) returns
   network metadata so you can confirm you are on the intended chain/network.
2. **Fetch the account** — `GetAccount` (`GET /api/v1/account/{pubkey}`) where
   `pubkey` is the address or, on UTXO chains, an extended public key (xpub).
   Returns the native balance plus token balances (EVM/Solana).
3. **Page through history** — `GetTxHistory`
   (`GET /api/v1/account/{pubkey}/txs`). Use the `pageSize` query param (default
   10) and pass the opaque base64 `cursor` returned by the previous response to
   fetch the next page. Stop when no cursor is returned.
4. **Inspect a transaction** — `GetTransaction` (`GET /api/v1/tx/{txid}`) for the
   full detail of any txid surfaced in the history.

## Rules

- Cursor pagination only — never construct the cursor yourself; echo back the one
  the API returned (see `conventions/shapeshift-conventions.yml`).
- Handle errors from the small custom JSON envelopes: 400 `{error}`, 422
  `{message,details}`, 500 `{message}` (see `errors/shapeshift-problem-types.yml`).
- For realtime updates instead of polling history, subscribe over the WebSocket
  surface (see `asyncapi/shapeshift-unchained-webhooks.yml`).
