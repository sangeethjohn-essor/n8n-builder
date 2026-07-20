# Debug Request

Edit **Active request** below, then in Cursor **Agent mode** say:

> Debug the workflow described in debug-request.md

---

## Active request

<!-- Replace this section with your debug spec. Delete these placeholder lines when ready. -->

_(No active request yet — copy from the template or example below.)_

---

<!-- TEMPLATE — copy into Active request above; keep this block commented

Workflow: "<name>"
Workflow ID: <optional — from n8n URL>

Symptom: <error message, wrong output, silent failure, etc.>
Failing node: <node name, or "unknown">
Execution ID: <from n8n Executions tab, if available>

Expected: <what should happen>
Actual: <what happens instead>

Context:
- <when it started failing, recent changes, sample input, etc.>

Constraints:
- Do not activate the workflow unless I ask
- Do not modify other workflows
- Ask before running n8n_test_workflow (may have side effects)
- Show root cause before applying fixes

-->

<!-- EXAMPLE — Snowflake column mapping error (reference only; keep commented)

Workflow: "3PL - n8n builder"
Workflow ID: abc123

Symptom: Snowflake Insert node fails with "Invalid identifier COLUMN_X"
Failing node: Insert to Snowflake
Execution ID: 789

Expected: Rows from XLSX land in SANDBOX.LOGISTICS.VITAQUEST_INVENTORY
Actual: Execution stops at Snowflake with invalid column name

Context:
- Started after renaming a column in the Parse XLSX node
- Gmail trigger workflow — cannot use n8n_test_workflow

Constraints:
- Do not activate unless I ask
- Do not modify other workflows
- Ask before any test that writes to Snowflake
- Show root cause before applying fixes

-->
