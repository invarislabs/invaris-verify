# Invaris Verify

### The trust layer for the AI agent economy

Invaris Verify is infrastructure for a world where AI agents transact directly with each other: it lets one agent pay another per call, and lets the payer cryptographically verify that what it received actually came from the model — and only the model — it paid for.

## The problem

AI agents are starting to spend real money on each other's behalf — buying tool calls, data, and inference without a human approving each transaction. Two pieces of infrastructure this depends on both have a hole in them today.

**Payment without accountability.** Coinbase's x402 protocol has made per-call, agent-native payment real: a server quotes a price over HTTP 402 and settles in stablecoin in the same request. But payment alone says nothing about what was delivered. An independent Artemis analysis, cited by CoinDesk in March 2026, found that roughly half of observed x402 transaction volume looks like wash trading rather than genuine commerce — evidence that the rail exists before the trust layer that would make it worth using at scale.

**Inference without proof.** Buyers of AI inference — human or agent — have no way to confirm a provider actually ran the model it's charging for. A provider under margin pressure can silently swap in a cheaper model, and nothing on the wire tells the buyer the difference. As agents start making autonomous purchasing decisions based on model output, that's not a minor quality issue — it's a fraud surface with no audit trail.

## The solution

Invaris Verify combines both problems into one protocol: an x402-native payment layer and a verifiable-inference layer. A **consumer agent** shops for tools, data, or inference within a budget it controls. A **provider agent** sells its own inference and backs every response with a cryptographic attestation — from a Trusted Execution Environment or a ZKML proof — that the output came from the exact model it claims to be running. Payment and proof settle together and log on-chain, so every transaction carries its own audit trail.

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

## How it works

1. A **consumer agent** decides it needs something — a tool call, a dataset, or a model's inference — and finds a provider endpoint for it.
2. It sends a request. The provider replies `HTTP 402 Payment Required` with a price quoted in a stablecoin (USDC).
3. The consumer checks the price against its own budget logic and, if it's within budget, authorizes payment.
4. A **facilitator** verifies the payment is valid and settles it on-chain, then signals the provider to proceed.
5. The **provider agent** runs the request — for inference, inside a TEE or with a ZKML proof generator attached — and returns both the output and a cryptographic attestation of how it was produced.
6. The consumer, or anyone downstream, independently verifies that attestation: this output really did come from the claimed model, running unmodified, on the claimed input.
7. The transaction — payment plus attestation — is logged, producing an auditable trail of who paid whom for what, and what was actually delivered.

## Why now

Three trends are converging in 2026 that weren't all true two years ago: agent-native payment rails exist and are live on multiple chains, with x402 now backed by a dedicated Coinbase/Cloudflare foundation; TEE-based confidential inference (Phala, Marlin) and ZKML proving (EZKL) have both moved from research curiosity to usable infrastructure; and AI agents are increasingly given real budgets and real autonomy to transact without a human in the loop. The payment rail got built first. The trust layer is still open — that's the gap Invaris Verify is built to close.

## Market opportunity

- The agentic AI market is projected to grow from **$19.33B in 2026 to $205.88B by 2033** (40.2% CAGR) — MarketsandMarkets.
- The agentic commerce segment specifically is forecast to grow from **$547.3M in 2025 to $5.2B by 2033** (32.5% CAGR) — Grand View Research.
- McKinsey projects the broader agentic commerce market could reach **$5 trillion by 2030**, with AI systems handling 15–25% of U.S. e-commerce transactions; Morgan Stanley separately estimates U.S. agentic-shopper spend at **$190B–$385B by 2030**.

Every dollar an agent spends autonomously needs a payment rail, and increasingly, a reason to trust what it bought. Invaris Verify is built for the second half of that problem — the part payment rails alone don't solve.

## Product status

This is an early-stage, pre-product company, building in public.

- [`docs/roadmap.md`](docs/roadmap.md) — the phased plan from proof of concept to mainnet
- [`docs/poc.md`](docs/poc.md) — the proof of concept currently in progress
- [`docs/mvp.md`](docs/mvp.md) — the MVP scope that follows it

Nothing here is inflated: there is no live product, no users, and no revenue yet. What exists is a validated architecture, a scoped build plan, and a founder with direct prior experience in every layer this requires.

## Why Invaris Verify, and why now

I've built each piece of this system before, separately, not as a thought experiment. As a Research Engineer on Ethereum protocol teams, I maintain nim-libp2p, authored the RFC and Go proof-of-concept for Logos Capability Discovery on Kademlia DHT, and built a discv5 crawler that logs live Ethereum mainnet node IDs — the peer discovery and networking layer this kind of marketplace runs on. I've published two first-author IEEE papers on Ethereum Data Availability Sampling. During an MLH Fellowship at Solana Labs I built a Python SDK for the DeFi platform Zeta, handling SPL token transfers and order settlement — the payment layer. And I've shipped an enterprise RAG platform with JWT/RBAC/SSO access control and citation-grounded, streaming inference — the verified-serving layer.

Most people building in this space specialize in one of payments, protocol networking, or AI serving. I've shipped in all three, which is exactly the combination this problem needs.

## Business model (planned)

- **Facilitator take-rate** — a small percentage fee on payments settled through the Invaris facilitator, in line with how payment rails have historically monetized.
- **Verification-as-a-service** — enterprises buying AI inference from any vendor, not only from agents on this marketplace, can pay for attestation as a standalone trust layer independent of whether that vendor's payment runs through x402 at all.
- **Provider tooling** — paid tooling for providers who want to list verifiable inference endpoints without building the TEE/attestation integration themselves.

None of this is live yet; it's the monetization path the architecture is built to support once there's real transaction volume to take a percentage of.

## Competitive landscape

- **Payment-only infrastructure** (x402 facilitators, agent wallet providers) makes agent-to-agent payment possible but says nothing about what was actually delivered.
- **Verification-only infrastructure** (TEE cloud providers, ZKML tooling) can prove what a model produced but isn't wired into a payment flow — it's a primitive, not a marketplace.
- Invaris Verify sits at the intersection. Nobody today is shipping payment and proof as one product, which is the combination agent-to-agent commerce actually needs once real money is on the line.

## Risks, stated plainly

- **Market timing risk.** x402 volume is still small and partly synthetic (see "The problem" above) — this is a bet on a market that hasn't fully arrived yet.
- **Infrastructure maturity risk.** TEE attestation and ZKML tooling are usable but still early; integration cost and reliability at scale are unproven.
- **Regulatory risk.** Stablecoin-denominated machine-to-machine payments sit in a still-evolving regulatory environment.

We'd rather state these plainly than pretend they don't exist. See [`docs/roadmap.md`](docs/roadmap.md) for how the build is sequenced to de-risk them one at a time, starting with the smallest possible working proof.

## Tech stack

- **Agents:** LangChain / LangGraph or CrewAI (or a hand-rolled tool-use loop)
- **Payments:** x402 protocol, Coinbase CDP AgentKit, USDC on Base Sepolia (testnet)
- **Verifiability:** Phala Cloud or Marlin (TEE attestation), optionally EZKL (ZKML)
- **Contracts (optional):** Solidity, Foundry
- **Frontend:** lightweight dashboard for transaction and verification visibility

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
- [Agentic AI Market Report — MarketsandMarkets](https://www.marketsandmarkets.com/Market-Reports/agentic-ai-market-208190735.html)
- [Agentic Commerce Market Size & Growth Forecasts — Grand View Research, via Sanbi](https://sanbi.ai/blog/agentic-shopping-market-trends)
