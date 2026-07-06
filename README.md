# Aurora — Metadata Standard & Indexer for Cardano Credit Markets

> **An optional identity and compliance layer for P2P credit markets on Cardano.**
>
> Attach verifiable identity proofs, verification status, and compliance metadata to credit market UTxOs — then discover, filter, and monitor lending opportunities through an open indexer API.

---

## What this repository will contain

This repository is the home for two open-source components described in the **Sundown** Cardano Treasury proposal:

| Component | Purpose |
| :--- | :--- |
| **Metadata Standard** | A versioned schema for attaching identity proofs (e.g. Veridian ACDC credential commitments), verification status, and loan-level compliance information to credit market UTxOs as transaction metadata. |
| **Off-Chain Indexer** | A service that monitors credit market UTxOs on Cardano, verifies attached proofs, tracks loan lifecycles (origination → funding → repayment → closure), and exposes a query API for discovery and filtering. |

Both components are designed to work with Cardano's eUTxO model and align with [CIP-89 (Beacon Tokens)](https://github.com/cardano-foundation/CIPs/tree/master/CIP-0089) for distributed dApp discovery without centralized infrastructure.

## Why metadata, not smart-contract logic?

The standard uses transaction metadata rather than embedding identity checks inside lending validators. This is a deliberate architectural choice:

- **Efficiency.** No additional on-chain computation or script execution costs. Identity metadata is attached alongside existing lending transactions.
- **Flexibility.** The standard is extensible — new proof types (ZK proofs via Midnight, additional credential frameworks) can be added without modifying lending contracts.
- **Permissionlessness.** Lending contracts remain open. The metadata layer is an _opt-in_ extension that enables compliant participation without restricting the underlying protocol.

## How it works

```
1. Origination     Verified lending institution publishes a Loan Request UTxO
                   with identity metadata attached (credential commitment,
                   verification status, compliance references).

2. Indexing        The off-chain indexer detects the UTxO, verifies the attached
                   proofs, and makes the opportunity queryable.

3. Discovery       Capital providers query the indexer to find lending
                   opportunities filtered by jurisdiction, verification status,
                   or other metadata attributes.

4. Funding         Eligible capital is deployed into verified lending
                   opportunities through the credit market infrastructure.

5. Repayment       As loans are repaid, repayment history is trustlessly
                   associated with the originating entity and its verification
                   credentials — building toward privacy-preserving reputation
                   systems over time.
```

## Repository structure (planned)

```
aurora-metadata-standard-and-indexer/
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
│   │   ├── chain/             # Cardano chain sync (Blockfrost / Ogmios)
│   │   ├── verify/            # Proof verification logic
│   │   ├── lifecycle/         # Loan state tracking
│   │   └── api/               # Query API (REST)
│   ├── Dockerfile
│   └── README.md              # Indexer-specific setup and operation guide
│
├── docs/
│   ├── integration-guide.md   # For originators and capital providers
│   ├── architecture.md        # System design and data flow
│   └── audit-procedure.md     # KYC commitment verification walkthrough
│
├── pilot/
│   └── dashboard/             # Lightweight dRep monitoring dashboard
│
├── LICENSE                    # Apache 2.0
└── README.md                  # This file
```

## Pilot context

The infrastructure is validated through a real-world pilot with Ethiopian Savings and Credit Cooperative Organizations (SACCOs). Treasury-provided USDM serves as revolving pilot capital deployed into verified lending opportunities originated by participating SACCOs.

SACCOs retain full responsibility for borrower onboarding, underwriting, local compliance, disbursement, and collections. The metadata standard and indexer provide the verifiable, transparent layer that connects these institutions to digital capital — without exposing borrower data on-chain.

## Consortium

| Partner | Role |
| :--- | :--- |
| **[Fairway](https://fairway.global)** | Metadata standard development, off-chain indexer implementation, verification infrastructure, SACCO onboarding, pilot execution, reporting, and ecosystem coordination. |
| **Sundial** | Capital provider engagement, market design, institutional onboarding requirements, and future capital formation frameworks. |
| **Fallen Icarus** | Credit market architecture guidance, UTxO lending design review, metadata framework review, and technical alignment with existing Cardano P2P lending infrastructure. |

## Milestone roadmap

| Milestone | Timeline | Key deliverables |
| :--- | :--- | :--- |
| **M1** — Specification & design | Month 1 | Metadata spec published for community review; indexer architecture document; confirmed SACCO cohort; pilot operating model. |
| **M2** — Infrastructure delivery | Months 2–3 | Working indexer on testnet; metadata standard tested against credit market contracts; developer documentation; settlement workflow validated end-to-end. |
| **M3** — Pilot round 1 | Months 4–5 | First USDM deployment into live contracts; mainnet infrastructure; dRep monitoring dashboard; first public progress report. |
| **M4** — Pilot round 2 & market validation | Months 6–8 | Recycled capital deployment; infrastructure improvements; capital provider engagement; assessment of granular lending models. |
| **M5** — Pilot round 3, capital readiness & handover | Months 9–12 | Final pilot round; public pilot case study; capital provider readiness framework; ecosystem handover documentation. |

## Technical foundations

- **Cardano eUTxO model** — each loan is a discrete, auditable on-chain object.
- **CIP-89 Beacon Tokens** — distributed discovery without centralized registries.
- **CIP-20 Transaction Metadata** — standard label for human-readable transaction messages.
- **Veridian (KERI/ACDC)** — credential commitments for privacy-preserving identity binding.
- **USDM** — regulated Cardano-native stablecoin for settlement.
- **Midnight (future)** — ZK proof upgrade path for on-chain credential verification without disclosure.

## Status

🟡 **Pre-development.** This repository is a placeholder accompanying the Sundown Cardano Treasury proposal submission. Development begins upon proposal approval, following the milestone schedule above. The specification, indexer source, documentation, and pilot tooling will be published here as open-source deliverables.

## License

This project will be released under the [Apache License 2.0](https://www.apache.org/licenses/LICENSE-2.0).

## Links

- [Fairway](https://fairway.global) · [Aurora](https://aurora.fairway.global)
- Sundown Treasury Proposal — *link to be added upon submission*

---

<sub>Built by [Fairway Oy](https://fairway.global) (Helsinki, Finland) in partnership with Sundial and Fallen Icarus.</sub>
