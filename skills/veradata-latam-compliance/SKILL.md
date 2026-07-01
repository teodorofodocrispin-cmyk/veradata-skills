---
name: veradata-latam-compliance
description: Use when an agent needs sanctions screening, KYB entity verification, or LATAM financial data with an auditable trail. Screens against OFAC SDN + UN + EU + UK consolidated lists (59,000+ entries), looks up companies in Colombian (RUES), Brazilian (CNPJ), and Mexican (RFC) registries, and fetches central bank rates. Pay-per-call via x402 USDC on Base or Solana — no API key, no account. Every screening returns an EU AI Act Art.12 hash-chain audit trail, independently verified (ERC-8299 conformance fixture public). Prices: $0.01-$0.25 per call. Trial: 5 free calls per endpoint per day with X-TRIAL: true header.
---

# VeraData — LATAM Compliance Data for Autonomous Agents

Base URL: `https://api.veradata.dev`
Protocol: x402 v2 (`PAYMENT-SIGNATURE` header) — v1 `X-PAYMENT` also accepted
Networks: Base (`eip155:8453`) and Solana mainnet, USDC
Discovery: `https://api.veradata.dev/.well-known/x402`
Docs: `https://api.veradata.dev/llms.txt`

## When to use this skill

- Screening a person or company against global sanctions lists before a transaction, onboarding, or payout
- Verifying a Latin American company exists (KYB) via official registries
- Getting LATAM central bank rates (TRM, TIIE, Selic, UF, dólar blue)
- Any compliance decision that needs a recomputable, hash-chained audit trail

## Endpoints

| Endpoint | Method | Price | What it does |
|----------|--------|-------|--------------|
| `/sanctions/quick` | POST | $0.01 | OFAC-only screening. Cheapest first call to test the integration. |
| `/sanctions` | POST | $0.05 | Full screening: OFAC + UN + EU + UK (59k+ entries). AAT hash chain. |
| `/sanctions/zkp` | POST | $0.05 | Privacy-preserving: send a commitment hash, never the name. |
| `/entity/kyb` | POST | $0.08 | Company lookup: RUES (CO), CNPJ (BR), RFC (MX). |
| `/rates` | GET | $0.02 | Central bank rates: BanRep, Banxico, BCB, BCRP. |
| `/compliance/report` | POST | $0.25 | Full regulator-ready compliance report. |
| `/wallet/screen` | POST | $0.05 | Screen a blockchain wallet address. ALLOW/REVIEW/BLOCK. |

## Quick start (with agentcash or any x402 client)

```bash
# Free trial call first (5/day per endpoint)
curl -X POST https://api.veradata.dev/sanctions/quick \
  -H "Content-Type: application/json" \
  -H "X-TRIAL: true" \
  -d '{"name": "ACME Corp", "country": "CO", "type": "company"}'
```

With agentcash (handles payment automatically):
```
Ask your agent: "Screen ACME Corp (Colombia) against sanctions lists using api.veradata.dev, paying via x402"
```

## Payment flow (x402 v2)

1. POST to any endpoint without payment → HTTP 402 with payment terms
2. Sign EIP-3009 TransferWithAuthorization with your USDC wallet
3. Retry with `PAYMENT-SIGNATURE: <base64-signature>` header
4. Receive 200 OK with data + audit trail

## The audit trail (what makes this different)

Every screening response includes an `aat` block:
```json
{
  "aat": {
    "query_hash": "sha256:...",
    "event_hash": "sha256:...",
    "prev_hash": "sha256:...",
    "chain_hash": "sha256:...",
    "policy_ref": "veradata-v2.0.0-EU-AI-ACT-ART12-...",
    "chain_stored": true
  }
}
```

The chain is independently recomputable — reference implementation:
https://github.com/babyblueviper1/preaction-governance-conformance/tree/main/examples/veradata

Verification: `SHA256(query_hash|risk_score:.4f|timestamp|policy_ref) == event_hash`
Note: hash values carry their own `sha256:` prefix as input to the next hash.

## Response semantics (important for agents)

- `risk_category: "CLEAN"` means "not found in these lists at this moment" — never "entity is globally clean"
- `lists_checked` always tells you exactly which lists were screened
- `matches: []` explicit on CLEAN results — "checked and found nothing" is different from "field not populated"

## Budget control

```
POST /budget {"agent_id": "my-agent", "cap_usdc": 10.0, "period": "daily"}
GET /budget/{agent_id}
```

## Reputation tiers

NEW → ACTIVE (10 calls) → VERIFIED (50 calls, 10% off) → TRUSTED (200 calls, 20% off)
Check: `GET /reputation/{your_wallet_address}`
