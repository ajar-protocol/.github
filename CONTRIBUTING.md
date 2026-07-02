# Contributing to Ajar

> **Org path convention:** this file applies org-wide (lives in the `.github` repo). References like `docs/03-PROTOCOL-SPEC.md`, `GLOSSARY.md`, `DECISIONS.md`, `schemas/`, `examples/` resolve inside the **`ajar` spec repo**; `CONVERSION-PIPELINE.md` lives in **`ajar-gateway`**; `ROADMAP.md` and `tasks/` live in **`planning`**; conformance vectors currently seed in `ajar/test-vectors` and graduate to **`conformance`** as the executable harness lands.

Thank you for helping build the owner-sovereign agentic web.

## Before you start
1. Read `README.md`, then the doc relevant to your area (`docs/`), then `GLOSSARY.md`.
2. If you are an AI coding agent (or directing one), `AGENTS.md` is binding.
3. Check `DECISIONS.md` — if your idea contradicts an accepted ADR, open a discussion to revisit the ADR first; don't code against it.

## How work is organized
- All work maps to task IDs in `/tasks/phase-N-*.md` (e.g., `T1.6`). Issues are seeded in the owning repo and aggregated on the org Project board, [Ajar Roadmap](https://github.com/orgs/ajar-protocol/projects). One task = one PR.
- New ideas: open an issue proposing a task (context, deliverable, Definition of Done, which docs it touches). Maintainers slot it into a phase.
- Spec changes: separate PRs labeled `spec-change`, must update schemas, examples, conformance vectors, and (if needed) an ADR — together.

## Ground rules
- Definition of Done is binding; demonstrate it in the PR.
- Tests ship with the change (unit / conformance / adversarial as applicable). Never weaken a conformance or adversarial test to pass CI.
- Docs move with code in the same PR (spec, GLOSSARY, DECISIONS as applicable).
- Fail closed; registry error codes at protocol boundaries; no telemetry in owner-deployed software (ADR-014).
- Licenses: docs CC-BY-4.0, code Apache-2.0 (ADR-011). Check license compatibility before porting any snippet. DCO sign-off required (`git commit -s`).

## Security issues
Do **not** open public issues for vulnerabilities. Follow `SECURITY.md` (private disclosure). Adversarial test contributions are very welcome via normal PRs *after* the underlying issue is fixed or when they encode already-documented threats.

## Communication
- Issues: bugs, task proposals, spec ambiguities (cite the spec section).
- Discussions: design debates, ADR revisit proposals, ecosystem/interop topics.
- Be specific, cite documents by section, use GLOSSARY terms exactly.

## Recognition & governance
External contributors who sustain quality work gain review rights per phase-5 governance workstream (T5.7). The goal is explicitly for this project to outgrow its founders.
