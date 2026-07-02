# Onboarding

Your first contribution, end to end.

## 1. Orient

1. Start with the organization profile: https://github.com/ajar-protocol
2. Read the planning repo next: https://github.com/ajar-protocol/planning
3. Read `planning/ROADMAP.md` for phase goals and exit criteria.
4. Read `planning/BUILD-ORDER.md` for the build sequence:
   CORE protocol -> server -> client -> plugins -> actions, money, discovery.
5. Use the repository map in the organization profile:
   `planning`, `ajar`, `conformance`, `ajar-gateway`, `ajar-docs-mcp`,
   and `awesome-ajar` are public launch repos.
6. Current status: Phase 0 has the v0.1 draft baseline prepared. Phase 1,
   the Gateway MVP read layer, is kicking off.
7. The `ajar` spec repo is the source of truth for protocol behavior. If an
   implementation disagrees with the spec, the implementation is wrong.

## 2. Pick Work

1. Prefer planned work from the Ajar Roadmap Project board: https://github.com/orgs/ajar-protocol/projects/1
2. Start with issues labeled `good first issue` when learning the system.
3. If the work is not already tracked, open a task proposal issue.
4. A task proposal states context, phase or repo, deliverable, Definition of
   Done, and dependencies or blockers.
5. Claim a task by commenting on the issue before opening a PR.
6. Use one issue or planning task per PR. Do not bundle unrelated cleanup.

## 3. Set Up

1. Clone the repo that owns the task.
2. For spec, schema, example, or vector work in `ajar`, use a Python virtual
   environment. System Python needs a venv.
3. From the `ajar` repo:

```bash
python3 -m venv .venv
. .venv/bin/activate
python3 -m pip install -r requirements-dev.txt
make validate
python3 tools/check_phase0.py
```

4. Expected green output from `ajar/README.md`:

```text
Validated 14 valid examples, 8 invalid examples, 4 signing vectors, 2 HTTP signature vectors, 5 extension vectors, 6 manifest check vectors, 11 core vectors, 25 runtime vectors, and 12 scope vectors.
MUST coverage OK: 24 requirements mapped.
```

5. For manifest-specific edits, also run:

```bash
make manifest-check
```

6. Record the exact validation output in the PR when it is DoD evidence.

## 4. Work

1. Name branches with the task ID when one exists, for example `T1.6-template-clustering`.
2. Use GLOSSARY terms exactly: Manifest, View, Chunk, Action, Mandate, Offer,
   Receipt, Owner Key, Operational Key, Tier, Gate, Monitor, Vault.
3. One task means one PR.
4. Keep docs, tests, examples, schemas, registries, and vectors in the same PR
   when behavior changes require them.
5. The spec is the source of truth. Do not create silent protocol behavior.
6. If the spec is ambiguous, file the ambiguity, cite the section, and state
   the proposed interpretation in the PR.

## 5. Commit

1. DCO sign-off is required:

```bash
git commit -s
```

2. Documentation is CC-BY-4.0.
3. Code and machine-readable implementation artifacts are Apache-2.0.
4. Check license compatibility before porting text, code, generated artifacts,
   or dependencies.
5. Do not include secrets, real private keys, real mandates, private receipts,
   or customer data.

## 6. Open The PR

1. Use the PR template checklist.
2. Reference the task or issue.
3. Keep scope limited to the task.
4. Demonstrate the Definition of Done with tests, recorded output, or documented evidence.
5. Include unit, conformance, and adversarial tests as applicable.
6. Update docs when behavior changes.
7. Update spec, GLOSSARY, DECISIONS, schemas, examples, registries, and vectors
   when protocol behavior changes.
8. Review looks for the same invariants listed in `AGENTS.md` section 2: owner sovereignty, safe defaults, untrusted models, build-time-only LLM use in Gateway code, spec floors, designated signing modules, fail-closed error handling, and consensual fallback.
9. Those invariants apply to humans too.

## 7. After Merge

1. Watch follow-up issues from review.
2. Keep contributing in the same narrow style: small task, clear evidence,
   no hidden behavior changes.
3. Sustained quality work leads toward review rights under the phase-5 governance workstream.

## If You Get Stuck

Use `SUPPORT.md`:

- Bugs: open an issue in the affected repo.
- Spec questions: open a discussion or issue in `ajar`.
- Security reports: follow `SECURITY.md`; do not open a public issue.
- Integration questions: use `planning` discussions or issues until platform
  repos publish.
- General project direction: use `planning`.

AI-agent-driven contributions must follow `AGENTS.md`; it is binding.
