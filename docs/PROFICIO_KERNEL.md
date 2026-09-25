# Proficio Kernel Contract

Proficio is the internal execution standard for Verifactum.

## Seven contracts

### 1. Goal Package

Canonical request envelope: actor, tenant, matter, requested capability, inputs, constraints, desired outputs, risk hints, and requested execution mode if permitted.

Schema: `schema/goal_package.json`.

### 2. Execution Mode

One of `DIRECT`, `GUIDED`, `ESCALATED`, `FORGE`. Mode resolution is recorded; callers may request a mode but policy has final authority.

### 3. Skill Registry

Maps stable capability names to versioned implementation assets. Skills must declare input/output expectations and may not silently expand tool authority.

### 4. Tool Policy Registry

Defines tool classes, operations, data scopes, consequence levels, approval requirements, and deny rules.

Schema: `schema/tool_policy.json`.

### 5. Model Routing Policy

Resolves model/provider candidates by capability, sensitivity, reliability requirements, cost/latency constraints, and policy. Routing is an implementation concern; canonical outcome semantics remain stable.

### 6. Outcome Contract

Canonical delivery envelope containing findings/artifacts, provenance, verification state, approval state, warnings, and renderer hints.

Schema: `schema/output.json`.

### 7. Execution Trace / Receipt Envelope

Append-oriented record of resolved versions, stages, tools, model routes, approvals, verification, and terminal state.

Schema: `schema/workflow_run.json` plus `schema/audit_event.json`.

## Kernel invariants

- Policy is evaluated before consequential execution.
- Authority cannot be self-escalated by a skill, workflow stage, or model.
- Every tool call resolves against a policy rule.
- Required verification is blocking.
- Approval decisions are attributable to an actor and policy requirement.
- Renderer output is derived from canonical outcome data.
- Execution is reproducible to the degree allowed by nondeterministic dependencies; all resolved versions and external references are recorded.
