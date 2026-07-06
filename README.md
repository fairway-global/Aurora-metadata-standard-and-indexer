# Sundown — Metadata Standard & Indexer for Cardano Credit Markets

> **An open-source identity and compliance layer for P2P credit markets on Cardano.**
>
> Attach zero-knowledge identity proofs, verification status, and compliance metadata to credit market UTxOs — then discover, filter, and monitor lending opportunities through an open indexer API.

[![License](https://img.shields.io/badge/license-Apache%202.0-blue.svg)](LICENSE)

---

## Sundown & Aurora

This project has two layers with different scopes and licensing:

| | Sundown | Aurora |
| :--- | :--- | :--- |
| **What** | On-chain infrastructure: metadata standard, off-chain indexer, ZK proof generation, connection to BTC/stablecoin liquidity | Off-chain business services: SACCO onboarding, local fiat payments, legal & operational frameworks, last-mile lending operations |
| **License** | Open source (Apache 2.0) | Closed source (Fairway proprietary) |
| **Where** | **This repository** | Separate, private |

**Sundown** is the open Cardano protocol layer — the metadata standard, indexer, and verification infrastructure that any credit market participant can use, extend, or self-host.

**Aurora** is Fairway's commercial service built on top of Sundown, handling the operational realities of connecting real-world lending institutions (SACCOs, MFIs, banks) to Cardano credit markets: onboarding, compliance, fiat settlement, and loan servicing coordination.

Through Sundown, institutions connect to Cardano's open credit markets. Through Aurora, Fairway handles the last mile.

---

## What this repository contains

This is the home for two open-source components described in the **Sundown** Cardano Treasury proposal:

| Component | Purpose |
| :--- | :--- |
| **Metadata standard** | A versioned schema for attaching identity proofs, verification status, and loan-level compliance information to credit market UTxOs as transaction metadata. Designed to be extensible and support additional trust and verification use cases over time. |
| **Off-chain indexer** | A service that reads credit market UTxOs on Cardano, verifies attached proofs, tracks loan lifecycles (origination → funding → repayment → closure), and exposes a query API for discovery and filtering. Can be run locally to avoid dependence on any third-party API. |

Both components are designed to work with Cardano's eUTxO model and align with [CIP-89 (Beacon Tokens)](https://github.com/cardano-foundation/CIPs/tree/master/CIP-0089) for distributed dApp discovery without centralized infrastructure.

The infrastructure is designed to integrate with [Pogun's credit market](https://pogun.io) and other compatible Cardano lending implementations, while remaining independent of any single protocol or production deployment.

## Why metadata instead of smart-contract logic?

The standard uses transaction metadata rather than embedding identity checks inside lending validators. This is a deliberate architectural choice:

- **Efficiency.** No additional on-chain computation or script execution costs. Identity metadata is attached alongside existing lending transactions.
- **Flexibility.** The standard is extensible — new proof types (ZK proofs via Midnight, additional credential frameworks) can be added without modifying lending contracts.
- **Permissionlessness.** Lending contracts remain open. The metadata layer is an *opt-in* extension that enables compliant participation without restricting the underlying protocol.

## How it works

```
1. Origination       A verified lending institution publishes a Loan Request UTxO
                     through a compatible Cardano credit market implementation,
                     with verification metadata attached (ZK credential proof,
                     verification status, compliance references).

2. Indexing          The off-chain indexer monitors the chain for credit market
                     UTxOs via beacon tokens (CIP-89), verifies attached proofs,
                     and makes the opportunity queryable.

3. Discovery         Capital providers query the indexer to find lending
                     opportunities, filtering by jurisdiction, verification
                     status, or other metadata attributes.

4. Funding           Eligible capital is deployed into verified lending
                     opportunities through the credit market infrastructure.

5. Repayment         As loans are repaid, repayment history is trustlessly
                     associated with the originating entity and its verification
                     credentials — building toward privacy-preserving reputation
                     systems and on-chain credit histories over time.

6. Settlement        Regulated settlement infrastructure (initially Encryptus)
                     supports conversion between Cardano-native stable assets
                     and local fiat currencies for last-mile disbursement.
```

## Repository structure (planned)

```
sundown-metadata-standard-and-indexer/
├── standard/
│   ├── spec/                  # Metadata schema specification (versioned)
│   │   ├── v1.0/
│   │   │   ├── schema.cddl    # CDDL definition for on-chain metadata
│   │   │   └── spec.md        # Human-readable specification document
│   │   └── examples/          # Example metadata payloads
│   └── aiken/                 # Reference helpers for metadata construction
│
├── indexer/
│   ├── src/                   # Indexer service source
│   │   ├── chain/             # Chain sync (CIP-89 beacon detection)
│   │   ├── verify/            # ZK proof verification logic
│   │   ├── lifecycle/         # Loan state tracking
│   │   └── api/               # Query & discovery API (REST)
│   ├── Dockerfile             # Self-hostable — no third-party dependency
│   └── README.md              # Indexer setup and operation guide
│
├── docs/
│   ├── integration-guide.md   # For originators and capital providers
│   ├── architecture.md        # System design and data flow
│   └── trust-framework.md     # Verification principles and ZK approach
│
├── pilot/
│   └── dashboard/             # Lightweight dRep monitoring dashboard
│
├── LICENSE                    # Apache 2.0
└── README.md                  # This file
```

## Technical foundations

- **Cardano eUTxO model** — each loan is a discrete, auditable on-chain object.
- **[CIP-89](https://github.com/cardano-foundation/CIPs/tree/master/CIP-0089) Beacon Tokens** — distributed discovery of credit market UTxOs without centralized registries. Designed by Fallen Icarus.
- **CIP-20 Transaction Metadata** — standard label for human-readable transaction messages.
- **Zero-knowledge proofs** — ZK proofs derived from verifiable credentials, enabling verification without exposing underlying identity data.
- **USDM** — regulated Cardano-native stablecoin for settlement.
- **Midnight** (future integration) — ZK proof infrastructure for enhanced on-chain credential verification.

## Verification and trust framework

The metadata layer follows four design principles:

1. **Technology agnostic.** The framework supports multiple credential and proof systems — not locked to any single identity provider.
2. **Proof-based.** Verification relies on cryptographic proofs, not trusted assertions. Third parties can independently verify without accessing underlying data.
3. **Privacy-preserving.** No personal or borrower data is stored on-chain. Only proofs, commitments, and verification status.
4. **Open participation.** Lending contracts remain fully permissionless. The metadata layer is opt-in — it enables compliant participation without gatekeeping the protocol.

## Pilot context

The infrastructure is validated through a Treasury-backed pilot with Ethiopian Savings and Credit Cooperative Organizations (SACCOs). A dedicated ADA allocation reserved for pilot lending liquidity is converted into USDM and deployed through progressive lending rounds into verified opportunities originated by participating SACCOs.

SACCOs retain full responsibility for borrower onboarding, underwriting, local compliance, disbursement, collections, and recovery. Loan metadata, verification status, funding events, and repayment history are recorded on-chain — creating transparent, verifiable lending activity and the first on-chain institutional credit histories on Cardano.

Pilot liquidity is administered independently from the project operating budget by Independent Pilot Trustees operating under a 2-of-3 multisignature custody arrangement.

## Consortium

| Partner | Role |
| :--- | :--- |
| **[Fairway](https://fairway.global)** | Metadata standard development, off-chain indexer implementation, verification infrastructure, SACCO onboarding, pilot execution, reporting, and ecosystem coordination. |
| **Fallen Icarus** | Credit market architecture, UTxO lending design review, metadata framework review, CIP-89 alignment, and technical oversight. Architect of cardano-loans and the metadata-based ZK approach this proposal implements. |
| **Sundial** | Capital provider engagement, market design, institutional onboarding requirements, and future capital formation frameworks for stablecoin, institutional, and Bitcoin-backed participants. |

### Pilot service providers

| Provider | Role |
| :--- | :--- |
| **Independent Pilot Trustees** | 2-of-3 multisignature custody of Treasury pilot liquidity. Convert ADA to stable assets, manage capital deployments and repayments, return remaining principal with Treasury-entitled proceeds at pilot conclusion. |
| **Encryptus** | Initial regulated settlement partner. Cross-border settlement infrastructure connecting Cardano-native stable assets with local banking rails. |

## Milestone roadmap

| Milestone | Timeline | Key deliverables |
| :--- | :--- | :--- |
| **M1** — Specification & design | Month 1 | Metadata spec published for community review; indexer architecture document; confirmed SACCO cohort with signed partnership agreements; pilot operating model. |
| **M2** — Infrastructure delivery | Months 2–3 | Working indexer on testnet; metadata standard validated against Pogun's credit market architecture (with compatibility for alternative implementations); developer documentation; settlement workflow validated end-to-end. |
| **M3** — Pilot round 1 | Months 4–5 | First pilot liquidity deployment into live contracts with verified SACCOs; mainnet infrastructure; dRep monitoring dashboard; first public progress report. |
| **M4** — Pilot round 2 & market validation | Months 6–8 | Recycled capital deployment; infrastructure improvements from operational feedback; capital provider engagement; assessment of granular lending models. |
| **M5** — Pilot round 3, capital readiness & handover | Months 9–12 | Final pilot round; public pilot case study; capital provider readiness framework; ecosystem handover documentation; remaining Treasury principal returned. |

## Open ecosystem commitment

All infrastructure, standards, and implementation learnings developed through this project will be open source and available for the broader Cardano ecosystem to adopt, extend, and integrate.

The project introduces an optional metadata, verification, and discovery layer that complements existing lending protocols — including Pogun — while preserving open participation and avoiding liquidity fragmentation. Should Pogun's production deployment be delayed or unavailable, the consortium may utilize equivalent audited lending contracts or compatible partner infrastructure while preserving the same metadata standard, indexer architecture, and credit market model.

## Status

🟡 **Pre-development.** This repository is a placeholder accompanying the Sundown Cardano Treasury proposal submission. Development begins upon proposal approval, following the milestone schedule above. The specification, indexer source, documentation, and pilot tooling will be published here as open-source deliverables.

## License

This project will be released under the [Apache License 2.0](https://www.apache.org/licenses/LICENSE-2.0).

## Links

- [Fairway](https://fairway.global) · [Aurora](https://aurora.fairway.global)
- Sundown Treasury Proposal — *link to be added upon governance submission*

---

<sub>Built by [Fairway Oy](https://fairway.global) (Helsinki, Finland) in consortium with Fallen Icarus and Sundial Protocol.</sub>
