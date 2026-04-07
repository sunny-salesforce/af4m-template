# Semantic Data Model Research for Agentforce

> Research findings on how the Salesforce Customer 360 Semantic Data Model (SDM) and Tableau Semantics strengthen Agentforce accuracy. The SDM provides a business-language abstraction over Data Cloud's raw tables, enabling AI agents to query through business definitions rather than raw SQL — directly reducing hallucination and improving query accuracy.

## What Is the Semantic Data Model (SDM)?

The Customer 360 Semantic Data Model is a structured framework that sits above Data Cloud's raw DMOs/DLOs. It defines how business data is organized and interpreted — encoding metrics, relationships, calculations, and metadata into a standardized, business-friendly representation.

Tableau Semantics is the **query engine** that powers the SDM. It translates business-level questions into optimized SQL queries against Data Cloud, using the SDM definitions.

### Architecture Flow
```
Business Question → Tableau Semantics Query Engine → SDM Definitions → Optimized SQL → Data Cloud
```

### Key Components
- **Metrics Store**: Centralized definitions for business metrics (e.g., "CPC", "CTR", "Revenue")
- **Composable Models**: Analysts extend federated models while governance is maintained
- **AI Data Modeling**: Natural language model creation, relationship suggestions, auto-formula generation
- **Semantic Learning** (forthcoming): Bridges business logic gaps for improved agent accuracy

## How It Strengthens Agentforce Accuracy

The SDM improves AI agent accuracy through three mechanisms:

### 1. Business Context Grounding
Instead of querying raw tables with cryptic names (e.g., `FacebookAds_Media_Conversion_XXXXX__dll`), agents query through business-defined metrics ("CPC", "impressions by channel"). The semantic layer translates these into the correct SQL — eliminating the need for our NL-to-SQL prompt template to know table/field names.

### 2. Consistent Metric Definitions
When "CPC" is defined once in the SDM as `SUM(Spend) / NULLIF(SUM(Clicks), 0)`, every query — whether from Agentforce, Tableau Next, or CRM reports — returns the same result. No ambiguity, no hallucination about formulas.

### 3. Governance Integration
Semantic models inherit lineage, tagging, and classification metadata from source data. Agents understand data sensitivity and proper usage context automatically.

## Key Metrics

- **SDM Type**: Business Abstraction — Translates raw Data Cloud tables into business-language metrics and dimensions
- **Query Engine**: Tableau Semantics — SQL generator-based approach leveraging underlying data warehouse optimization
- **Impact on NL-to-SQL**: Transformative — Could replace our custom NL-to-SQL with native semantic query APIs
- **MI Integration**: Native — Marketing Intelligence has a pre-built Semantic Data Model viewable in workspace

## Marketing Intelligence Semantic Data Model

Marketing Intelligence includes a **pre-built semantic model** specifically for marketing data. As of Winter '26, users can view this model directly from their MI workspace, seeing how data is structured including relationships between fields and tables.

This pre-built model:
- Harmonizes data from EMI Mock connectors (Meta Ads, Google Ads) into business metrics
- Is used by Tableau Next visualizations in the MI workspace
- Provides the semantic foundation that the Paid Media Optimization Agent already uses
- Our Tableau Next dashboard already references `Marketing_Intelligence_Semantic_Model`

## Relevance to Our Agent Architecture

### Current Approach (Plan v1)
Our 3-layer NL-to-SQL architecture:
1. Schema discovery (raw table/field names)
2. Prompt template generates SQL from schema
3. Execute against Data Cloud Query API

### With Semantic Data Model (Plan v2 Enhancement)
Two possible integration paths:

**Path A — Semantic Query API (Future-Forward)**
If the Semantic Query API is available, the agent could query through the SDM directly — asking for "CPC by campaign" and letting Tableau Semantics generate the SQL. This would replace Layer 2 (our NL-to-SQL prompt template) with a native, more accurate engine.

**Path B — SDM-Enriched Schema Discovery (Practical Now)**
Enrich our schema discovery (Layer 1) with SDM metadata — adding business descriptions, metric definitions, and relationships. This gives the NL-to-SQL prompt template much richer context, improving SQL generation accuracy without requiring the Semantic Query API.

**Recommendation**: Use **Path B** for the demo (immediately practical), note **Path A** as the future-state architecture.

> ℹ️ **Recommendation for Plan v2**: Add the Marketing Intelligence Semantic Data Model as a key data foundation component. Enhance the schema discovery service to include semantic metadata (business descriptions, metric formulas) from the SDM where available. Document Path A (Semantic Query API) as the future-state architecture. The pre-built MI Semantic Model is already created by the Data Pipeline in Phase 1 — we just need to leverage it more deliberately in the agent's schema context.
