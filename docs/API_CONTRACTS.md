# Action Contracts

This document defines logical actions, not transport-specific REST endpoints. Implementations may expose HTTP, queues, SDKs, or internal calls so long as semantics remain equivalent.

## Core actions

### `matter.create`

Input: matter metadata and tenant context.  
Output: canonical matter record.

### `document.register`

Input: matter reference, document metadata, content reference, provenance.  
Output: canonical document record.

### `workflow.start`

Input: Goal Package.  
Output: Workflow Run with resolved mode, workflow version, policy profile, and initial state.

### `workflow.continue`

Input: workflow run ID plus a permitted continuation event.  
Output: updated Workflow Run.

### `approval.decide`

Input: approval request ID, decision, actor, optional rationale.  
Output: immutable decision record plus workflow transition when authorized.

### `verification.run`

Input: target artifact or outcome plus verification profile.  
Output: Verification Run.

### `outcome.get`

Input: workflow run ID and requested representation.  
Output: Outcome Contract.

### `receipt.get`

Input: workflow run ID.  
Output: Execution Trace / Receipt envelope.

## Contract rules

1. IDs are opaque strings.
2. Timestamps use RFC 3339 UTC.
3. Every mutable record carries a schema version and record version.
4. Policy resolution is captured by immutable policy/version identifiers in the receipt.
5. A caller cannot convert an `ESCALATED` or `AWAITING_APPROVAL` run to `COMPLETED` by presentation-layer action.
6. External side effects require an explicit tool-policy decision recorded in the trace.
