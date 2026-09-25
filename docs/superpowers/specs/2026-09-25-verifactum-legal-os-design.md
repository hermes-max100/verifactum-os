# Verifactum Legal OS Design Specification

**Status:** Proposed design specification for human review before implementation planning  
**Date:** 2026-09-25  
**Repository:** `hermes-max100/verifactum-os`  
**Scope:** Legal OS trust model specialization of the existing Verifactum / Proficio architecture

## 1. Purpose and inheritance

This specification defines the trust, provenance, epistemic, lifecycle, storage, and agent-authority model for Verifactum Legal OS.

It **inherits** the existing repository contracts in:

- `docs/ARCHITECTURE.md`
- `docs/PROFICIO_KERNEL.md`
- `docs/API_CONTRACTS.md`
- `docs/WORKFLOW_ENGINE.md`
- `CANARY.md`

It does **not** introduce a competing orchestration layer. Verifactum remains the enterprise legal-operations product plane; Proficio remains the internal execution kernel; Governance Core remains the authority plane. Provider-specific models, tools, runtimes, and orchestration frameworks remain replaceable adapters behind Verifactum/Proficio-owned interfaces.

## 2. North-star invariant

> Nothing derived may lose its provenance path.

Every material proposition or output must be traversable backward through:

`Output Segment -> Argument / Proposition -> Fact / Claim -> Evidence Object -> Source Artifact -> immutable identity/history`

A second invariant governs authority:

> The language model never owns canonical truth.

Agents may ingest, extract, classify, propose, challenge, and draft. Promotion into trusted state requires deterministic validation, provenance, policy, independent verification where required, and human approval for consequential legal acts.

## 3. Canonical object envelope

Every material object uses a common envelope:

- `object_id`
- `object_type`
- `matter_id`
- `version`
- `status`
- `created_at`
- `created_by`
- `content_hash`
- `source_refs[]`
- `provenance_refs[]`
- `execution_refs[]`
- `supersedes_id`
- `superseded_by_id`
- `verification_state`
- `confidence`
- `policy_state`
- `metadata`

Identity semantics are strict:

- `object_id` identifies the logical object.
- `version` identifies a historical state of that object.
- `content_hash` identifies the exact content bytes or canonical serialized content.

These fields must never be conflated.

### 3.1 Core object types

The initial Legal OS object model includes:

- `SourceArtifact`
- `EvidenceObject`
- `Fact`
- `Claim`
- `Issue`
- `Authority`
- `Argument`
- `OutputArtifact`
- `ExecutionRun`
- `PolicyDecision`
- `Approval`
- first-class `Relationship` / provenance edge objects

Relationships are not implicit graph decorations. Each relationship is independently versioned, attributable, verifiable, disputable, supersedable, and auditable.

## 4. Epistemic model

Epistemic type is separate from lifecycle state.

Initial epistemic types:

- `OBSERVED_FACT`
- `ASSERTION`
- `ADMISSION`
- `STIPULATION`
- `JUDICIAL_FINDING`
- `EXPERT_OPINION`
- `INFERENCE`
- `MODEL_INFERENCE`
- `LEGAL_PROPOSITION`

Examples:

- `ASSERTION + VERIFIED` means the system verified that the assertion was made and attributed correctly; it does not mean the assertion is true.
- `OBSERVED_FACT + DISPUTED` means source-backed evidence exists, but the proposition or its interpretation remains contested.
- `LEGAL_PROPOSITION + SUPERSEDED` means the proposition previously existed in the governed corpus but is no longer current authority for the relevant scope.

Inference records should identify method, such as `HUMAN`, `DETERMINISTIC_RULE`, `STATISTICAL`, `MODEL`, or `HYBRID`.

## 5. Lifecycle and promotion state machine

Primary lifecycle:

`INGESTED -> EXTRACTED -> PROPOSED -> VERIFIED -> CANONICAL`

Additional states:

- `DISPUTED`
- `REJECTED`
- `SUPERSEDED`
- `REVOKED`
- `UNRESOLVED`

### 5.1 Transition rules

#### `INGESTED -> EXTRACTED`

Requires:

- source artifact integrity check,
- exact source locator,
- extraction execution receipt,
- retained source linkage.

May be performed automatically by an authorized extractor.

#### `EXTRACTED -> PROPOSED`

Requires:

- canonical schema validity,
- epistemic type assignment,
- complete source/provenance references,
- recorded extraction method and confidence.

May be performed automatically by authorized extraction/research roles.

#### `PROPOSED -> VERIFIED`

Requires:

- source existence and integrity validation,
- attribution validation,
- provenance-chain validation,
- contradiction scan,
- policy checks,
- independent verification where policy requires it.

The producing agent may not satisfy an independent-verifier requirement for its own output.

#### `VERIFIED -> CANONICAL`

Requires:

- complete provenance,
- valid verification receipt,
- no unresolved blocking contradiction unless policy explicitly permits canonical-disputed state,
- required governance decision,
- required human approval for material legal facts, positions, or consequential classifications.

The producing agent may never self-promote to `CANONICAL`.

### 5.2 Nondestructive state history

No transition rewrites history. Every successful or denied transition emits an immutable receipt containing at least:

- `transition_id`
- `object_id`
- `object_version`
- `from_state`
- `requested_state`
- `final_state`
- `actor_id`
- `actor_role`
- `verifier_id` where applicable
- `timestamp`
- `reason_code`
- `evidence_refs[]`
- `provenance_refs[]`
- `execution_run_id`
- `policy_decision_id`
- `approval_refs[]`
- `previous_transition_hash`
- `transition_hash`

Denied promotions are retained as audit evidence.

## 6. Provenance edge schema

Each material relationship is a first-class object with fields including:

- `edge_id`
- `edge_type`
- `from_object_id`
- `from_object_version`
- `to_object_id`
- `to_object_version`
- `matter_id`
- `created_at`
- `created_by`
- `execution_run_id`
- `source_refs[]`
- `evidence_refs[]`
- `provenance_refs[]`
- `assertion_scope`
- `confidence`
- `verification_state`
- `lifecycle_state`
- `valid_from`
- `valid_to`
- `reason_code`
- `policy_decision_id`
- `approval_refs[]`
- `previous_edge_hash`
- `edge_hash`

### 6.1 Edge classes

#### Class A — promotion-capable

Examples:

- `EXTRACTED_FROM`
- `SUPPORTED_BY`
- `AUTHENTICATED_BY`
- `CORROBORATED_BY`
- `VERIFIED_BY`

These may contribute directly to verification/promotion only when source versions are pinned, locators are resolvable, provenance is complete, and integrity checks pass.

#### Class B — conflict-bearing

Examples:

- `CONTRADICTED_BY`
- `DISPUTES`
- `REFUTES`
- `OVERRULES`
- `SUPERSEDES`

A verified unresolved Class-B edge may block promotion unless the applicable policy explicitly allows a disputed canonical object.

#### Class C — analytical

Examples:

- `INFERRED_FROM`
- `SUPPORTS_ARGUMENT`
- `UNDERMINES_ARGUMENT`
- `RELEVANT_TO_ISSUE`
- `DISTINGUISHES`

Analytical edges support reasoning but are never sufficient by themselves to establish evidentiary truth.

### 6.2 Exact source locators

Evidentiary edges must support deterministic locators where applicable, including:

- document ID and version,
- page,
- paragraph,
- line range,
- bounding box,
- audio/video timestamp range,
- spreadsheet cell/range,
- JSON pointer,
- source content hash.

A citation to a whole document is insufficient when a more exact locator exists.

### 6.3 Conflict handling

Conflicting propositions coexist. Verifactum must preserve competing propositions and their evidence rather than collapsing them into a single value solely on confidence ranking.

## 7. Policy and approval model

Actions are grouped into four authority classes:

- `CLASS_0_READ`: retrieve governed data; autonomous when authorized.
- `CLASS_1_PROPOSE`: create proposed objects, analytical edges, drafts, or contradiction candidates; autonomous but noncanonical.
- `CLASS_2_TRUST`: verification, authority validation, contradiction resolution; requires independent verification or deterministic verifier according to policy.
- `CLASS_3_CONSEQUENCE`: canonical promotion of material legal conclusions, revocation, filing-ready approval, external submission, or irreversible legal action; requires explicit authorized-human approval.

### 7.1 Separation of duties

Hard constraints include:

- producer != independent verifier where independence is required;
- verifier != final approver where `CLASS_3` policy requires separate roles;
- model inference != canonical fact without independent evidence;
- drafter != filing authorizer;
- agent != policy engine.

Model/provider diversity may improve robustness but is **not sufficient evidence of independence**. Independent review still requires separate verification responsibility and, where relevant, separate evidence or source-validation paths.

### 7.2 Fail-closed conditions

Trusted actions stop when required signals are missing or invalid, including:

- missing provenance,
- unknown or unauthorized actor,
- expired authority,
- invalid source hash,
- unresolved blocking contradiction,
- missing verification receipt,
- missing required approval,
- policy engine unavailable,
- gateway bypass detected.

## 8. Storage architecture

Legal OS uses four logically separate stores.

### 8.1 Artifact Store

Stores original source artifacts and derived representations. It is content-addressed and versioned. Source bytes are never silently overwritten.

Derived artifacts such as OCR output, extracted text, or thumbnails remain separate artifacts with explicit provenance to the original.

### 8.2 Append-only Event Ledger

Stores durable execution history, including:

- execution manifests,
- policy decisions,
- state transitions,
- verification receipts,
- approvals,
- revocations,
- provenance mutations,
- security events.

Corrections create new events; historical events are not rewritten.

### 8.3 Temporal Knowledge Graph

Stores governed legal concepts and relationships, including parties, artifacts, evidence, facts, claims, issues, authorities, arguments, approvals, and provenance edges.

Nodes and edges are versioned and temporally scoped to support historical questions such as what was known, believed, disputed, or authoritative at a prior point in time.

### 8.4 Operational Store

Stores replaceable runtime state such as:

- job queues,
- temporary agent state,
- caches,
- search indexes,
- workflow locks,
- rate limits,
- UI/session state.

Operational state carries no evidentiary or canonical authority merely by existing.

### 8.5 Write-path invariant

Trusted write path:

`Request -> Gateway -> Policy Decision -> Execution -> Artifact/Object -> Verification if required -> Ledger Event Sealed -> Graph Update -> Operational Index Refresh`

The graph must not update first and log later.

### 8.6 Read-path invariant

Agents do not receive unrestricted database access. Reads occur through deterministic, policy-scoped query interfaces that return version-pinned result sets and are recorded in the execution manifest.

## 9. Agent capability contracts

### 9.1 Extractor

May:

- read authorized source artifacts,
- OCR/parse,
- identify exact locators,
- extract metadata,
- create `EXTRACTED` objects and `EXTRACTED_FROM` edges.

May not:

- declare truth,
- resolve disputes,
- create final legal conclusions,
- canonicalize,
- perform external legal actions.

### 9.2 Researcher

May:

- query governed graph/evidence,
- research authorities,
- create `PROPOSED` objects,
- suggest analytical relationships,
- identify missing evidence.

May propose; may not verify its own output or canonicalize.

### 9.3 Verifier

Must be independent of the producer when the policy requires independent verification.

May validate:

- source integrity,
- exact quotation/citation,
- attribution,
- chronology,
- jurisdiction,
- authority status,
- contradiction state,
- provenance completeness,
- schema/policy constraints.

May emit `VERIFICATION_PASS`, `VERIFICATION_FAIL`, `VERIFICATION_PARTIAL`, `CONFLICT_FOUND`, or `INSUFFICIENT_EVIDENCE` and may move eligible objects to `VERIFIED`. It may not self-grant canonical authority.

### 9.4 Adversarial Reviewer

Its default objective is to identify the strongest reason a proposed conclusion may be wrong.

It may create challenge, contradiction, counterargument, missing-evidence, distinguishing, limiting, and undermining objects/edges. It may not alter or delete the object it reviews.

### 9.5 Drafter

May draft briefs, motions, memos, declarations, chronologies, evidence tables, and argument structures from governed matter context.

It may not introduce unsupported material propositions. Unsupported content must be flagged rather than fabricated.

Every material output segment must be linkable through governed provenance to supporting evidence and/or authority.

### 9.6 Governance service

Governance enforcement should be principally deterministic software, not an LLM agent.

It owns:

- policy evaluation,
- separation-of-duty checks,
- receipt validation,
- provenance completeness checks,
- approval requirements,
- permitted state transitions,
- deny decisions,
- transition receipt sealing.

Agents request authority; the policy/governance layer decides authority.

### 9.7 Human approver

Authorized humans control `CLASS_3` consequential legal acts, including filing/submission and other binding or irreversible legal actions.

Approval must be scoped to exact objects/versions/actions and must not silently carry forward after material content changes.

## 10. Capability and delegation model

Each execution receives a short-lived, narrowly scoped capability token defining at least:

- `capability_id`
- `agent_id`
- `agent_role`
- `matter_id`
- `allowed_actions[]`
- `allowed_object_types[]`
- `allowed_tools[]`
- `allowed_data_classes[]`
- `issued_at`
- `expires_at`
- `policy_version`
- `delegated_by`
- `max_action_class`

There is no ambient authority.

Agent-to-agent delegation creates a new governed task. Credentials and authority are not inherited from the requesting agent. The receiving role obtains only its own independently authorized capabilities.

## 11. Agent memory and canonical knowledge

Agent memory is not canonical knowledge.

Permitted path:

`Memory / learned experience -> hypothesis or PROPOSED object -> evidence-backed verification -> governed canonical graph`

Forbidden path:

`Memory -> CANONICAL`

This preserves the separation among Knowledge, Learning, and Evidence.

## 12. Global hard-deny rules

No AI agent may directly:

- delete evidence,
- rewrite ledger history,
- alter original source bytes,
- change policy to increase its own authority,
- grant itself capabilities,
- self-verify a high-risk output where independent review is required,
- self-canonicalize,
- forge approval,
- submit a legal filing without explicit authorized-human approval,
- waive a legal right,
- accept a settlement,
- send binding legal communications without the required human authorization.

These are architectural controls, not prompt-level preferences.

## 13. Gateway execution lifecycle

Every trusted model, retrieval, API, and tool action must pass through a Verifactum/Proficio-owned gateway.

### Preflight

- authenticate actor/agent,
- authorize requested capability,
- classify data sensitivity,
- resolve policy,
- resolve exact prompt/input,
- pin source/retrieval versions,
- assign immutable `run_id`.

### Execute

- invoke only authorized model/tool/retriever operations,
- preserve model/provider identifiers and parameters,
- capture actual tool arguments/results and retrieval sets.

### Postflight

- hash outputs,
- record returned metadata/errors,
- run required provenance/compliance checks,
- classify output state (`UNVERIFIED`, `PROPOSED`, or eligible for later promotion).

### Seal

Append the execution manifest to the tamper-evident ledger and link every downstream derived artifact to the relevant run.

Invariant:

> No trusted action without preflight authorization; no trusted result without a sealed postflight receipt.

## 14. Reproducibility and provider opacity

Where a provider exposes immutable model fingerprints or snapshots, record them. Where it does not, record the strongest available reproducibility tuple:

- provider,
- exact model identifier,
- snapshot/fingerprint if available,
- API version,
- timestamp,
- inference parameters,
- resolved prompt/input as permitted by data policy,
- retrieval/source version hashes,
- tool calls/results,
- explicit reproducibility limitation.

Verifactum must never imply exact replay is possible when provider opacity prevents it.

## 15. Security/threat model

Initial P0 threat classes:

1. prompt injection,
2. data exfiltration,
3. privilege escalation through tools,
4. provenance tampering,
5. replay/audit falsification,
6. canonical fact / authority poisoning.

Primary controls include gateway enforcement, least privilege, signed/hashed append-only events, source/version pinning, independent verification, explicit promotion states, and nondestructive supersession.

## 16. Acceptance criteria for implementation planning

This design is ready to move into implementation planning only after human review confirms that:

1. the spec correctly inherits rather than replaces current Verifactum/Proficio contracts;
2. epistemic type and lifecycle state remain separate;
3. canonical promotion is impossible for the producing agent acting alone;
4. relationships themselves carry provenance and lifecycle state;
5. exact source locators are required where available;
6. trusted writes seal ledger events before graph projection/update;
7. agents operate with least-privilege scoped capabilities and zero credential delegation;
8. model diversity is not treated as sufficient independence;
9. consequential legal acts remain subject to explicit authorized-human control;
10. no implementation begins before the written implementation plan is reviewed and an execution method is selected.

## 17. Explicit non-goals for this specification

This document does not choose production vendors, databases, model providers, vector stores, cloud platforms, or UI frameworks. Those are implementation decisions constrained by this trust model and the existing provider-neutral architecture.

It also does not define matter-specific substantive law, client facts, or filing strategy.

## 18. Next stage

After human approval of this written specification, the next required stage is a written implementation plan that decomposes the design into repository changes, schemas, services, tests, migration/compatibility steps, and verification gates before implementation begins.
