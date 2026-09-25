# Operating Model

## Enterprise experience

The user-facing flow is intentionally simple:

1. provide or select matter materials;
2. choose a legal-operations task;
3. run the governed workflow;
4. inspect status: verified, escalated, awaiting approval, or completed;
5. review/export/continue.

Complexity remains in the execution layer.

## Roles

| Role | Authority |
|---|---|
| Requestor | Starts permitted workflows and supplies inputs. |
| Reviewer | Reviews escalated or policy-designated outputs. |
| Approver | Authorizes specified consequential actions or releases. |
| Policy Admin | Maintains policy profiles and tool permissions. |
| Workflow Admin | Authors/promotes workflow definitions under FORGE controls. |
| Auditor | Reads receipts, provenance, verification, and policy history. |

A deployment may map several roles to one person, but the logical authorities remain distinct.

## Matter boundary

Every execution belongs to a tenant and, when applicable, a matter. Cross-matter retrieval or action is denied unless an explicit policy grants it.

## Lifecycle

```text
REQUESTED
  -> NORMALIZED
  -> POLICY_EVALUATED
  -> RUNNING
  -> VERIFYING
  -> AWAITING_APPROVAL  (when required)
  -> COMPLETED

Any state may transition to:
  -> ESCALATED
  -> FAILED
  -> CANCELLED
```

`COMPLETED` means the workflow contract completed; it does not imply that every substantive legal conclusion is correct. Verification status and confidence remain separately represented.

## High-consequence actions

External submissions, destructive actions, releases of privileged/confidential material, changes to canonical policy, and other deployment-designated consequential actions require explicit policy authorization and, when configured, human approval.

## Records

The platform distinguishes:

- **Outcome:** what the workflow produced.
- **Verification:** what checks were performed and their status.
- **Approval:** who authorized a gated step and under which policy.
- **Trace:** what executed.
- **Audit event:** append-oriented governance/security event.
