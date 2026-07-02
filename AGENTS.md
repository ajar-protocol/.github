# AGENTS.md — Instructions for AI Coding Agents

> **Org path convention:** this file and `ENGINEERING.md` apply org-wide (live in the `.github` repo). References like `docs/03-PROTOCOL-SPEC.md`, `GLOSSARY.md`, `DECISIONS.md`, `schemas/`, `examples/` resolve inside the **`ajar` spec repo**; `CONVERSION-PIPELINE.md` lives in **`ajar-gateway`**; `ROADMAP.md` and `tasks/` live in **`planning`**; conformance vectors currently seed in `ajar/test-vectors` and graduate to **`conformance`** as the executable harness lands.

*You are an AI coding agent working on Ajar. This file is your standing contract. Read it fully before any task. The human maintainer's instructions override this file; this file overrides your defaults.*

---

## Layered policy

This file (behavior) + `ENGINEERING.md` (engineering standards) + each repo's `AGENTS.md` (architecture contract) all bind. Stricter rule wins.

## 1. Project context (never lose this)

Ajar is an open, self-hosted, **owner-sovereign** layer letting websites open their doors to AI agents. Four components: Spec, Gateway (site-side), Client/Kernel (agent-side), Index (federated). The spec (`docs/03-PROTOCOL-SPEC.md`) is the single source of truth; implementations serve it, never the reverse.

Before writing anything, read in order: `README.md` → the doc(s) your task cites → the task ticket itself. If a task conflicts with the spec or an ADR, **stop and flag it** — do not silently pick one.

## 2. Non-negotiable invariants (violations = automatic rejection)

1. **Owner sovereignty:** nothing is exposed, published, or signed without an explicit owner action. Automation drafts; owners decide. Never "helpfully" auto-enable an action, widen exposure, or soften a default.
2. **Safe defaults:** fresh installs expose nothing; first approval exposes read-only public content only; authenticated/personal paths are unexposable without explicit override.
3. **The model is untrusted:** in Kernel code, no model output may reach the Executor except through the deterministic Policy Monitor. No prompt-level enforcement anywhere. No key material in model context, ever.
4. **LLM at build time only:** in Gateway code, LLM output is configuration (extraction rules, draft text) validated before use — never a serving-path dependency.
5. **Spec floors are floors:** R2+ ceremonies (SIMULATE, two-phase, mandates, receipts) may be raised by owner policy, never lowered by code.
6. **All crypto through the designated signing module** of each implementation. No ad-hoc signing, hashing, or canonicalization.
7. **Fail closed:** verification/parse/expiry/signature failures deny by default with registry error codes.
8. **Consensual fallback:** Kernel fallback honors robots/AIPREF/402, signs requests, flags output `unverified`, and never performs R2/R3-equivalents without per-operation human confirmation.

## 3. Working rules

- **One task = one PR.** Reference the task ID (e.g., `T1.6`) in branch name, commits, and PR title. Do not bundle unrelated changes.
- **DoD is binding.** A task is done when its Definition of Done holds and is demonstrated (tests, recorded output, or documented evidence in the PR).
- **Tests are part of the task**, not a follow-up: unit tests for logic, conformance vectors for protocol surfaces, adversarial tests for anything in `docs/04-SECURITY-MODEL.md`'s catalogue that your change touches.
- **Docs move with code:** any behavior change updates the relevant doc/spec section, `GLOSSARY.md` if terms change, and `DECISIONS.md` if a choice was made. Same PR.
- **Uncertainty protocol:** if the spec is ambiguous → file the ambiguity (T0.1-style issue), propose an interpretation in the PR description, mark the code with a tracked TODO referencing the issue. Never invent silent protocol behavior.
- **Dependencies:** minimize; justify each new one in the PR (license, maintenance, size). AGPL and unlicensed code may not be linked into Apache-2.0 deliverables (see ADR-010/011 for the rendering-engine exception pattern: process isolation behind CDP, not linking).
- **No secrets in repo, logs, or test fixtures.** Test keys must be obviously fake and generated, never real-looking.
- **Performance:** the Gateway serves per-request with zero LLM calls and no per-request rendering; if your change adds serve-path latency, measure and report it in the PR.

## 4. Code conventions

- Language per ADR-009 (Rust core; ecosystem-native for plugins). Follow the toolchain's standard formatter/linter; CI enforces.
- Errors: typed, mapped to the `Ajar-Error-Code` registry at protocol boundaries.
- Logging: structured; interaction events must reach the audit log exactly once; never log content of views, mandates' key material, or receipts beyond digests at INFO level.
- Public APIs of each crate/module documented; examples compile.
- Naming: use `GLOSSARY.md` terms exactly (Manifest, View, Chunk, Action, Mandate, Offer, Receipt, Owner Key, Operational Key, Tier, Gate, Monitor, Vault). Do not coin synonyms.

## 5. PR checklist (paste into every PR)

```
- [ ] Task ID referenced; scope limited to it
- [ ] DoD demonstrated (link evidence)
- [ ] Tests added: unit / conformance / adversarial (as applicable)
- [ ] Spec/docs/GLOSSARY/DECISIONS updated in this PR
- [ ] Invariants §2 reviewed against this diff — none violated
- [ ] New dependencies justified (license checked)
- [ ] No serve-path LLM calls introduced; latency impact reported if any
- [ ] Error paths fail closed with registry codes
```

## 6. Things you must never do

- Merge or self-approve protocol-surface changes without human review.
- Weaken, skip, or mark-flaky a conformance or adversarial test to make CI pass.
- Introduce telemetry/phone-home in owner-deployed software (Gateway/Kernel) — explicitly forbidden; owner-local metrics only.
- Copy code from repositories with incompatible licenses (check before porting *any* snippet).
- Change signature/canonicalization behavior outside a dedicated, human-reviewed PR marked `crypto-change`.
- Rename protocol fields, headers, or error codes outside a spec-version PR.

## 7. Escalate to the human maintainer when

Spec ambiguity blocks a MUST; any §2 invariant seems to require violation; a dependency/license conflict appears; an adversarial test reveals a real vulnerability (report privately per SECURITY.md, not in a public issue); or two documents in this repo contradict each other.
