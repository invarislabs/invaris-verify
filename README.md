# invaris-verify

An autonomous AI agent that pays for the tools and data it uses, and sells its own inference the same way — with cryptographic proof that the output actually came from the model it claims to.

## Why this project

AI x crypto has shifted in 2026 from speculative token narratives toward real infrastructure. Two things are converging:

- **Agentic payments** — Coinbase's [x402](https://docs.cdp.coinbase.com/x402/) protocol lets an AI agent pay for an API call in the moment, using the HTTP 402 status code: a server quotes a price in a stablecoin, the agent checks it against its own budget, and a facilitator settles the payment on-chain.
- **Verifiable inference** — as agents start moving real money based on model outputs, "trust me" is no longer good enough. Running inference inside a trusted execution environment (or producing a ZKML proof) lets a caller verify that a specific model produced a specific output for a specific input.

This project builds a small agent economy around both ideas: a consumer agent that shops for and pays for tools within a budget, and a provider agent that sells verifiable inference per call.

## How it works

1. A **consumer agent** decides it needs something — a tool call, a dataset, or a model's inference — and finds a provider endpoint for it.
2. It sends a request. The provider replies `HTTP 402 Payment Required` with a price quoted in a stablecoin (USDC).
3. The consumer checks the price against its own budget logic. If it's within budget, it authorizes payment.
4. A **facilitator** verifies the payment is valid and settles it on-chain, then signals the provider to proceed.
5. The **provider agent** runs the request — for inference, inside a Trusted Execution Environment (TEE) or with a ZKML proof generator attached — and returns both the output and a cryptographic attestation of how it was produced.
6. The consumer (or anyone downstream) can independently verify that attestation: this output really did come from the claimed model, running unmodified, on the claimed input.
7. The transaction — payment plus attestation — is logged, so there's an auditable trail of who paid whom for what, and what was actually delivered.

## Architecture

```
Consumer agent (budgeted wallet)
        │
        ▼
x402 paywalled request (HTTP 402 + price quote)
        │
        ▼
Facilitator (verifies payment, settles USDC on-chain)
        │
        ▼
Provider agent (runs the model inside a TEE, signs an attestation)
        │
        ▼
Verified response (output + proof, logged on-chain) ──▶ back to consumer
```

## Key concepts

- **x402** — an open payment protocol built on the long-unused HTTP 402 status code. A server names its price in-band with the request itself, instead of requiring a pre-negotiated API key or subscription.
- **Facilitator** — a service that verifies a payment claim and settles it on-chain (e.g. USDC transfer on Base) so the provider doesn't have to run its own blockchain infrastructure.
- **TEE (Trusted Execution Environment)** — a hardware-isolated enclave (e.g. via Phala Cloud or Marlin) that runs the model and can cryptographically attest "this exact code, on this exact input, produced this exact output," without the hosting party being able to tamper with it.
- **ZKML (Zero-Knowledge Machine Learning)** — an alternative to TEEs: a mathematical proof (e.g. via EZKL) that a specific model produced a specific output, verifiable without trusting any hardware or hosting party at all.
- **Attestation** — the signed proof (from a TEE or a ZK circuit) that accompanies a model's output, letting the buyer verify authenticity after the fact.

## Practical use cases

- **Pay-per-call financial research.** A trading bot needs a market analysis before it risks real capital on a trade. Instead of trusting an unverifiable API response, it pays a few cents via x402 for one inference call and gets back an attestation proving the analysis came from the specific model it's paying for — not a cheaper model quietly swapped in to save the provider money.
- **Agent-to-agent subcontracting.** A general-purpose "project manager" agent breaks a task into specialized subtasks — translation, code review, image generation — and hires specialist agents on the open market for each one, paying per completed task instead of requiring a pre-negotiated contract or API key with every specialist it might ever need.
- **Autonomous, per-query data purchasing.** A research agent needs one query against a paywalled dataset (legal case law, satellite imagery, proprietary market data). Rather than committing to a monthly subscription it may barely use, it pays x402-style for exactly the queries it makes.
- **Compliance-sensitive AI outputs.** In legal, medical, or financial-advice contexts, a regulator or auditor may need to trace exactly which model version produced a given output. TEE or ZKML attestation gives that output an audit trail, which matters for liability once an AI-generated recommendation is challenged.
- **Anti "model downgrade" protection.** Enterprises paying for a premium AI API today have no way to verify the vendor is actually running the model they're being billed for, rather than a cheaper substitute swapped in silently to cut costs. Verifiable inference is a trust layer that can be sold on top of any inference vendor.
- **Micro-monetization for indie model builders.** A solo developer fine-tunes a niche model — say, a legal-clause classifier — and has no reason to build a full subscription/billing system for it. x402 lets them monetize per call from day one, with a facilitator handling settlement.
- **Verified sensor and IoT data marketplaces.** Drones, weather stations, or supply-chain sensors sell data streams to any paying agent; TEE attestation guarantees the data was captured and reported without being altered before sale.
- **Audit infrastructure for autonomous decision-making.** A company running agents that trade, procure, or handle customer service needs an immutable record of what model, running where, produced which decision — the payment log plus attestation trail doubles as that audit record for SOC2 or regulatory review.
- **Cross-organization agent collaboration without contracts.** Two companies' agents can transact ad hoc — e.g. one company's research agent buying inference time on another company's fine-tuned model — using x402 as the trust and settlement layer instead of negotiating a formal API agreement first.

## Market context

x402's adoption is still early: as of March 2026, reported daily transaction volume sits around $28,000 with average payments near $0.20, and independent analysis suggests a meaningful share of on-chain activity is wash trading rather than genuine commerce. That's not a reason to dismiss the idea — the underlying need (per-call payment, verifiable output) is real — but it does mean this project is a bet on helping define the killer use case rather than riding an already-proven wave.

## Roadmap

- [ ] Stand up wallets and a working x402 payment (testnet, Base Sepolia)
- [ ] Build the consumer agent (budget-aware, tool-selecting)
- [ ] Build the provider side — sell inference behind an x402-gated endpoint
- [ ] Add verifiability — TEE attestation (Phala / Marlin) or a ZKML proof (EZKL) on the provider side
- [ ] Log transactions and add a small dashboard for payment + verification flow
- [ ] Optional: on-chain Solidity contract (reputation registry or attestation-gated escrow)
- [ ] Polish: public testnet demo, technical writeup, short demo video

## Tech stack (planned)

- **Agents:** LangChain / LangGraph or CrewAI (or a hand-rolled tool-use loop)
- **Payments:** x402 protocol, Coinbase CDP AgentKit, USDC on Base Sepolia (testnet)
- **Verifiability:** Phala Cloud or Marlin (TEE attestation), optionally EZKL (ZKML)
- **Contracts (optional):** Solidity, Foundry
- **Frontend:** lightweight dashboard for transaction + verification visibility

## Status

Early scaffolding. README and roadmap only — implementation in progress.

## License

MIT

## Sources

- [x402 docs (Coinbase CDP)](https://docs.cdp.coinbase.com/x402/)
- [Coinbase-backed AI payments protocol wants to fix micropayment but demand is just not there yet (CoinDesk)](https://www.coindesk.com/markets/2026/03/11/coinbase-backed-ai-payments-protocol-wants-to-fix-micropayment-but-demand-is-just-not-there-yet)
- [Coinbase and Cloudflare Will Launch the x402 Foundation](https://www.coinbase.com/blog/coinbase-and-cloudflare-will-launch-x402-foundation)
- [Coinbase's x402 Facilitator Launches on Polygon](https://www.coinbase.com/developer-platform/discover/launches/x402facilitator-polygon)
- [Coinbase Expands x402 With AI Agent App Store](https://cryptonews.com/news/coinbase-x402-ai-agent-app-store-crypto-payments/)
- [Coinbase Developer Platform — Launches & Updates](https://www.coinbase.com/developer-platform/discover/launches)
- [Algorand Builders Berlin: Agentic Commerce x402 Hackathon](https://luma.com/agentic-commerce-hack)
- [Tether Launches Developer Grants Program to Fund Local-First AI and Payments Infrastructure](https://tether.io/news/tether-launches-developer-grants-program-to-fund-local-first-ai-and-payments-infrastructure/)
- [Phala — Confidential AI Cloud / Private Inference on GPU TEE](https://phala.com/)
