# Agentforce for Marketing — Solutions Template

An org-agnostic, deployable blueprint for building a custom Agentforce Marketing Agent on Salesforce. Combines Data Cloud, Marketing Cloud Growth/Advanced, and CRM into a single conversational interface powered by AI grounding.

## What This Template Delivers

A fully custom Agentforce agent with three core capabilities:

| Capability | Description | Functions |
|---|---|---|
| **Data Exploration** | Natural language queries across Data Cloud DMOs/DLOs and CRM objects | Discover Schema, Generate SQL, Query DMOs, Query CRM |
| **Campaign & Journey Creation** | Create segments targeting Unified Individuals, trigger Broadcast Flows | Create Segment, Trigger Broadcast Flow |
| **Performance Analysis** | Unified paid (Data Cloud) + owned (CRM) channel analytics with executive reports | Analyze Performance, Generate Report |

## Architecture

```
┌────────────────────────────────────────────────────┐
│              Marketing Copilot Bot                  │
├────────────────────────────────────────────────────┤
│          Marketing Intelligence Planner (ReAct)     │
├──────────────┬─────────────────┬───────────────────┤
│  Plugin 1    │    Plugin 2     │     Plugin 3       │
│  Data        │  Campaign &     │   Performance      │
│  Explorer    │  Journey Ops    │   Analyst           │
├──────────────┴─────────────────┴───────────────────┤
│           AI GROUNDING LAYER (v2)                   │
│  Data Prism · Metadata Studio · MI Semantic Model   │
├────────────────────────────────────────────────────┤
│              DATA CLOUD + CRM                       │
│  DMOs · DLOs · CRM Objects · Data Graphs            │
└────────────────────────────────────────────────────┘
```

## Deployable Components

| Category | Count | Details |
|---|---|---|
| Apex Services | 6 | Schema, Data Explorer, CRM Query, Campaign, Broadcast Flow, Performance |
| Prompt Templates | 2 | NL-to-SQL (schema-aware), Performance Report |
| GenAI Functions | 8 | Full mapping layer between AI planner and services |
| GenAI Plugins | 3 | Data Explorer, Campaign Ops, Performance Analyst |
| GenAI Planner | 1 | ReAct-based with variable persistence |
| Bot + BotVersion | 2 | Marketing Copilot agent |
| Permission Set | 1 | Marketing Copilot Access |
| CSP Trusted Site | 1 | GitHub images |
| **Total** | **24** | |

## AI Grounding Layer

Three layers ensure the agent understands data in business terms:

1. **Einstein Data Prism** — Auto-scans Data Cloud metadata, generates semantic descriptions, stores in vector DB
2. **Metadata Studio** — Admin UI for reviewing and refining auto-generated descriptions
3. **MI Semantic Data Model** — Pre-built by MI Data Pipeline with metric definitions (CPC, CTR, ROAS)

## Presentations

Two branded HTML presentations are included for sharing and stakeholder review:

| File | Format | Description |
|---|---|---|
| [`agentforce-marketing-scrollable.html`](agentforce-marketing-scrollable.html) | Scrollable page | Long-form one-pager with animated sections and scroll-triggered reveals |
| [`agentforce-marketing-interactive.html`](agentforce-marketing-interactive.html) | Interactive deck | 10-slide full-screen presentation with keyboard/scroll/touch navigation |

Open either file in a browser to view.

## Prerequisites

- Salesforce org with Marketing Intelligence license
- Data Cloud enabled and provisioned
- Agentforce features enabled
- Einstein Data Prism enabled
- MC Growth/Advanced provisioned
- Tableau/CRM Analytics license
- Admin permissions on org

## Implementation Phases

| Phase | Name | Time | Type |
|---|---|---|---|
| 1 | MI Foundation (EMI Mock, pipelines, enrichment) | ~45 min | Manual + Automated |
| 1.5 | AI Grounding Layer (Data Prism, Metadata Studio, SDM) | ~15 min | Manual |
| 2A | NL-to-SQL Architecture Design | — | Architecture |
| 2 | Apex Services & Prompt Templates | ~45 min | Automated |
| 3 | GenAI Functions | ~20 min | Automated |
| 4 | GenAI Plugins | ~10 min | Automated |
| 5 | Planner, Bot & Permissions | ~15 min | Automated |
| 6 | Deployment Sequence | ~10 min | Automated |
| 7 | Default Paid Media Agent | ~8 min | Manual |
| 8 | Validation, Quality & Demo Script | ~20 min | Manual |

**Estimated Total**: ~2.5–3.5 hours

## Demo Script

**Act 1 — Discovery**: "Show me what marketing data we have" → "How many Meta Ads campaigns?" → "What does our CRM data look like?"

**Act 2 — Insight**: "Compare all paid channels" → "Highest CPC? Generate a report" → "Unified paid + owned view"

**Act 3 — Action**: "Create a segment for top clickers" → "Trigger a broadcast to that audience" → "Executive summary report"

## Version History

- **v2** — Added AI Grounding Layer (Data Prism, Metadata Studio, SDM), fixed 11 design gaps from v1, added Broadcast Flow trigger, enriched schema discovery with business descriptions
- **v1** — Initial 7-function architecture with NL-to-SQL engine

---

*Built with [MeshMesh](https://meshmesh.io)*
