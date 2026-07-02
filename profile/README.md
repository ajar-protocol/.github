# Ajar Protocol

Open websites to AI agents under owner-controlled policy.

Ajar is a protocol layer over HTTPS for agent-readable content, typed actions,
delegated authority, and verifiable receipts. A website owner publishes a signed
manifest that tells agents what is available, who may use it, at what price, and
under what proof of authority.

Ajar does not replace HTML, checkout systems, payment rails, MCP, or agent
frameworks. It gives websites a standard way to declare and govern agent access
while integrating with the protocols companies already use.

## Why Ajar Exists

Today, agents often treat websites as screens to scrape. That is expensive,
fragile, and unsafe for real actions. Ajar adds the missing owner-side contract:

| Need | Ajar Primitive |
|---|---|
| Meaning | Semantic Views served from the same URLs as HTML |
| Actions | Typed Actions with R0-R3 risk classes |
| Safety | SIMULATE dry runs and two-phase Offer/Commit |
| Authority | Principal-signed Mandates with scope, caps, expiry, and revocation |
| Accountability | Dual-signed Receipts and audit trails |
| Economics | 402-native metering with pluggable settlement |

## How It Works

1. A site owner installs or runs an Ajar Gateway, plugin, or native integration.
2. The owner reviews generated Views, Actions, policy, pricing, and gates.
3. The owner signs a Capability Manifest at `/.well-known/ajar.json`.
4. An agent Kernel discovers the manifest, verifies signatures, reads Views,
   simulates actions, checks mandates, commits only when allowed, and stores
   receipts.

The owner key is the root of authority. Automation drafts; owners decide.

## Repository Map

| Repo | Purpose |
|---|---|
| [`planning`](https://github.com/ajar-protocol/planning) | Roadmap, build order, positioning, and integration stories |
| [`ajar`](https://github.com/ajar-protocol/ajar) | Protocol specification, glossary, ADRs, schemas, examples, registries, AEPs |
| [`conformance`](https://github.com/ajar-protocol/conformance) | Test vectors and harnesses defining "Ajar Compatible" |
| [`ajar-gateway`](https://github.com/ajar-protocol/ajar-gateway) | Reference server for website owners |
| [`ajar-kernel`](https://github.com/ajar-protocol/ajar-kernel) | Reference client runtime for agents |
| [`ajar-docs-mcp`](https://github.com/ajar-protocol/ajar-docs-mcp) | MCP server exposing the Ajar spec to coding agents |
| [`ajar-woocommerce`](https://github.com/ajar-protocol/ajar-woocommerce) | WordPress/WooCommerce integration |
| [`ajar-shopify`](https://github.com/ajar-protocol/ajar-shopify) | Shopify integration design |
| [`ajar-examples`](https://github.com/ajar-protocol/ajar-examples) | Demo sites and benchmark scenarios |
| [`awesome-ajar`](https://github.com/ajar-protocol/awesome-ajar) | Directory of implementations, bridges, and conformant sites |

## Collaboration Model

Ajar is designed for adoption by companies, platforms, hosts, agencies, and
independent implementers. Reference implementations get no special status:
compatibility comes from the public conformance suite.

Ajar is intended to work alongside:

- MCP and WebMCP for tool/action transport
- ACP and UCP for commerce semantics
- x402, AP2, and MPP for settlement
- A2A for agent-to-agent workflows
- RFC 9421 / Web Bot Auth for signed agent identity
- UCAN-style chains for capability delegation

## Current Status

Ajar is in design-stage repository setup. The first public milestone is a
v0.1-draft spec, conformance vectors, and working read-layer demos.

Initial build order:

1. Freeze the CORE protocol draft.
2. Publish schemas, examples, registries, and conformance vectors.
3. Build the Gateway read layer and the docs MCP server.
4. Build the Kernel read/verify path.
5. Add actions, mandates, receipts, metering, and discovery.

## Governance And Licenses

Project contact: Hardik Modha <hardik@technopers.com>

- Specification and documentation: CC-BY-4.0
- Code and machine-readable implementation artifacts: Apache-2.0
- Long-term governance target: neutral standards venue; see ADR-012 in
  [`ajar`](https://github.com/ajar-protocol/ajar)

Contributors should read [`CONTRIBUTING.md`](https://github.com/ajar-protocol/.github/blob/main/CONTRIBUTING.md).
AI coding agents must follow [`AGENTS.md`](https://github.com/ajar-protocol/.github/blob/main/AGENTS.md).
