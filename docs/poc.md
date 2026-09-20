# Proof of Concept

## Goal

Prove the core mechanic — pay per call, verify what you got — actually works, end to end, on testnet. This is deliberately the smallest possible slice of the full product. Everything not required to prove the mechanic is out of scope on purpose.

## What "done" looks like

A single, recorded run where:

1. A consumer script requests a resource from a provider endpoint.
2. The provider responds `402 Payment Required` with a real USDC price on Base Sepolia.
3. The consumer authorizes and sends payment.
4. A facilitator verifies and settles that payment on-chain.
5. The provider runs one inference call inside a TEE (Phala Cloud) and returns the output plus a signed attestation.
6. The consumer independently verifies the attestation and confirms it matches the claimed model and input.
7. The full transaction (payment + attestation) is written to a log.

If that sequence runs successfully and is independently reproducible, the POC is complete.

## In scope

- One consumer agent (a script is fine — it does not need to be "agentic" yet)
- One provider agent, one endpoint, one price
- Real x402 payment flow on Base Sepolia testnet, using Coinbase CDP AgentKit for wallets
- One TEE-attested inference call via Phala Cloud
- A flat-file (JSON) transaction log — no database, no UI required

## Explicitly out of scope

- Multiple providers or provider discovery
- Budget logic beyond a hardcoded ceiling
- A dashboard or any UI
- ZKML / EZKL (TEE only, for this phase)
- Mainnet, real funds, or a token
- Any reputation or trust-scoring system

## Technical plan

| Component | Choice | Notes |
|---|---|---|
| Chain | Base Sepolia (testnet) | matches x402's primary reference deployment |
| Payment | x402 protocol via Coinbase CDP AgentKit | quickstart exists; avoid building a facilitator from scratch |
| Wallets | CDP-managed wallets | one for consumer, one for provider |
| Verifiability | Phala Cloud TEE attestation | more turnkey than ZKML at this stage; EZKL deferred to a later phase |
| Provider endpoint | FastAPI | returns `402` with price quote, then the gated resource on payment |
| Logging | flat JSON file | sufficient to prove the trail exists; no need for infra yet |

## Success criteria

- The full 7-step sequence above completes without manual intervention beyond kicking it off
- The attestation is independently verifiable by a third party who did not run the call
- Per-call cost and latency are measured and recorded (even if not optimized)
- The run is recorded on video as the primary artifact for pitches and applications

## Timeline

1–2 weeks from kickoff, working part-time.

## Deliverable

A tagged commit in this repository, a short screen recording of the run, and updated numbers (real cost, real latency) fed back into this document and the README.
