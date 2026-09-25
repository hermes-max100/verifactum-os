# Architecture

## 1. System model

Verifactum is a legal-operations product plane. Proficio is its execution kernel. Governance is an independent control plane applied before, during, and after execution.

```text
Human / Enterprise System
          |
          v
+--------------------------+
|       VERIFACTUM         |
| matter + workflow UX/API |
+------------+-------------+
             |
             v
+--------------------------+
|      PROFICIO ENGINE     |
| normalize -> plan -> run |
+---+----------+-----------+
    |          |
    |          +--------------------+
    v                               v
Governance Core               Workflow Modules
policy / approvals            contracts / NDA / DSR /
verification / audit          holds / risk / briefing
    |                               |
    +---------------+---------------+
                    v
             Tool / Model Ports
                    |
        replaceable provider adapters
                    |
                    v
             Outcome Contract
                    |
                    v
             Outcome Renderer
```

## 2. Architectural responsibilities

### Verifactum

Owns enterprise-facing concepts: tenant, matter, document, workflow request, review status, approval status, rendered outcome, export, and receipt visibility.

It must not expose provider-specific model semantics as product invariants.

### Proficio Engine

Owns the execution protocol:

1. normalize a request into a Goal Package;
2. resolve an Execution Mode;
3. resolve workflow + skill versions;
4. evaluate Tool Policy;
5. resolve Model Routing Policy;
6. execute stages;
7. invoke verification and approval gates;
8. emit an Outcome Contract;
9. emit an Execution Trace / Receipt.

### Governance Core

Owns authority, not content generation. It answers:

- Is this action allowed?
- Does it require approval?
- Which policy version applied?
- What evidence supports the output?
- Has required verification completed?
- What events must be auditable?
- May delivery or an external side effect proceed?

Generation components may request authority but may not grant themselves authority.

### Workflow Modules

A workflow is a declarative stage graph. Stages define inputs, capabilities, policy hooks, outputs, verification, and transition conditions.

### Outcome Renderer

Consumes the standardized outcome and renders product views such as reports, redlines, issue lists, receipts, and exports. It must not invent substantive findings that are absent from the outcome contract.

## 3. Trust boundaries

The following are separate trust domains:

- user input;
- retrieved evidence;
- model-generated claims;
- deterministic tool output;
- reviewer decisions;
- external side effects.

Crossing a trust boundary must be explicit in the execution trace.

## 4. Provider-neutral rule

Models, tool hosts, vector stores, orchestrators, and execution frameworks are replaceable adapters. Canonical semantics live in Verifactum/Proficio contracts.

A provider adapter may enrich execution metadata, but it may not change:

- required approvals;
- outcome meaning;
- provenance requirements;
- workflow state semantics;
- tenant or matter authority.

## 5. Failure model

Execution fails closed for:

- unresolved required approval;
- prohibited tool invocation;
- missing mandatory provenance;
- required verification failure;
- schema-invalid stage output;
- authority or tenant mismatch.

Non-critical enrichment failures may degrade gracefully only when the workflow contract explicitly permits it.
