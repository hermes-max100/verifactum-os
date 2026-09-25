# Verifactum OS

**Public canary specification for an enterprise legal-operations platform powered by the Proficio Engine.**

Verifactum is the enterprise product surface. Proficio is the internal execution kernel that normalizes requests, selects execution mode, assembles skills and tools, applies model-routing and governance policy, and emits a standardized outcome plus an execution receipt.

> **Canary status:** this repository currently defines interfaces, schemas, and workflow contracts. It is not a production legal system, does not contain client matter data, and does not by itself perform legal analysis or filing.

## Locked architecture

```text
Verifactum
  = enterprise legal-operations platform

Proficio Engine
  = internal execution kernel / orchestration standard

Governance Core
  = approvals, verification, audit, provenance, policy

Workflow Modules
  = contract review, NDA triage, DSR, holds, risk, briefing

Outcome Renderer
  = report views, redlines, receipts, exports

Provider-neutral execution
  = external worker / model / tool adapters behind owned interfaces
```

### Boundary rule

Verifactum and Proficio own the legal-product contracts. Provider-specific runtimes remain replaceable adapters. No external provider, model, or orchestration framework may become a required semantic dependency of the product contract.

## Proficio's seven internal contracts

1. Goal package
2. Execution mode
3. Skill registry
4. Tool policy registry
5. Model routing policy
6. Outcome contract
7. Execution trace / receipt envelope

## Execution modes

| Mode | Purpose |
|---|---|
| `DIRECT` | Deterministic, low-ambiguity workflow. |
| `GUIDED` | Multi-step workflow with structured routing. |
| `ESCALATED` | High-risk or ambiguous matter; delivery or action is blocked pending required review. |
| `FORGE` | Workflow creation, template authoring, or admin-level workflow revision. |

## Repository map

```text
docs/        Architecture and operating contracts
schema/      JSON Schema definitions for canonical envelopes and records
workflows/   Declarative workflow examples
CANARY.md    Compatibility and change-control policy
SECURITY.md  Public-canary security boundary
```

## Design invariants

- Human approval gates are explicit and machine-readable.
- Every material assertion or artifact can carry provenance.
- Verification state is distinct from generation state.
- Tool permissions are least-privilege and policy-driven.
- Model routing is replaceable and never changes the public outcome contract.
- Execution traces are append-oriented and independently auditable.
- Workflow modules communicate through versioned action contracts rather than UI assumptions.
- Public canary artifacts contain no privileged, confidential, or client-specific data.

See [`docs/ARCHITECTURE.md`](docs/ARCHITECTURE.md) and [`docs/PROFICIO_KERNEL.md`](docs/PROFICIO_KERNEL.md).

## License

No open-source license has been granted yet. All rights are reserved unless and until a license is added explicitly.
