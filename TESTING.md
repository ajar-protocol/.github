# Testing

This is the org-wide guide for test contributions. The `ajar` spec repo is the
current source for schemas, examples, seed vectors, and validation tools.

## Test Taxonomy

1. Schema validation

   Owner: `ajar/schemas/*.schema.json` and `ajar/examples`.

   Valid examples live under `ajar/examples/manifests`,
   `ajar/examples/implementer-manifests`, `ajar/examples/views`,
   `ajar/examples/policies`, `ajar/examples/errors`, and
   `ajar/examples/scenario-tickets`. Invalid examples live under
   `ajar/examples/invalid` and are indexed by `ajar/examples/invalid/index.json`.

2. Conformance vectors

   Owner: `ajar/test-vectors`, graduating to `conformance`.

   Current vector files are `core-vectors.json`, `runtime-vectors.json`,
   `scope-vectors.json`, `extension-vectors.json`,
   `manifest-check-vectors.json`, `http-signature-vectors.json`, and
   `crypto-signing.json`. Every spec MUST maps to at least one vector through
   `test-vectors/must-coverage.json`, enforced by
   `tools/check_must_coverage.py`. Sub-clause gaps remain visible as Follow-up
   markers in `test-vectors/must-coverage.md`.

3. Adversarial cases

   Owner: vectors and examples derived from `docs/04-SECURITY-MODEL.md`.

   The threat catalogue calls out prompt injection, forged manifests, expired
   offers, over-cap commits, rollback, and injection corpora. Adversarial parity
   is required for compatibility claims. Passing these cases is release-blocking.

4. End-to-end scenarios

   Owner: full protocol walks such as `ajar/examples/scenario-tickets`.

   The canonical scenario is a 50-ticket purchase. It covers mandate ->
   simulate -> offer -> commit -> receipt with real signing vectors. Under
   T1.14, these scenarios become executable harness runs in `conformance`.

## Author A Conformance Vector

1. Choose the spec MUST.
2. Use `ajar/test-vectors/must-coverage.json` to find the requirement id or add
   one when the MUST is new.
3. Pick the vector file by surface:

   - `core-vectors.json` for schema-backed protocol artifacts.
   - `runtime-vectors.json` for executable decision rules.
   - `scope-vectors.json` for mandate-scope matching.
   - `extension-vectors.json` for version and extension policy.
   - `manifest-check-vectors.json` for implementer-facing manifest checks.
   - `http-signature-vectors.json` for signed agent HTTP requests.

4. Follow the existing `kind`, `input`, `data`, and `expected` shape for that
   file. Do not invent a parallel format.
5. Wire every reject verdict to a registry error code from
   `ajar/registries/error-codes.md`.
6. Add the vector id to `test-vectors/must-coverage.json`.
7. Add the human-readable row or vector id to `test-vectors/must-coverage.md`.
8. Run:

```bash
make validate
make must
```

9. If vector counts change, update the expected-count lines in `ajar/README.md`
   and `ajar/tools/check_phase0.py`.
10. Vectors may be added. They must not be weakened.

Runtime vectors are checked by `runtime_verdict` in
`ajar/tools/validate_examples.py`. Current `kind` values are `http_surface`,
`domain_binding`, `kid_resolution`, `client_action_sequence`,
`commit_offer_state`, `mandate_required`, `mandate_status`,
`simulate_equivalence`, `simulation_offer_binding`, `view_provenance`,
`fallback_operation`, `version_change`, and `aep_requirement`.

Worked runtime vector:

```json
{
  "id": "client-r3-propose-without-simulate",
  "spec_section": "6",
  "kind": "client_action_sequence",
  "input": {
    "action_risk": "R3",
    "attempted_mode": "propose",
    "prior_simulation": false
  },
  "expected": {
    "verdict": "reject",
    "error_code": "AJAR-SIMULATE-REQUIRED"
  }
}
```

Core vectors use a schema-backed artifact path:

```json
{
  "id": "offer-expired-at-issue",
  "profile": "ACT",
  "spec_section": "7",
  "schema": "offer.schema.json",
  "input": "../examples/invalid/expired-offer/offer.json",
  "rule": "offer-expired-at-issue",
  "expected": {
    "verdict": "reject",
    "error_code": "AJAR-OFFER-EXPIRED"
  }
}
```

## Author An Invalid Example

Invalid examples are declared in `ajar/examples/invalid/index.json`.

Each case has:

- `id`
- `path`
- `schema`
- `expected`
- `rule` when the failure is semantic
- `error_code`
- `why`

`expected` is either `schema-fail` or `semantic-fail`.

Current semantic rules in `tools/validate_examples.py` are
`offer-expired-at-issue`, `offer-exceeds-mandate-cap`,
`manifest-sequence-rollback`, `manifest-expired-at-issue`, and
`manifest-lifetime-over-180d`.

Example index entry:

```json
{
  "id": "expired-offer",
  "path": "expired-offer/offer.json",
  "schema": "offer.schema.json",
  "expected": "semantic-fail",
  "rule": "offer-expired-at-issue",
  "error_code": "AJAR-OFFER-EXPIRED",
  "why": "expires_at is earlier than issued_at."
}
```

The invalid artifact must pass its JSON Schema before a semantic failure is
checked. Schema-fail cases must fail the named schema directly.

## Produce Signed Fixtures

Use `ajar/tools/signing_profile.py`.

The helper implements draft canonicalization for these fixtures: UTF-8 JSON,
sorted keys, no insignificant whitespace, and removal of the artifact's own
signature field before signing. Receipts remove `site_signature` and
`agent_signature` before signing.

Use obviously fake seeds. Never use real-looking keys.

```bash
python3 tools/signing_profile.py public-key 000102030405060708090a0b0c0d0e0f101112131415161718191a1b1c1d1e1f
python3 tools/signing_profile.py sign --type offer --seed-hex 404142434445464748494a4b4c4d4e4f505152535455565758595a5b5c5d5e5f examples/scenario-tickets/offer.json
python3 tools/signing_profile.py verify --type offer --public-key JUO5L_EJVRFHatyDadtt3JM2ZaEZeN2hQE7hBmypVZ0 --signature JxqIuUE8oAKHT44Kh8500yq9Jn2IFhzifut29kCSRmrY8k8hYIOQUHHRpT-wfuRyYoDKXDRpSQkAcA1vaTXGCw examples/scenario-tickets/offer.json
```

Canonical bytes, public key, SHA-256 digest, and signature land in `ajar/test-vectors/crypto-signing.json`. Signature changes are `crypto-change`
PRs and require human review.

## Contribute An End-To-End Scenario

Start from `ajar/examples/scenario-tickets`.

Current files:

- `mandate.json`
- `simulation.json`
- `offer.json`
- `receipt.json`

A new scenario must include:

- Every artifact schema-valid under `tools/validate_examples.py`.
- Signatures verifiable by `tools/signing_profile.py` and represented in
  `test-vectors/crypto-signing.json` when canonical bytes are part of the
  fixture.
- At least one deliberate failure branch with its registry error code.
- A README stating which spec sections the scenario exercises.

Use the real protocol order: manifest -> simulate -> offer -> commit -> receipt.

## Adversarial Contributions

Adversarial PRs are welcome when they encode documented threats from
`docs/04-SECURITY-MODEL.md`.

Do not use a public PR to disclose a new vulnerability. Follow `SECURITY.md`
first. Add the adversarial case after the fix or after maintainers confirm that
the threat is already documented.

## Implementation Tests

Gateway and Kernel tests are forward-looking but the rule is already fixed:
logic needs unit tests, and the conformance harness must stay green.

Anything touching the `docs/04-SECURITY-MODEL.md` catalogue needs an
adversarial test in the same PR.

Do not weaken, skip, or mark-flaky a conformance or adversarial case to pass
CI. End-to-end Definition of Done evidence belongs in the PR: command output,
harness report, or documented trace.

## Running Everything

For `ajar` work:

```bash
python3 -m venv .venv
. .venv/bin/activate
python3 -m pip install -r requirements-dev.txt
make validate
make phase0
make manifest-check
```

Targets:

- `make examples`: runs `tools/validate_examples.py`.
- `make must`: runs `tools/check_must_coverage.py`.
- `make links`: runs `tools/check_links.py`.
- `make markdown`: runs `tools/check_markdown.py`.
- `make validate`: runs `examples`, `must`, `links`, and `markdown`.
- `make phase0`: runs `tools/check_phase0.py`.
- `make manifest-check`: checks `examples/manifests/blog-core.json` as served
  from `/.well-known/ajar.json`.

Expected green output:

```text
Validated 14 valid examples, 8 invalid examples, 4 signing vectors, 2 HTTP signature vectors, 5 extension vectors, 6 manifest check vectors, 11 core vectors, 25 runtime vectors, and 12 scope vectors.
MUST coverage OK: 24 requirements mapped.
```
