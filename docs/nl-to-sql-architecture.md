# NL-to-SQL Query Architecture Design

> Design decision for how the Agentforce Marketing Copilot translates natural language questions into accurate Data Cloud SQL and CRM SOQL queries. Uses a 3-layer architecture: runtime schema discovery, schema-aware prompt template for NL→SQL, and validated query execution with error recovery via the ReAct loop.

## The Problem

Data Cloud table names and field names are **org-specific** — they include auto-generated hashes (e.g., `FacebookAds_Media_Conversion_1076212609__dll`). Hardcoding SQL inside Apex would break across orgs. The agent needs to discover schema at runtime and dynamically generate correct queries.

## Key Metrics

- **New Components**: 2
- **Total GenAI Functions**: 8
- **Prompt Templates**: 2
- **ReAct Loop Steps**: 3

## 3-Layer Architecture

### Layer 1 — Schema Discovery
`Discover_Marketing_Schema` (Apex) calls Data Cloud APIs to fetch all DLO/DMO table names, field names, data types, and record counts. Returns a structured text block the planner can reason over.

### Layer 2 — NL-to-SQL Translation
`Generate_Marketing_SQL` (Prompt Template) takes the schema context + user's natural language question and generates valid ANSI SQL. Enforces rules like exact naming, NULLIF for division, proper LIMIT defaults.

### Layer 3 — Validated Execution
`Query_Marketing_DMOs` (Apex) executes the SQL via `POST /ssot/query`. On error, returns the error to the planner so the ReAct loop can self-correct (fix column name, retry).

> ℹ️ **Key Design Choice**: The Schema_Context is stored as a planner variable — it persists across the ReAct loop so follow-up questions reuse the cached schema without re-discovering. This makes multi-turn conversations fast.

### Data Cloud vs CRM Query Paths

| Aspect | Data Cloud (Paid) | CRM (Owned) |
|---|---|---|
| Query Language | ANSI SQL | SOQL |
| Schema Discovery | Required (org-specific names) | Not needed (standard objects) |
| Translation Method | NL→SQL Prompt Template | Planner builds SOQL directly |
| API Endpoint | `POST /ssot/query` | `Database.query()` in Apex |
| Tables | DLOs (__dll) and DMOs (__dlm) | Campaign, Lead, Contact, Opportunity |
| Error Recovery | ReAct retry with corrected SQL | Standard Apex exception handling |

## ReAct Loop Example

**User**: "What's our CPC across Meta Ads campaigns?"

**Step 1 (Reason → Act)**: I need table/field names → Call `Discover_Marketing_Schema`
**Step 1 (Observe)**: Found `FacebookAds_Media_XXX__dll` with `CPC__c`, `Campaign_Name__c`

**Step 2 (Reason → Act)**: Generate SQL from schema → Call `Generate_Marketing_SQL`
**Step 2 (Observe)**: `SELECT Campaign_Name__c, AVG(CPC__c) as avg_cpc FROM FacebookAds_Media_XXX__dll WHERE Marketing_Channel_Name__c = 'Meta Ads' GROUP BY Campaign_Name__c`

**Step 3 (Reason → Act)**: Execute query → Call `Query_Marketing_DMOs`
**Step 3 (Observe)**: 15 rows returned

**Response**: Presents campaign CPC rankings to user

> ⚠️ **Data Graph Limitation**: Real-Time Data Graphs cannot be created via API — must use browser UI. The agent can read existing Data Graph records via SOQL (`SELECT Id, Name FROM DataGraph`) but creation/configuration is a manual step in Phase 1. For real-time personalization use cases, pre-create the Data Graph during foundation setup.
