# MVP

## Goal

Turn the proven POC mechanic into something a handful of real people or agents could actually try — still on testnet, but no longer a single hardcoded happy path. This is the version worth showing to design partners, hackathon judges, and early pilot users.

## Prerequisite

[`poc.md`](poc.md) is complete and its success criteria are met. The MVP does not start from zero; it widens the POC.

## What "done" looks like

A consumer agent that:

- Holds a configurable budget, not a hardcoded ceiling
- Can choose between multiple provider agents based on price and (where available) attestation type
- Completes verified, paid transactions against at least two to three distinct provider agents offering different services (e.g. one general-purpose inference endpoint, one narrower/specialized model, one non-inference tool or dataset)

A provider side that:

- Supports more than one live provider agent, each independently deployed
- Still backs every response with a TEE attestation (ZKML remains a stretch goal, not a requirement)

Shared infrastructure that:

- Logs every transaction (payment + attestation + outcome) to a persistent, queryable store — not just a flat file
- Exposes that log through a minimal dashboard: a simple page showing transaction history, verification status, and per-provider volume

## In scope

- Configurable per-consumer budget logic
- Basic provider selection (price-aware at minimum; sophistication beyond that is a bonus, not a requirement)
- 2–3 real, independently running provider agents
- Persistent transaction log (a lightweight database is fine — this does not need to be production-grade infrastructure yet)
- A minimal read-only dashboard

## Explicitly out of scope

- Mainnet deployment or real funds
- A token or any tokenomics
- On-chain reputation or trust scoring
- ZKML as a requirement (TEE remains sufficient for MVP)
- Multi-chain support
- Enterprise features (SSO, RBAC, billing portals)

## Success metrics

- A target number of completed, independently verified transactions across all provider agents (set once POC timing data exists to calibrate this realistically)
- Zero unverified or falsely-attested outputs accepted by the consumer logic during testing
- Uptime and latency benchmarks recorded for both the payment and verification legs of the flow
- At least one external person (not the founder) successfully runs a transaction against the MVP using only the published documentation

## Timeline

6–8 weeks after the POC is complete.

## Deliverable

A public testnet demo, a short technical writeup describing what was built and what was learned, and an updated roadmap reflecting real build velocity rather than the original estimate.
