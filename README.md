# n8n Workflow Builder

Design, build, and deploy n8n workflows from Cursor using the n8n-mcp MCP server and the skills in `.cursor/skills/`.

## Quick start

| Step | Action |
|------|--------|
| Setup MCP | `cp .cursor/mcp.json.example .cursor/mcp.json` → set `N8N_API_URL` and `N8N_API_KEY` from n8n Settings → API |
| Setup secrets | `cp .env.example .env` → fill credential variables when the agent asks |
| Request a workflow | Edit [workflow-request.md](workflow-request.md) → Agent chat: *"Create the workflow described in workflow-request.md"* |
| Debug a workflow | Edit [debug-request.md](debug-request.md) → Agent chat: *"Debug the workflow described in debug-request.md"* |
| Run the workflow | Test and activate in the n8n UI — workflows do not run from this terminal |

Enable the `n8n-mcp` server in **Cursor Settings → MCP**.

Agent behavior and MCP tool usage: [.cursor/AGENTS.md](.cursor/AGENTS.md).

## What to commit

Safe to commit:

- `workflow-request.md`
- `debug-request.md`
- `.env.example`
- `.cursor/mcp.json.example`
- `.cursor/AGENTS.md`, `.cursor/skills/`, `README.md`, `.gitignore`

Never commit:

- `.env` (live credentials)
- `.cursor/mcp.json` (contains your n8n API key)

## workflow-request.md

1. Open [workflow-request.md](workflow-request.md).
2. Fill in the **Active request** section (template and example are in HTML comments in the same file).
3. In Cursor **Agent mode**, say: **Create the workflow described in workflow-request.md**.

The agent builds the workflow on your n8n instance via MCP, validates it, and leaves it inactive for you to test.

## debug-request.md

1. Open [debug-request.md](debug-request.md).
2. Fill in the **Active request** section (template and example are in HTML comments in the same file).
3. In Cursor **Agent mode**, say: **Debug the workflow described in debug-request.md**.

The agent inspects the workflow and executions via MCP, identifies the root cause, and applies fixes only after explaining them — leaving the workflow inactive unless you ask to activate.

## Running workflows

Workflows execute on your n8n instance (e.g. `https://n8n.goessor.com`), not locally:

1. Open the workflow in n8n.
2. Wire credentials on nodes that need them.
3. **Test workflow** with sample data.
4. Toggle **Active** when ready.
