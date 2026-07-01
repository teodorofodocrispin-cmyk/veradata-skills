# VeraData Skills

Agent skills for [VeraData](https://api.veradata.dev) — verified Latin American compliance data for autonomous AI agents, pay-per-call via x402.

## Install

```bash
npx skills add teodorofodocrispin-cmyk/veradata-skills --skill veradata-latam-compliance
```

Works in Claude Code, Codex, Cursor, Droid, OpenCode, and any agent runtime that supports the SKILL.md format.

## What your agent gets

- **Sanctions screening** against OFAC SDN + UN + EU + UK consolidated lists (59,000+ entries) — $0.01-$0.05/call
- **KYB entity verification** via official LATAM registries: RUES (Colombia), CNPJ (Brazil), RFC (Mexico) — $0.08/call
- **Central bank rates**: TRM, TIIE, Selic, UF — $0.02/call
- **EU AI Act Art.12 audit trail** on every screening — hash chain independently verified ([conformance fixture](https://github.com/babyblueviper1/preaction-governance-conformance/tree/main/examples/veradata))

## No API key. No account. No human in the loop.

Payment is authentication: your agent hits the endpoint, gets a 402, signs a USDC micropayment on Base or Solana, and gets the data.

**Free trial:** 5 calls per endpoint per day with `X-TRIAL: true` header.

## Verified in public

The AAT hash chain was audited byte-by-byte by an independent verifier and registered as an ERC-8299 conformance fixture. Two `decision_ref` proofs published. Full exchange: [x402-foundation/x402#2749](https://github.com/x402-foundation/x402/issues/2749)

## Links

- API docs: https://api.veradata.dev/llms.txt
- x402 discovery: https://api.veradata.dev/.well-known/x402
- OpenAPI: https://api.veradata.dev/docs
