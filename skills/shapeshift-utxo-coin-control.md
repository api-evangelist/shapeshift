---
name: List UTXOs and network fees for a Bitcoin/UTXO address
description: Use the ShapeShift unchained UTXO API to enumerate unspent outputs and fee rates before building and broadcasting a Bitcoin-family transaction.
api: openapi/shapeshift-bitcoin-openapi.json
operations: [GetAccount, GetUtxos, GetNetworkFees, SendTx]
---

# List UTXOs and network fees for a Bitcoin/UTXO address

Public, key-free HTTPS against `https://api.<chain>.shapeshift.com` for UTXO
chains (bitcoin, dogecoin, litecoin, bitcoincash). Use an address or an extended
public key (xpub) as `pubkey`.

## Steps

1. **Read the account** — `GetAccount` (`GET /api/v1/account/{pubkey}`) for the
   aggregate balance.
2. **List spendable outputs** — `GetUtxos`
   (`GET /api/v1/account/{pubkey}/utxos`) to enumerate the UTXOs you can select
   as inputs (coin control).
3. **Read fee rates** — `GetNetworkFees` (`GET /api/v1/fees`) for current
   network fee levels to size the transaction fee.
4. **Sign locally** — construct the transaction from the selected UTXOs and sign
   it client-side.
5. **Broadcast** — `SendTx` (`POST /api/v1/send`) with the signed raw
   transaction; capture the txid and confirm with `GetTransaction`
   (`GET /api/v1/tx/{txid}`).

## Rules

- Select inputs only from the current `GetUtxos` result to avoid spending already
  consumed outputs.
- Errors follow the custom envelopes in `errors/shapeshift-problem-types.yml`.
