# Canary Policy

This repository is the public contract canary for Verifactum OS.

## Purpose

The canary exists to make architectural decisions, contract versions, and compatibility expectations inspectable before production implementations depend on them.

## Locked decisions

The following boundaries are canonical unless superseded by an explicit versioned architecture decision:

1. **Verifactum** is the enterprise legal-operations product surface.
2. **Proficio Engine** is the internal execution kernel and orchestration standard.
3. **Governance Core** owns approval, verification, provenance, audit, and policy enforcement.
4. **Workflow Modules** are declarative, versioned, and product-domain specific.
5. **Outcome Renderer** consumes standardized outcome contracts rather than raw model output.
6. Provider/model/tool implementations sit behind owned interfaces and remain replaceable.
7. A workflow may not silently bypass a required human approval or verification gate.

## Compatibility

- `0.x` schemas may evolve while the canary is pre-production.
- Breaking changes require a schema version increment and migration note.
- Unknown fields should be ignored by tolerant consumers unless a schema declares `additionalProperties: false`.
- Required semantic changes must not be smuggled in as renderer-only or prompt-only changes.

## Change classes

- **PATCH:** wording, examples, non-semantic documentation.
- **MINOR:** backward-compatible fields, workflow capabilities, optional policy controls.
- **MAJOR:** breaking schema semantics, removed fields, changed approval guarantees, or changed trust boundaries.

## Promotion rule

A canary contract is promotable only when:

1. schema validation passes;
2. workflow examples conform to the referenced schema versions;
3. approval and verification behavior is explicit;
4. red-team review finds no silent authority escalation;
5. at least one independent implementation can consume the contract without private assumptions.

## Public-data boundary

Do not commit client matter facts, privileged work product, credentials, access tokens, private prompts containing sensitive matter context, or production audit logs to this repository.
