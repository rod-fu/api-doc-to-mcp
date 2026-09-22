---
name: api-doc-to-mcp
description: Convert OpenAPI, Swagger, Postman, Markdown, Word, PDF, or similar API docs into a runnable, reviewable MCP server, or audit an existing API-to-MCP wrapper. Use for API-to-MCP standardization, Tool/security design, and integration tests; exclude unrelated API client work.
---

# API Document to MCP

Convert an existing system's documented interfaces into a task-oriented MCP integration while preserving the system's business, identity, and authorization boundaries. Produce a usable implementation when the user asks to build; produce the mapping and review artifacts only when the user asks for analysis or a plan.

## Operating workflow

1. **Discover the source and target.** Locate the interface documents and the target repository. Identify the document format, API base URLs, authentication flow, current language/framework, existing MCP SDK or conventions, deployment target, tenant model, and test commands. Do not invent credentials. Keep unknowns as explicit assumptions or stubs.
2. **Normalize the interfaces.** Build an inventory of operations, parameters, request/response schemas, errors, auth requirements, pagination, side effects, and dependencies. Prefer structured parsing for OpenAPI/Swagger/Postman. For prose documents, extract evidence and mark fields that need confirmation. Read [references/intermediate-model.md](references/intermediate-model.md) before creating a mapping or implementation.
3. **Design the surface and classify risk.** Map tasks to Tools, stable data to Resources, and repeatable interactions to Prompts. Apply [references/mapping-rules.md](references/mapping-rules.md); exclude auth, health, debug, and implementation-only endpoints. Assign risk, permission, sensitivity, confirmation, idempotency, and tenant scope before approval.
   
   **🔴 CHECKPOINT · 🛑 STOP — mapping approval.** Before handlers for writes, unknown boundaries, or production use, present the mapping, exclusions, unknowns, and risks. A fully documented L0 read-only analysis may continue after recording them; otherwise stop for reviewer approval of the mapping and open assumptions.
4. **Classify risk and access.** Treat create/update/approve/send/delete/payment operations as writes. Do not silently expose destructive or privileged operations; keep them disabled or pending-review when useful.
   
   **🔴 CHECKPOINT · 🛑 STOP — high-risk enablement.** Do not enable delete, payment, approval, external-send, privileged-download, or other L2/L3 operations while permission, confirmation, audit, idempotency, and tenant boundaries are unknown. Stop and report `pending-review` or `blocked` until the required evidence is approved.
5. **Generate or update the integration.** Follow the repository's existing stack and official MCP SDK. Keep protocol handling, adapters, domain services, and infrastructure concerns separate. Tool handlers validate input, resolve the caller/tenant context, enforce authorization, call the existing API or SDK, normalize errors, redact sensitive output, and return concise structured content. Never place secrets in source or schemas. Use environment/configuration references for endpoints and credentials.
6. **Verify before calling it ready.** Run the repository's formatter, type checker, build, and focused tests when available. Test discovery, valid and invalid arguments, auth failures, tenant isolation, redaction, downstream timeout/error behavior, pagination, idempotency, and confirmation gates. If a real downstream system is unavailable, use contract fixtures or mocks and label that limitation. Read [references/review-gates.md](references/review-gates.md) before reporting readiness.
   
   **🔴 CHECKPOINT · 🛑 STOP — release decision.** Before describing the result as `ready`, show the open-gate list, test evidence, simulated-versus-live coverage, and residual risks. Stop at `conditional` or `blocked` when any release gate is unknown or failing; never promote by inference.
7. **Report the result.** Deliver the implementation or mapping, the source-to-MCP inventory, assumptions and unresolved questions, security/risk findings, tests run and their results, configuration/deployment instructions, and a clear `ready`, `conditional`, or `blocked` status. Keep generated code and documentation aligned with the same mapping source.

## Failure handling

Record each trigger, evidence, action, and resulting status; never silently guess or skip a failure.

| Trigger | First response | If it still fails |
| --- | --- | --- |
| Structured parser fails | Validate format/encoding and retry a supported parser | Use a review-only inventory with `unknown` fields; do not generate handlers from guesses |
| Prose field is missing or conflicting | Record page/heading/line evidence and keep the value unknown | Exclude or mark the operation `pending-review`; never merge schemas or invent defaults |
| Auth, tenant, permission, or sensitivity is undocumented | Keep authority server-derived and add a blocking review item | Do not expose writes/restricted data; mark `blocked` if isolation is unproven |
| Format/type/build/startup check fails | Fix the first mapping/adapter/config error and rerun the focused check | Deliver only a draft with failure evidence; do not call it runnable or `ready` |
| Downstream is unavailable or untestable | Use matching contract fixtures/mocks and label tests simulated | Set `conditional`; set `blocked` for production if auth, isolation, or writes remain untested |
| Call times out, is unknown, or exceeds a bound | Apply timeout, stable error mapping, and bounded retry only for transient/idempotent cases | Return a correlation ID; never retry unsafe writes or return raw/unbounded payloads |

Traceability, authorization, isolation, sensitive-data, and high-risk gaps block release until evidence closes them.

## Red-line anti-patterns

Reject every item; stop and report required violations.

| Wrong approach | Risk | Required replacement |
| --- | --- | --- |
| Mirror every endpoint, including auth/health/debug | Expands attack surface | Curate task Tools/Resources and record exclusions |
| Invent missing auth, tenant, permission, error, or field rules | Hides untraceable security decisions | Preserve `unknown`; route to `pending-review`/`blocked` |
| Let the model provide tenant, role, approval, or authority headers | Enables spoofing and cross-tenant access | Derive authority from the authenticated session server-side |
| Return raw payloads, traces, tokens, URLs, or unrestricted files | Leaks secrets and internal data | Redact fields, bound output, map errors, and control file references |
| Retry non-idempotent writes automatically | Duplicates irreversible effects | Require idempotency; otherwise fail once with a correlation ID |
| Execute high-risk work when confirmation semantics are absent | Turns an assumption into a side effect | Keep it disabled/pending-review and stop at the risk checkpoint |
| Poll forever or proxy arbitrary download URLs | Causes exhaustion or SSRF/exfiltration | Use task IDs, bounded status/results, and allowlisted server URLs |
| Call it `ready` after schema/unit tests only | Hides auth, isolation, downstream, and ops gaps | Apply release gates; use `conditional`/`blocked` when evidence is incomplete |

## Implementation rules

- Preserve the existing system as the business source of truth; keep business rules and authorization in its services.
- Prefer domain Tools such as `get_order_detail`; compose calls only with defined transaction, compensation, and failure behavior.
- Use stable names/descriptions that state purpose, identifiers, side effects, permissions, and limits; do not expose endpoint paths as the UX.
- Normalize pagination, dates, enums, errors, empty results, and bounded structured output.
- Use stdio locally and Streamable HTTP for hosted services unless the client requires another transport; keep transport separate from adapters.
- For long jobs expose create/status/result operations. Retry only bounded transient/idempotent calls; never infer production readiness from schemas alone.

## Reference routing

- Read `references/intermediate-model.md` when extracting interfaces or producing `mapping.yaml`.
- Read `references/mapping-rules.md` when selecting Tools, Resources, Prompts, names, schemas, or composition boundaries.
- Read `references/review-gates.md` when implementing, auditing, testing, or assigning a release status.
- Read `references/usage.md` when the user asks how to invoke or use this skill, needs a request template, or asks what its outputs and status values mean.

## Output contract

Return the interface inventory/mapping, requested code, placeholder-only configuration, focused tests, risk review, connection instructions, assumptions, unresolved items, and validation status. Omit code when the user requested analysis only.
