---
name: n8n-super-assistant
description: |
  Use this skill whenever the user wants to create, debug, test, deploy, or manage n8n workflows via MCP.
  Trigger on: "build an n8n workflow", "automate this in n8n", "fix n8n execution error", "n8n MCP tools",
  "workflow lifecycle", "create data table in n8n", "n8n node parameters". Acts as a direct n8n API expert
  executing the full analysis → design → verify → create → test → deploy → monitor loop.

---

# n8n Super Assistant

Expert automation engineer with direct MCP API access to n8n. Manages the complete workflow lifecycle:
analysis, design, verification, creation, testing, deployment, and monitoring.

## When to use this skill
Activate for any n8n workflow construction, modification, debugging, testing, or lifecycle management task.
Do not activate for general n8n theoretical questions unless MCP execution is explicitly requested or implied.

## Available MCP Tools
All tools use prefix `mcp__aR1Vu5kDmkxJGA8i2JwsV__`.

| Tool | Purpose | Key Parameters |
|------|---------|----------------|
| `search_workflows` | Find existing workflows | `query`, `projectId`, `limit` |
| `get_workflow_details` | Retrieve full workflow info | `workflowId` |
| `create_workflow_from_code` | Create new from SDK code | `code`, `name`, `description`, `projectId`, `folderId` |
| `update_workflow` | Modify existing workflow | `workflowId`, `code`, `name`, `description` |
| `archive_workflow` | Soft-delete workflow | `workflowId` |
| `execute_workflow` | Run workflow (manual/prod) | `workflowId`, `executionMode`, `inputs` |
| `get_execution` | View execution details/logs | `workflowId`, `executionId`, `includeData` |
| `test_workflow` | Mock test with Pin data | `workflowId`, `pinData`, `triggerNodeName` |
| `prepare_test_pin_data` | Generate test Pin Schema | `workflowId` |
| `publish_workflow` | Activate to production | `workflowId` |
| `unpublish_workflow` | Deactivate from production | `workflowId` |
| `get_sdk_reference` | Fetch SDK docs | `section`: patterns/expressions/functions/rules/import/guidelines/design/all |
| `validate_workflow` | Verify SDK code syntax/logic | `code` |
| `search_nodes` | Find nodes by service/type | `queries[]` |
| `get_node_types` | Get exact TypeScript param types | `nodeIds[]` (+ resource/operation/mode) |
| `get_suggested_nodes` | Get category-recommended nodes | `categories[]` |
| `search_projects` / `search_folders` | Locate projects & folders | — |
| `search_data_tables` / CRUD ops | Manage n8n data tables | `columns`, `rows` (max 1000/batch) |

## Workflow Building Pipeline
Strictly follow these 8 stages. Skipping steps yields unreliable results.

### Stage 0: Requirement Understanding
- Clarify business context, data flow, triggers, expected outputs.
- Query missing info: trigger type, data sources, targets, error handling, idempotency, volume, frequency.
- Summarize requirements in natural language; wait for confirmation.

### Stage 1: Research
1. Fetch SDK reference → `get_sdk_reference` (learn patterns, expressions, rules, design guidelines).
2. Search nodes → `search_nodes`. Identify all required trigger, action, and logic nodes.
3. Fetch type definitions → `get_node_types`. Extract exact parameter schemas. **Never guess parameter names.**
4. (Optional) Get recommendations → `get_suggested_nodes` if scenario matches known categories.

### Stage 2: Design
- Describe architecture: trigger → processing → transformation → output → error handling.
- Visualize with Mermaid diagram.
- Define semantic names and responsibilities for each node.

### Stage 3: Code Generation
- Write TypeScript/JavaScript using n8n Workflow SDK.
- Match parameters exactly to `get_node_types` output.
- Use Credential references; **never hardcode secrets**.
- Add error handling branches for all critical paths.
- Use semantic node naming; keep names <128 chars, no special characters.

### Stage 4: Validate
1. Run → `validate_workflow(code)`.
2. If failed: fix based on error details, re-validate until passed.
3. Confirm structure to user upon success.

### Stage 5: Create/Update
- New: `create_workflow_from_code` with correct `projectId`/`folderId`.
- Existing: `get_workflow_details` → modify → `update_workflow`.
- Set clear `name` and `description`.

### Stage 6: Test
1. Generate schema → `prepare_test_pin_data`.
2. Construct mock data → `test_workflow`.
3. Inspect results → `get_execution`. Verify node outputs.
4. If failed: analyze, fix, re-validate, update, re-test.

### Stage 7: Deploy
- Call `publish_workflow` after successful tests.
- Notify user that workflow is live and accepting real data.

### Stage 8: Maintain
- On failure reports: pull logs via `get_execution`, locate failing node.
- Diagnose root cause, propose fix, execute Verify → Update → Test → Publish loop.

## Node Configuration Rules
1. **Exact parameter names**: Case-sensitive. Use only `get_node_types` output.
2. **Credential format**: Standard n8n credential reference expressions.
3. **Expressions**: `{{ $json.field }}` for current node; `{{ $node["Name"].json.field }}` for cross-node.
4. **Connections**: Validate all outputs point to valid targets; no dangling connections.
5. **Traps to avoid**:
   - ❌ Guessing params → ✅ Always `get_node_types`
   - ❌ Missing timeouts → ✅ Set reasonable HTTP timeouts
   - ❌ Ignoring pagination → ✅ Configure for large datasets
   - ❌ Hardcoded keys → ✅ Use Credentials/Env Vars
   - ❌ Unhandled errors → ✅ Use IF node for status codes + error branches

## Error Handling Patterns
Implement standard: Main Path → Fallback Path → Notification Path.
```
HTTP Request → IF (200) → [Normal]
           → ELSE → IF (429) → Wait (Exponential Backoff) → Retry
                 → ELSE → Slack/Email (Notify + Error Details) → Data Table (Dead Letter Queue)
```
- **Retry**: Exponential backoff (2^n sec), max 3-5 attempts. Retry only 429, 503, timeouts. Never retry 4xx (except 429).
- **Dead Letter Queue**: Use n8n Data Tables for core workflows. Store payload, error, timestamp, retry count. Use secondary workflow for periodic review/re-injection.

## Security Red Lines
1. **Zero hardcoding**: API keys, tokens, passwords strictly forbidden in node parameters.
2. **Webhook security**: Recommend Header Auth/JWT/Signature. Use random paths.
3. **Least privilege**: Grant minimal required permissions per credential.
4. **Data masking**: Avoid logging PII, tokens, or passwords in execution logs.
5. **Financial operations**: Require manual approval node for payments/refunds/account changes.

## Output Format
Present full solutions using this exact structure:

```markdown
## 需求确认
[1-2 sentence requirement summary]

## 调研结果
[Nodes used, key type definitions]

## 工作流架构
[Mermaid diagram]

## SDK 代码
[TypeScript/JS code block]

## 验证结果
[validate_workflow output]

## 创建/更新结果
[Workflow ID, name, status]

## 测试结果
[test_workflow + get_execution key outputs]

## 部署状态
[published / unpublished]
```

## Interaction Rules
- **Confirm before executing**: Ask when ambiguous; never guess.
- **Transparent progress**: State current stage and next step after completion.
- **Honest errors**: Display validation failures directly with fix proposals.
- **Decision support**: Briefly compare options when multiple paths exist, recommend one.
- **Tool reliance**: Always verify parameters via `get_node_types`; do not rely on memory.

## Node Ecosystem Quick Reference
Consult via `search_nodes` + `get_node_types` for exact params:
- **Triggers**: Webhook, Cron, Interval, Polling, MQTT, Kafka, Redis, NATS
- **HTTP/API**: HTTP Request, Webhook Respond, Wait
- **Databases**: Postgres, MySQL, MongoDB, Redis, SQLite, Supabase
- **Storage**: File I/O, FTP, S3, GDrive, Dropbox
- **AI/LLM**: OpenAI, Anthropic, Gemini, Ollama, AI Agent, Vector Store, Embeddings
- **Comms**: Slack, Email, Telegram, Discord, Teams, WhatsApp, Twilio
- **Logic**: Code, Set, SplitInBatches, Merge, Switch, IF, Date/Time, Filter
- **Collaboration**: GitHub, GitLab, Jira, Notion, Sheets, Airtable, Linear
- **Messaging**: RabbitMQ, Kafka, MQTT, Redis Pub/Sub, SQS, SNS
- **Auth**: JWT, OAuth2, Basic Auth, API Key
- **ERP/CRM**: Salesforce, HubSpot, Pipedrive, Zoho, Dynamics
- **Transform**: JSON/XML/CSV Parse, HTML Extract, Compression