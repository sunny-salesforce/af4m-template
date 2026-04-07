# Plan v1 End-to-End Design Review (Complete)

> Complete design review of the Agentforce for Marketing plan (v1). Identifies 11 gaps covering all three new research areas (Data Prism, Metadata Studio, Semantic Data Model) plus architecture, error handling, journey creation, and deployment issues. All findings feed into Plan v2.

## What's Working Well

The v1 plan demonstrates strong foundational architecture:

### NL-to-SQL 3-Layer Design
Schema discovery → prompt template → validated execution with ReAct error recovery. This is robust and org-agnostic.

### Component Hierarchy
Bot → Planner → Plugins → Functions → Apex/Templates follows Agentforce best practices. Variable mappings for schema persistence across turns are correct.

### Plugin Separation
Three plugins (Data Explorer, Campaign Ops, Performance Analyst) map cleanly to the three core capabilities.

## Complete Gap Analysis (11 Items)

| # | Gap | Severity | Category | Fix in v2 |
|---|---|---|---|---|
| 1 | Missing Einstein Data Prism provisioning | Medium | AI Grounding | Add Phase 1.5 for Data Prism + Metadata Studio |
| 2 | No Semantic Data Model integration | Medium | AI Grounding | Enhance schema discovery with SDM metadata |
| 3 | Missing metadata description best practices | Medium | AI Grounding | Add quality checklist for object/field descriptions |
| 4 | PerformanceService uses hardcoded table name | High | Architecture | Refactor to accept table name from planner context |
| 5 | No Journey/Email creation capability | High | Capability Gap | Add CRM Campaign + Flow guidance functions |
| 6 | Missing Broadcast Flow trigger function | High | Capability Gap | Add Apex service for POST /actions/custom/flow/{api} |
| 7 | No owned_summary implementation | Medium | Capability Gap | Add CRM email engagement analysis path |
| 8 | GenAI function count mismatch | Low | Consistency | Reconcile: 7 defined vs 8 claimed |
| 9 | No Data Graph read function | Low | Completeness | Document as manual setup with agent read guidance |
| 10 | Missing NamedCredential documentation | Medium | Deployment | Document UserInfo.getSessionId() requirement |
| 11 | segmentOnApiName inconsistency | Medium | Correctness | UnifiedIndividual__dlm for MC Growth, ssot__Individual__dlm for others |

## New Gaps from Research (1-3)

### Gap 1: Einstein Data Prism (Medium)
Data Prism auto-scans all Data Cloud metadata (schema, relationships, sample values, descriptions), generates missing descriptions, and stores them in a vector database. At runtime, it returns **focused, semantically-matched tables and fields** for any natural language utterance. This would dramatically improve our NL-to-SQL accuracy by pre-filtering schema to only relevant tables — reducing token usage and hallucination.

### Gap 2: Semantic Data Model Integration (Medium)
Marketing Intelligence already creates a pre-built Semantic Data Model that defines metrics like CPC, CTR, and impressions as business-language definitions. Our schema discovery currently returns raw table/field names. Enhancing it with SDM metadata (business descriptions, metric formulas, dimension hierarchies) gives the NL-to-SQL prompt template much richer context. Future-state: the Semantic Query API could replace our prompt template entirely.

### Gap 3: Metadata Description Best Practices (Medium)
Salesforce's own guidance states field/object descriptions are "super powerful in the age of Agentforce." Our plan deploys metadata but includes no guidance on writing high-quality descriptions for GenAI Functions, Plugin Instructions, and Topic definitions. These descriptions directly control planner action selection accuracy.

## Existing Gaps (4-11)

### Gap 4: Hardcoded Table Name (High)
`MarketingPerformanceService.buildAnalysisQuery()` uses placeholder `Marketing_Intelligence_Data__dlm`. Contradicts core principle. Fix: accept table name as input parameter from planner (which already has schema context from variable mapping).

### Gap 5: Missing Journey/Email Creation (High)
Plugin 2 only has segment creation. MC Growth uses Broadcast Flows for dynamic segments. Broadcast Flows can be **triggered via API** (`POST /actions/custom/flow/{flowApiName}`) but must be **created via browser UI**. The agent should: (a) create the segment programmatically, (b) guide the user through Flow setup in browser, (c) trigger the Broadcast Flow via API once created.

### Gap 6: Broadcast Flow Trigger (High)
Need a new Apex service and GenAI function to trigger Broadcast Flows via `POST /actions/custom/flow/{flowApiName}` with runtime parameters. This completes the end-to-end campaign execution path.

### Gap 7: Owned Summary (Medium)
The `owned_summary` analysis type referenced in plugin instructions has no SQL implementation. Add CRM-based email engagement query path.

### Gap 8: Function Count (Low)
Summary says 8 functions but only 7 defined. Adding the Broadcast Flow trigger function resolves this.

### Gap 9: Data Graph (Low)
Architecture discusses Data Graphs but no function accesses them. Document as manual pre-setup with agent read guidance via SOQL.

### Gap 10: NamedCredential (Medium)
All services use `UserInfo.getSessionId()`. Works in user-context but may fail in system context. Document clearly.

### Gap 11: segmentOnApiName (Medium)
Plan uses `ssot__Individual__dlm` but MC Growth requires `UnifiedIndividual__dlm` for flow compatibility. Need conditional logic or clear guidance.

> ⚠️ **Critical Path for v2**: Priorities: (1) Add AI Grounding layer (Data Prism + SDM + Metadata quality), (2) Fix PerformanceService hardcoded table name, (3) Add Broadcast Flow trigger for end-to-end campaigns, (4) Reconcile segmentOnApiName for MC Growth compatibility.
