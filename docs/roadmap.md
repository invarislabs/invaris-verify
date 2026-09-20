# Roadmap

This is a working build plan, not a marketing timeline — it's sequenced to prove the riskiest assumptions first with the smallest possible amount of code, then widen scope once each piece is proven. Timelines are estimates from a solo founder working part-time against this; they will move, and this document will be updated as they do.

## Phase 0 — Foundation (complete)

**Objective:** validate the architecture and scope the build before writing product code.

- Repository, architecture diagram, and technical concept defined
- Market and competitive research completed (see README)
- Roadmap, POC, and MVP specs written (this document and its siblings)

**Status:** done, September 2026.

## Phase 1 — Proof of Concept

**Objective:** prove the core mechanic works end to end, on testnet, with the smallest possible surface area.

**Scope:** one consumer agent, one provider agent, one x402-gated request, one TEE-attested inference call, on Base Sepolia.

Full spec: [`poc.md`](poc.md).

**Target:** 1–2 weeks from kickoff.

## Phase 2 — MVP

**Objective:** turn the proven mechanic into something a handful of real users could actually try — multiple providers, real budget logic, basic observability.

**Scope:** a consumer agent with configurable budget and provider-selection logic; two to three real provider agents; a minimal transaction log and dashboard. Still testnet.

Full spec: [`mvp.md`](mvp.md).

**Target:** 6–8 weeks after Phase 1 completes.

## Phase 3 — Public testnet beta

**Objective:** open the marketplace to external provider agents instead of only ones we built ourselves, and start generating real (if small) third-party transaction volume.

- Publish a provider SDK and developer documentation, so anyone can list a verifiable inference endpoint
- Recorded demo video and technical writeup published publicly
- Recruit 3–5 external provider agents or teams to test-list on the marketplace
- Track first real usage metrics: completed transactions, unique providers, unique consumers, verification failure rate

**Target:** 4–6 weeks after Phase 2.

## Phase 4 — Mainnet and formal fundraising

**Objective:** move from testnet demonstration to a product that can carry real economic value, backed by actual usage data rather than a plan.

- Move payment settlement to Base mainnet with real USDC
- Formalize the facilitator take-rate business model against real transaction volume
- Raise a pre-seed round backed by working mainnet volume rather than a pitch deck alone
- Begin conversations with the first verification-as-a-service enterprise pilot customers

**Target:** dependent on Phase 3 traction; not time-boxed yet.

## Phase 5 — Scale

**Objective:** grow from a working product into defensible infrastructure.

- On-chain reputation registry for provider agents (Solidity)
- Multi-chain support beyond Base
- Verification-as-a-service offered independent of the marketplace, for enterprises buying inference from any vendor
- ZKML as a first-class alternative to TEE attestation, not just a stretch goal

**Target:** post-fundraise.
