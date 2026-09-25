# Workflow Engine

## Workflow definition

A workflow is a versioned declarative graph of stages. Each stage declares:

- stage ID and type;
- required inputs;
- produced outputs;
- required capabilities/skills;
- allowed tool classes;
- verification requirements;
- approval hooks;
- transition rules;
- failure behavior.

## Stage classes

- `NORMALIZE` — convert user intent and inputs into canonical fields.
- `ANALYZE` — perform substantive analysis.
- `TRANSFORM` — produce a derivative artifact such as a redline or structured table.
- `VERIFY` — test citations, facts, schema, consistency, or policy conditions.
- `REVIEW` — human or independent reviewer gate.
- `ACT` — perform an external side effect under tool policy.
- `RENDER` — convert canonical outcome to a product representation.

## Mode semantics

### DIRECT

Use when the workflow is bounded, deterministic enough for configured automated checks, and no ambiguity threshold has been crossed.

### GUIDED

Use for structured multi-stage work requiring routing, retrieval, or intermediate checks.

### ESCALATED

Use when policy, ambiguity, conflict, verification failure, or consequence level requires human review before delivery or action.

### FORGE

Use only for authoring/changing workflow definitions, templates, policy-bound execution packages, or other administrative execution assets. FORGE does not bypass governance.

## Transition rule

A stage may transition only when:

1. its required output validates;
2. required verification is complete;
3. approval requirements are satisfied;
4. tool-policy decisions are recorded;
5. the transition condition evaluates true.

## Determinism

Where deterministic checks exist, use them before probabilistic judgment. Model output never substitutes for schema validation, authorization, cryptographic verification, or other deterministic controls.
