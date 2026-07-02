# ENGINEERING.md - Org-Wide Engineering Standard

## Why these rules exist

Ajar implementations will be run and extended by many independent parties.
Optimize for the reader and the extender, not the writer.
Protocol software fails closed, evolves without breaking, and can be audited by strangers.
These rules are contracts for code review.

## Code quality bar

1. Every public function, type, and module MUST be documented at the point of definition.
2. Public examples MUST compile or run in the repository's documented test environment.
3. No request-serving path may panic, abort, call `unwrap` on fallible input, or throw an uncaught exception.
4. No verification path may panic, abort, call `unwrap` on fallible input, or throw an uncaught exception.
5. Every fallible operation MUST return a typed error.
6. Each component MUST define a typed error taxonomy.
7. Errors crossing a protocol boundary MUST map to the `Ajar-Error-Code` registry.
8. Internal errors MUST NOT leak stack traces, secrets, filesystem paths, dependency internals, or implementation details to the wire.
9. Names MUST use `GLOSSARY.md` terms exactly.
10. Protocol nouns MUST NOT be abbreviated.
11. Protocol concepts MUST NOT get synonyms.
12. Function names MUST expose the protocol operation or domain decision they implement.
13. Functions MUST be small enough to unit test in isolation.
14. Side effects MUST live at the edges of the component.
15. Core domain logic MUST be testable without network, filesystem, clock, process, browser, LLM, or database access.
16. Use a pure-core, imperative-shell structure unless a repo AGENTS.md defines a stricter local pattern.
17. Global mutable state is forbidden.
18. Caches MUST be explicit dependencies with bounded size, expiry policy, and tests.
19. Time MUST be injected in tests that exercise expiry, freshness, rate limits, retries, or revocation.
20. Randomness MUST be injected in tests that exercise ids, nonces, sampling, or backoff.
21. Comments MUST state constraints code cannot express.
22. Comments SHOULD explain why a rule exists, not restate what a line does.
23. Commented-out code is forbidden.
24. TODO comments MUST include a tracked issue, task, or AEP reference.
25. Formatters MUST run clean.
26. Linters MUST run clean.
27. Suppressions MUST be zero by default.
28. A suppression requires an inline justification comment and reviewer sign-off.
29. Generated files MUST declare their generator and source inputs.
30. Generated files MUST NOT be hand-edited unless the repo AGENTS.md explicitly permits it.

## Extensibility rules

1. Every variation point already named by the docs MUST be an interface from day one.
2. Settlement adapters are interfaces.
3. Rendering engines at the CDP boundary are interfaces. See ADR-010.
4. Harvest collectors are interfaces.
5. Extraction rule formats are interfaces.
6. Key stores, including OS keystore and HSM integrations, are interfaces.
7. Revocation transport is an interface.
8. Storage backends are interfaces.
9. Third parties extend by implementing an interface.
10. Third parties MUST NOT extend by forking, patching, or editing core files.
11. Interfaces MUST be discoverable, not hardcoded.
12. Use a registry pattern, entry points, feature-gated modules, or the repo's equivalent registration mechanism.
13. Registration MUST let third parties add implementations without touching core domain files.
14. Core domain logic MUST depend on interfaces only.
15. Concrete implementations MUST live at the edges.
16. Startup wiring MAY choose concrete implementations.
17. Dependency direction always points inward.
18. Protocol and domain modules sit at the center.
19. Adapters, persistence, network clients, browsers, LLM clients, and CLIs sit at the edges.
20. A module at the center MUST NOT import an edge module.
21. A test double MUST exist for each extension interface when the interface lands.
22. Public API surface is deliberate.
23. Everything exported is supported.
24. Internal modules MUST be private by default.
25. Additive public API changes are preferred.
26. Breaking public API changes require a major package version and a migration note.
27. Code compatibility policy MUST mirror spec section 13 when protocol-facing APIs change.
28. Configuration is for deployment variation.
29. Code is for behavior variation.
30. Behavior differences MUST be testable units.
31. Behavior differences MUST NOT be hidden in untested configuration branches.
32. Do not add an interface for style preference.
33. Do not add an interface for one concrete implementation unless a document names the planned variation.
34. An interface needs two real implementations, two planned implementations, or a cited doc naming the variation point.
35. Interface documentation MUST cite the doc section, ADR, issue, or task that names the variation.
36. Extension interfaces MUST declare ownership, stability, error behavior, and conformance expectations.
37. Extension interfaces crossing process boundaries MUST declare serialization format and versioning rules.
38. Extension interfaces that touch keys, signatures, mandates, receipts, policies, or Views MUST fail closed.

## Dependency policy

1. Prefer the standard library.
2. Each new dependency MUST be justified in the PR.
3. The justification MUST state what the dependency does.
4. The justification MUST state the license.
5. The justification MUST state maintenance signal.
6. The justification MUST state size and transitive cost.
7. The justification MUST state what writing the needed subset ourselves would cost.
8. Runtime dependencies need stricter review than development dependencies.
9. Dependencies in request-serving or verification paths need stricter review than build tooling.
10. License allowlist: Apache-2.0, MIT, BSD-2-Clause, BSD-3-Clause, ISC, and similarly permissive BSD-class licenses.
11. AGPL code MUST NOT be linked into Apache-2.0 deliverables.
12. Unlicensed code MUST NOT be linked.
13. Process isolation per ADR-010 and ADR-011 is the escape hatch for incompatible rendering engines.
14. Copied code snippets follow the same license rules as dependencies.
15. Lockfiles MUST be pinned.
16. Dependency updates MUST be reviewed like code.
17. Dependency updates MUST include changelog or release-note review when available.
18. Dependency updates MUST NOT weaken security, telemetry, or license constraints.

## Versioning and compatibility

1. Semver applies everywhere.
2. Wire-format compatibility is governed by the spec's `ajar_version`.
3. Library API compatibility is governed by the package version.
4. Never conflate `ajar_version` with package version.
5. Compatible wire additions follow spec section 13.
6. Breaking wire changes require the spec major-version process.
7. Public library removals require a major package version.
8. Deprecations MUST ship with a working alternative.
9. Deprecations MUST emit a warning where the language and runtime support it.
10. Deprecations MUST name a removal version.
11. Migration notes MUST include old behavior, new behavior, and exact caller action.
12. Registry values MUST be append-mostly.
13. Error codes, scopes, settlement adapter identifiers, headers, and field names MUST NOT be renumbered or repurposed.

## Performance and resources

1. Serve paths MUST make zero LLM calls.
2. Serve paths MUST perform zero per-request rendering.
3. Serve paths MUST NOT have unbounded memory growth.
4. Serve paths MUST NOT perform unbounded fan-out.
5. Gateway serve paths MAY proxy to the origin where the architecture requires it.
6. Any other serve-path network call MUST be justified by the repo AGENTS.md and tested.
7. Each component README MUST state latency, memory, body-size, and concurrency budgets once code exists.
8. Budgets SHOULD be asserted in tests where feasible.
9. Any PR that adds serve-path latency MUST report before and after measurements.
10. Any PR that adds persistent storage MUST state retention, cleanup, and migration behavior.
11. Every network operation MUST have a timeout.
12. Every network-facing service MUST have rate limits.
13. Every network-facing service MUST have body-size limits.
14. Retry loops MUST have a maximum attempt count.
15. Backoff MUST be bounded.
16. Streaming parsers are preferred for untrusted large inputs.
17. Untrusted payloads MUST be size-checked before expensive parsing.

## Observability

1. Logs MUST be structured.
2. Log event names MUST be stable.
3. Stable event names are public operational API once released.
4. Gateway interaction events MUST reach the audit log exactly once.
5. Audit log writes MUST be part of the interaction control flow.
6. Audit log failure MUST fail closed when the interaction changes state, spends money, or grants access.
7. Logs MUST NOT contain secrets.
8. Logs MUST NOT contain private key material.
9. Logs MUST NOT contain mandate contents at INFO.
10. Logs MUST NOT contain View content at INFO.
11. Logs MAY contain digests, ids, risk classes, verdicts, registry error codes, and timings.
12. Metrics MUST be owner-local by default.
13. Telemetry is forbidden.
14. Phone-home is forbidden.
15. ADR-014 is checked in review with a grep, not trusted by assertion.
16. Any intentional outbound reporting MUST be explicit protocol behavior configured by the owner.
17. Trace ids MUST NOT encode tenant, principal, mandate, receipt, or key material.

## Security engineering

1. Follow `docs/04-SECURITY-MODEL.md`.
2. Do not duplicate the threat model in implementation docs.
3. All cryptography MUST flow through each repo's single designated signing module.
4. Ad-hoc signing is forbidden.
5. Ad-hoc canonicalization is forbidden.
6. Ad-hoc hashing for protocol decisions is forbidden outside the designated module.
7. Constant-time comparisons are required for secrets, signatures, tokens, MACs, and key identifiers where timing can reveal validity.
8. Inputs at trust boundaries MUST be validated against schemas before use.
9. Trust boundaries include HTTP, CLI, config files, generated data, plugin inputs, MCP tool inputs, environment variables, and storage reads of mutable data.
10. Verification failures MUST fail closed.
11. Parse failures MUST fail closed.
12. Expiry failures MUST fail closed.
13. Revocation lookup failures MUST follow the spec's cache and fail-closed rules.
14. Default exposure MUST be deny.
15. Default action publication MUST be deny.
16. Key material MUST NOT cross module boundaries as raw bytes except inside designated signer or keystore interfaces.
17. Test keys MUST be obviously fake.
18. Fixtures MUST NOT contain real mandates, private receipts, customer data, or production secrets.

Repo AGENTS.md files bind these standards to each repo's architecture. Conflicts between this file and a repo AGENTS.md: the stricter rule wins; flag the conflict in an issue.
