# MCP release review gates

Use these gates to assign a status. A generated server can be delivered as a draft without passing every gate, but it must not be described as production-ready when a blocking gate is open.

## Gate checklist

### Contract

- Every exposed Tool/Resource/Prompt has source evidence and a stable description.
- Input and output schemas match the source contract, including required fields, enums, pagination, and nullability.
- Excluded endpoints have a recorded reason.
- Mapping tests cover at least one success and one source-level error per exposed operation.

### Security

- Authentication and authorization are inherited or explicitly implemented; no secrets are committed.
- Tenant or user scope is enforced server-side and tested for cross-scope leakage.
- Sensitive output fields have a documented redaction policy.
- Destructive and privileged operations are approved, gated, and auditable.
- Generic arbitrary execution paths are absent.

### Reliability

- Timeouts, bounded retries, rate limits, and downstream failures produce stable MCP errors.
- Non-idempotent writes cannot be duplicated by automatic retries.
- Long-running operations return a task identifier and have status/result behavior.
- Logs and metrics contain request correlation data without secrets or sensitive payloads.

### Operability

- The selected transport and authentication configuration are documented.
- Startup, discovery, and representative Tool calls are verified in the target mode.
- Build/type checks and focused tests pass, or failures are recorded with their impact.
- Configuration uses environment or secret-manager references and has a rollback/versioning path.

## Status rules

- `ready`: contract, security, reliability, and operability gates pass; remaining items are routine follow-up.
- `conditional`: the integration is usable for a bounded environment, but a non-blocking limitation or an explicitly accepted production gap remains.
- `blocked`: a missing contract, broken build/test, unresolved authorization boundary, secret handling issue, tenant isolation issue, or unreviewed high-risk operation prevents safe use.

Report the exact open gate, evidence, and next action. Do not convert an unknown into a pass merely because the interface document is complete.
