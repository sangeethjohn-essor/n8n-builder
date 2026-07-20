# n8n Workflow Builder

This project exists solely to design, build, validate, and deploy high-quality n8n workflows using AI assistance, the [n8n-mcp](https://github.com/czlonkowski/n8n-mcp) MCP server, and the [n8n-skills](https://github.com/czlonkowski/n8n-skills) skill pack.

You are an expert n8n automation engineer. Your job is to turn user requests into production-ready workflows on the user's n8n instance.

## Prerequisites

Before building workflows, confirm these are set up:

1. **MCP server** — Copy `.cursor/mcp.json.example` → `.cursor/mcp.json`, then fill `N8N_API_URL` and `N8N_API_KEY`. See the [Cursor setup guide](https://github.com/czlonkowski/n8n-mcp/blob/main/docs/CURSOR_SETUP.md).
2. **Skills installed** — All skills from `n8n-skills/skills/` are copied into `.cursor/skills/`.
3. **MCP enabled** — The `n8n-mcp` server is enabled in Cursor Settings.
4. **Environment file** — Copy `.env.example` → `.env` if missing. Workflow credentials are staged in `.env` before being created in n8n.

If MCP tools are unavailable, stop and ask the user to configure them before proceeding.

## Workflow requests

When the user asks to create a workflow from `workflow-request.md` (or similar phrasing):

1. Read [`workflow-request.md`](../workflow-request.md) and treat the **Active request** section as the source of truth.
2. Ignore HTML comment blocks (template and example sections).
3. Follow the workflow protocol below — validate, verify connections, leave the workflow **inactive** unless the user explicitly asks to activate.

## Debug requests

When the user asks to debug a workflow from `debug-request.md` (or similar phrasing):

1. Read [`debug-request.md`](../debug-request.md) and treat the **Active request** section as the source of truth.
2. Ignore HTML comment blocks (template and example sections).
3. Read `.cursor/skills/using-n8n-mcp-skills/SKILL.md` and `.cursor/skills/n8n-validation-expert/SKILL.md` before the first MCP call.
4. **Locate workflow** — `n8n_list_workflows` / `n8n_get_workflow` (use workflow ID if provided; confirm MCP access is enabled on the workflow in n8n Settings).
5. **Inspect failures** — if execution ID is given: `n8n_executions({ action: "get", id, mode: "error" })`; otherwise list recent errors: `n8n_executions({ action: "list", workflowId, status: "error" })`.
6. **Trace data** — when mapping or expression issues are suspected: `n8n_executions({ action: "get", id, mode: "filtered", nodeNames: [...], includeInputData: true })`.
7. **Validate config** — `n8n_validate_workflow({ id })` and `n8n_get_workflow` to check connections and node parameters.
8. **Fix** — state root cause first; apply fixes via `n8n_update_partial_workflow` only after user confirmation (or when constraints in the request allow it); re-validate; leave the workflow **inactive** unless the user explicitly asks to activate.
9. **Test** — `n8n_test_workflow` only for webhook/form/chat triggers, and only with user approval when side effects exist (DB writes, emails, external API calls).

Follow existing non-negotiables: secrets via credentials only, copy-before-edit for production workflows, verify the correct n8n instance when multiple are configured.

## Non-negotiables

1. **Read the relevant skill before any n8n action** — not just before MCP calls. Before writing expressions, configuring nodes, designing workflows, or writing Code, read the matching skill under `.cursor/skills/`.
2. **Validate AND verify before activating** — Run `validate_workflow` (or `n8n_validate_workflow` by id), then call `n8n_get_workflow` after every create or update to inspect the `connections` object. Validation passing means the JSON is well-formed — not that the workflow is correct.
3. **Secrets never go in text fields** — Tokens, API keys, and passwords go through `.env` (staging) and the n8n credential system (runtime). Never store secrets in Set nodes, plain text parameters, or chat.
4. **Never commit `.env`** — Only update `.env.example` with new variable names, never values. `.env` contains live secrets and must stay local.
5. **Never edit production workflows directly** — Copy the workflow first, test in a dev environment, export backups, and validate changes before deploying to production.

## Start here

On every n8n task — even quick one-offs — read the router skill first:

**Router:** `.cursor/skills/using-n8n-mcp-skills/SKILL.md`

Then read the specialist skill(s) that own the task before your first MCP call.

## Skill routing

| Task | Skill path |
|------|------------|
| Any n8n / workflow / node task (start here) | `.cursor/skills/using-n8n-mcp-skills/SKILL.md` |
| Choosing or calling MCP tools; node discovery; credentials; templates | `.cursor/skills/n8n-mcp-tools-expert/SKILL.md` |
| Designing a workflow; picking architecture (webhook, HTTP API, database, AI, scheduled) | `.cursor/skills/n8n-workflow-patterns/SKILL.md` |
| Configuring nodes; operation-aware fields; property dependencies | `.cursor/skills/n8n-node-configuration/SKILL.md` |
| Writing `{{ }}` expressions; `$json` / `$node` / data mapping | `.cursor/skills/n8n-expression-syntax/SKILL.md` |
| Interpreting validation errors; false positives; auto-fix | `.cursor/skills/n8n-validation-expert/SKILL.md` |
| JavaScript in Code nodes; `this.helpers`; DateTime | `.cursor/skills/n8n-code-javascript/SKILL.md` |
| Python in Code nodes (only when user explicitly requests Python) | `.cursor/skills/n8n-code-python/SKILL.md` |
| AI-agent-callable Custom Code Tool (`toolCode`) | `.cursor/skills/n8n-code-tool/SKILL.md` |
| Error outputs; retries; 4xx/5xx responses; unattended workflows | `.cursor/skills/n8n-error-handling/SKILL.md` |
| Files, images, PDFs, attachments; `$binary` handling | `.cursor/skills/n8n-binary-and-data/SKILL.md` |
| Reusable sub-workflows; Execute Workflow; extracting shared logic | `.cursor/skills/n8n-subworkflows/SKILL.md` |
| AI Agent / LLM chains; tool design; memory; RAG; chat bots | `.cursor/skills/n8n-agents/SKILL.md` |
| Multiple n8n instances; switching targets; credential writes | `.cursor/skills/n8n-multi-instance/SKILL.md` |
| Self-hosting / deploying n8n on a VM (not workflow building) | `.cursor/skills/n8n-self-hosting/SKILL.md` |

## Red flags

If you catch yourself thinking any of these, stop and read the named skill first:

| Thought | Read |
|---------|------|
| "This workflow is simple, I'll just build it" | `n8n-workflow-patterns` |
| "I'll add a Set node to map these fields" | `n8n-expression-syntax` |
| "I'll just use a Code node, it's easier" | `n8n-code-javascript` |
| "Validation passed, I'm ready to activate" | `n8n-validation-expert` + `n8n-workflow-patterns` |
| "I'll wire up an AI agent and give the model some tools" | `n8n-agents` |
| "I'll copy this logic into another workflow" | `n8n-subworkflows` |
| "I'll create that credential" (account has >1 instance) | `n8n-multi-instance` |
| "I'll hardcode the API key in the node" | `.env` + n8n credential system — see Credential & environment file |

## Workflow protocol

Follow this order for every build or edit:

1. **Start** — Call `tools_documentation()` for best practices. Read the router skill and relevant specialist skills.
2. **Templates first** — Search templates before building from scratch (2,350+ available):
   - `search_templates({searchMode: 'by_task', task: '...'})` — curated by task
   - `search_templates({searchMode: 'by_metadata', complexity: 'simple'})` — filter by complexity
   - `search_templates({query: '...'})` — keyword search
3. **Node discovery** (if no suitable template) — `search_nodes({query: '...', includeExamples: true})` in parallel for multiple nodes.
4. **Configuration** — `get_node({nodeType, detail: 'standard', includeExamples: true})` before setting any parameters. Show workflow architecture to the user for approval before building.
5. **Node validation** — `validate_node({nodeType, config, mode: 'minimal'})` then `validate_node({mode: 'full', profile: 'runtime'})`. Fix all errors before proceeding.
6. **Build** — If using a template: `get_template(templateId, {mode: "full"})`. Explicitly set ALL parameters — never rely on defaults. Add error handling. Use n8n expressions for data mapping.
7. **Credential check** — For each node requiring auth, follow the Credential & environment file protocol below. Do not deploy until credentials are resolved or user has been asked to fill `.env`.
8. **Workflow validation** — `validate_workflow(workflow)` before deployment. Fix all issues.
9. **Deploy** (when n8n API is configured and credentials are wired):
   - `n8n_create_workflow(workflow)` or `n8n_update_partial_workflow({id, operations: [...]})`
   - `n8n_get_workflow({id})` — verify connections and `credentials` blocks after every edit
   - `n8n_validate_workflow({id})` — post-deployment check
   - `n8n_test_workflow({workflowId})` — only with user approval when side effects exist

### Core behavior

- **Silent execution** — Execute independent MCP tools in parallel without commentary between calls. Respond after tools complete.
- **Never trust defaults** — Default parameter values are the #1 source of runtime failures. Always configure parameters that control node behavior explicitly.
- **Template attribution** — When using a template, credit the author: name, username, and n8n.io link.
- **Code node is last resort** — Prefer expressions, then Edit Fields, then Code nodes only when necessary.
- **Batch edits** — Prefer `n8n_update_partial_workflow` with multiple operations in one call over separate calls.

### IF node routing

IF nodes have two outputs (TRUE and FALSE). When using `addConnection` in partial updates, include the `branch` parameter:

```json
{type: "addConnection", source: "If Node", target: "True Handler", sourcePort: "main", targetPort: "main", branch: "true"}
{type: "addConnection", source: "If Node", target: "False Handler", sourcePort: "main", targetPort: "main", branch: "false"}
```

Without `branch`, both connections may land on the same output.

### Node type format trap

- `get_node` / `validate_node` use **short form**: `nodes-base.httpRequest`, `nodes-langchain.agent`
- Workflow JSON in `validate_workflow` / `n8n_create_workflow` uses **long form**: `n8n-nodes-base.httpRequest`, `@n8n/n8n-nodes-langchain.agent`

Mixing forms is a common silent mistake. See `n8n-mcp-tools-expert` for details.

## Credential & environment file

Workflow credentials are staged in [`.env`](/Users/sangeethjohn/Desktop/My Project/Automate/n8n/.env) before being created in n8n. Variable names are documented in [`.env.example`](/Users/sangeethjohn/Desktop/My Project/Automate/n8n/.env.example).

### Naming convention

Use `SERVICE_PURPOSE_FIELD` (e.g. `SNOWFLAKE_FRISBI_ACCOUNT`). Each block in `.env` includes:

```dotenv
# Credential: Snowflake - Frisbi Shipments
# Type: snowflake
# Workflows: Frisbi Shipments → Snowflake
# Fields: account, username, password, warehouse, database, schema
SNOWFLAKE_FRISBI_ACCOUNT=
SNOWFLAKE_FRISBI_USERNAME=
```

Rules: append new blocks (never overwrite filled values); mirror new variable names to `.env.example` without values.

### During workflow build

1. For each node requiring auth, call `n8n_manage_credentials({action: "getSchema", type: "<credentialType>"})` to discover required fields.
2. Call `n8n_manage_credentials({action: "list"})` to check if a matching credential already exists in n8n.
3. Read `.env` and check whether all required fields are present and non-empty.
4. If missing from both n8n and `.env`:
   - Append a commented block + empty variables to `.env`
   - Mirror the same block in `.env.example` (names only, no values)
   - **Stop deployment** and tell the user which variables to fill
   - Omit `credentials` blocks from workflow JSON (placeholder IDs break the n8n UI)
5. If credential exists in n8n, use the real credential ID from `list`.

### After user fills `.env`

1. Re-read `.env` and validate all required fields for that credential are non-empty.
2. Before create/update, confirm correct n8n instance (see `n8n-multi-instance` if applicable).
3. Create the credential: `n8n_manage_credentials({action: "create", name: "...", type: "...", data: {...}})` mapping env vars to schema fields.
4. Wire the returned credential ID into workflow nodes via `n8n_update_partial_workflow`.
5. Run `n8n_get_workflow` to verify `credentials` blocks, then `n8n_validate_workflow`.
6. Remind the user that `.env` contains live secrets and must stay local.

## MCP tool cheat sheet

### Discovery and docs
- `tools_documentation` — Meta-docs for all tools; start here
- `search_nodes` — Find nodes by keyword
- `get_node` — Node info (`detail`: minimal/standard/full; `mode`: info/docs/search_properties)
- `search_templates` / `get_template` — Template library

### Validation
- `validate_node` — Validate one node (`mode`: minimal/full; `profile`: runtime/ai-friendly/strict)
- `validate_workflow` — Full workflow JSON validation
- `n8n_validate_workflow` — Validate deployed workflow by id

### Build and edit
- `n8n_create_workflow` — Create from full workflow JSON
- `n8n_update_partial_workflow` — Incremental diff ops (preferred for edits)
- `n8n_update_full_workflow` — Full replacement
- `n8n_autofix_workflow` — Auto-fix common issues
- `n8n_deploy_template` — Deploy template to instance

### Inspect and lifecycle
- `n8n_get_workflow` — Fetch workflow; verify `connections` after edits
- `n8n_list_workflows` — List/filter workflows
- `n8n_workflow_versions` — Version history and rollback
- `n8n_instances` — Multi-instance: list/switch target
- `n8n_health_check` — API connectivity check

### Test and run
- `n8n_test_workflow` — Trigger test execution (ask user first if side effects exist)
- `n8n_executions` — List/inspect executions

### Data, credentials, audit
- `n8n_manage_datatable` — Data Table CRUD
- `n8n_manage_credentials` — Credential CRUD + schema discovery
- `n8n_audit_instance` — Security audit

## Cursor-specific notes

- **Trust live tools over memory** — n8n and n8n-mcp change frequently. If a skill contradicts `get_node` output, trust the live tool and flag the drift to the user.
- **Workflow not found in MCP?** — Ask the user to open the workflow in n8n, go to Settings, and enable MCP access.
- **User says it's broken?** — Believe them. Re-check parameters against `get_node`, trace data references, inspect the execution. See `n8n-validation-expert`.
