# API-to-MCP intermediate model

Use this model as the stable handoff between document parsing, human review, and code generation. YAML is illustrative; use JSON when the repository's tooling requires it. Preserve the original operation identifier and evidence location so every generated Tool can be traced back to the source document.

```yaml
source:
  document: openapi.yaml
  version: "3.0.3"
  base_url: "https://api.example.test"
  evidence: "paths./orders/{order_id}.get"

operation:
  source_id: getOrder
  method: GET
  path: /orders/{order_id}
  summary: Get order detail
  category: query                 # query | command | async | document | internal
  mcp_kind: tool                  # tool | resource | prompt | exclude
  mcp_name: get_order_detail
  description: Query one order by its identifier.
  input_schema: {}
  output_schema: {}
  error_codes: []
  pagination: null
  side_effect: none               # none | creates | updates | deletes | external_send
  risk: L0                        # L0 | L1 | L2 | L3
  confirmation: false
  idempotency: not_applicable
  permission: orders.read
  tenant_scope: required
  data_sensitivity: internal      # public | internal | confidential | restricted
  auth: inherited                 # inherited | oauth | api_key | service_token
  status: draft                   # draft | approved | excluded | blocked
  notes: []
```

Required decisions before generation:

- `mcp_kind`, `mcp_name`, and `description`;
- complete input and output schema, including path/query/body/header mapping;
- side effect, risk, permission, tenant scope, and confirmation behavior;
- error and retry behavior, pagination or asynchronous task behavior;
- sensitive fields and redaction rules;
- source evidence and any assumption that is not stated in the document.

Keep excluded operations in the inventory with a reason. This makes omissions auditable and prevents a later regeneration from exposing them accidentally.
