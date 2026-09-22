# API to MCP mapping rules

These are defaults. A repository's established conventions and the documented business contract take precedence; record deviations in the mapping.

## Operation mapping

| Source operation | Default MCP treatment |
| --- | --- |
| Safe `GET`/read query | Tool when it answers a user task; Resource when it is a stable addressable document or record |
| `POST` create/command | Tool with write permission, validation, idempotency decision, and confirmation when consequential |
| `PUT`/`PATCH` update | Tool with explicit target, changed fields, authorization, and conflict handling |
| `DELETE` | Pending review by default; expose only with explicit authorization and confirmation |
| File/document download | Resource or a Tool returning a bounded file reference, according to client capabilities |
| Job submission plus status endpoint | `start_*` and `get_*_status` Tools, or a domain equivalent; do not block indefinitely |
| Login, token refresh, internal health, debug, metrics | Exclude; handle through the transport or runtime |
| Several calls for one business action | One domain Tool only when transaction, rollback/compensation, and failure reporting are defined |

## Naming and schemas

- Use lowercase `snake_case`, stable domain nouns, and an action verb for Tools (`search_customers`, `approve_invoice`). Avoid version numbers and transport terms in names.
- Write descriptions for model selection: state purpose, required identifiers, side effects, permission-sensitive behavior, and what the result contains.
- Flatten only when it preserves meaning. Keep nested objects for domain structure. Mark required fields and enum meanings explicitly.
- Map path parameters to required input fields, query parameters to optional/required fields with defaults, and request bodies to a named object. Do not silently drop headers that carry tenant or concurrency context.
- Return bounded results. For lists, define page size limits and a cursor or next-page field. Do not expose unbounded database dumps.
- Convert implementation-specific errors to stable codes such as `INVALID_ARGUMENT`, `FORBIDDEN`, `NOT_FOUND`, `CONFLICT`, `RATE_LIMITED`, `TIMEOUT`, and `DEPENDENCY_ERROR`.

## Security and behavior

- Pass the authenticated caller and tenant context through the adapter; do not ask the model to supply authority fields unless the business contract explicitly requires them.
- Redact credentials, tokens, secrets, full identity numbers, and other restricted fields before returning content. Keep the redaction decision in the mapping.
- Never generate arbitrary SQL, shell, URL fetch, file path, or code execution Tools from a generic interface description.
- Separate preview from commit for high-risk operations where the source system supports it. Otherwise require explicit confirmation immediately before execution.
- For writes, document idempotency keys, conflict behavior, retry limits, and the authoritative operation identifier returned by the source system.
