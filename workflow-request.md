# Workflow Request

Edit **Active request** below, then in Cursor **Agent mode** say:

> Create the workflow described in workflow-request.md

---

## Active request

<!-- Replace this section with your workflow spec. Delete these placeholder lines when ready. -->

_(No active request yet — copy from the template or example below.)_

---

<!-- TEMPLATE — copy into Active request above; keep this block commented

Create a NEW workflow in n8n named: "<name>"

Trigger: <e.g. Gmail unread mail to mailbox@example.com>
Input: <e.g. XLSX attachment from vendor>
Transform: <e.g. extract columns A, B, C; map to schema>
Output: <e.g. Snowflake table DATABASE.SCHEMA.TABLE_NAME>

Constraints:
- Do not modify existing workflows
- Leave inactive until I test
- Reuse existing n8n credentials where possible

-->

<!-- EXAMPLE — Vitaquest 3PL → Snowflake (reference only; keep commented)

Create a NEW workflow in n8n named: "3PL - n8n builder"

Trigger: Gmail Trigger on unread mail to 3pl.reports.receiver@goessor.com
Input: XLSX attachments (Vitaquest 3PL inventory reports; e.g. "Branded INV May 2026.xlsx")
Transform: Extract columns Material, Material Description, Batch, Opening Stock,
  Total Receipt Qties, Total Issue Quantities, Closing Stock, BUn
Output: Snowflake SANDBOX.LOGISTICS.VITAQUEST_INVENTORY (staging + snapshot pattern)

Constraints:
- Do not modify existing workflows
- Leave inactive until I test
- Use Gmail account 7 and Snowflake account 4 if they exist
- Mark email as read after successful load; leave unread on Snowflake error

-->
