# Security Policy

## Public canary boundary

This repository is public. Treat every committed byte as permanently disclosed.

Never commit:

- client or matter data;
- privileged or confidential legal work product;
- credentials, API keys, tokens, cookies, or private endpoints;
- private model transcripts or production traces;
- unredacted evidence;
- production secrets or internal-only policy exceptions.

## Design expectations

Production implementations should provide:

- tenant isolation;
- encryption in transit and at rest;
- least-privilege tool authorization;
- immutable or tamper-evident execution receipts;
- explicit approval gates for consequential actions;
- provenance for source-grounded assertions;
- secret management outside source control;
- auditable policy-version resolution.

## Reporting

Do not disclose a live vulnerability through public issues if doing so would expose an exploitable production system. Use the repository owner's private security-contact channel when one is published.
