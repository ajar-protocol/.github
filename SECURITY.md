# Security Policy

Ajar handles authority, signing, mandates, action execution, and receipts. Treat
security reports seriously and avoid public disclosure until a maintainer has
triaged the issue.

## Private Disclosure

Report suspected vulnerabilities to:

Hardik Modha <hardik@technopers.com>

When reporting, include:

- affected repo, commit, version, or document section
- a clear reproduction path
- expected vs. observed behavior
- security impact
- whether any keys, mandates, receipts, or owner data may be exposed

Do not open public GitHub issues for active vulnerabilities.

## Scope

In scope:

- signature verification bypass
- manifest spoofing or rollback
- mandate scope/cap bypass
- SIMULATE / Offer / Commit state-machine bugs
- key custody flaws
- policy monitor bypass
- action execution outside declared policy
- receipt tampering or audit-log integrity issues
- server-side LLM or rendering paths that violate documented invariants

Out of scope for private security handling:

- ordinary documentation typos
- missing features already marked as future work
- speculative issues without a plausible exploit path

## Handling

Maintainers should acknowledge reports within 72 hours, triage privately, and
publish an advisory or changelog entry after a fix is available. Add adversarial
conformance vectors after the fix when the issue affects protocol behavior.
