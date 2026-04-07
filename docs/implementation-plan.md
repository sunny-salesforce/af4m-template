# Agentforce for Marketing — Solutions Template

> **An org-agnostic, deployable blueprint for building a custom Agentforce Marketing Agent on Salesforce**

---

## What This Is

This is a **complete implementation template** for building a custom Agentforce agent that brings AI-powered marketing intelligence directly into your Salesforce org. It combines Data Cloud, Marketing Cloud Growth/Advanced, and CRM data into a single conversational interface — enabling marketers to explore data, launch campaigns, and analyze performance through natural language.

This template is designed to be **org-agnostic**: it does not assume specific object names, table structures, or org configurations. Every component adapts at runtime through dynamic schema discovery and an AI grounding layer that translates raw metadata into business-meaningful context.

## What the Agent Can Do

The agent delivers three core capabilities through a plugin-based architecture:

**Data Exploration** — Ask questions about any Data Cloud or CRM data in natural language. The agent discovers available tables, translates questions into SQL, executes queries, and returns results — all without the user needing to know table names, field API names, or query syntax.

**Campaign & Journey Creation** — Create audience segments targeting Unified Individuals in Data Cloud, then trigger Broadcast Flows to activate those segments through Marketing Cloud Growth channels. The full cycle from "who should we target" to "send the campaign" is handled conversationally.

**Performance Analysis** — Analyze both owned (CRM Campaigns) and paid (Data Cloud marketing data from connectors like Meta Ads, Google Ads) channel performance in a unified view. The agent generates executive-style reports with metrics, top performers, and recommendations.

## What Makes It Accurate

A three-layer **AI Grounding Architecture** ensures the agent understands your data in business terms, not just raw API names:

1. **Einstein Data Prism** — Automatically scans all Data Cloud metadata (tables, fields, relationships, sample values) and generates business-language descriptions stored in a vector database. At query time, it returns the most semantically relevant tables and fields.

2. **Metadata Studio** — An admin-facing UI for reviewing and refining the auto-generated descriptions. Domain experts can add business context that Data Prism alone can't infer.

3. **Marketing Intelligence Semantic Data Model** — A pre-built semantic layer created automatically by the MI Data Pipeline. Defines metrics (CPC, CTR, ROAS), dimensions, and calculations that Tableau Semantics and the agent can use for accurate analytics.

## What's Included

| Category | Count | Components |
|----------|-------|------------|
| Apex Services | 6 | Schema discovery, data explorer, CRM query, campaign/segment, broadcast flow trigger, performance analysis |
| Prompt Templates | 2 | NL-to-SQL translation, performance report generation |
| GenAI Functions | 8 | Full function layer mapping prompts + services to Agentforce |
| GenAI Plugins | 3 | Data Explorer, Campaign Ops, Performance Analyst |
| Planner | 1 | ReAct-based orchestrator with variable mapping |
| Bot | 1 | Marketing Copilot (Employee Agent / Internal Copilot) |
| Permission Set | 1 | Marketing Copilot Access |
| **Total** | **24** | **Deployable metadata components** |

## Who This Is For

- **Solutions Engineers** demonstrating Agentforce + Marketing Intelligence capabilities
- **Architects** evaluating how Agentforce integrates with Data Cloud and MC Growth
- **Partners** building marketing-focused AI agent solutions on Salesforce
- **Admins/Developers** wanting a reference implementation of a custom Agentforce agent

## Prerequisites

- Salesforce org with **Data Cloud**, **Agentforce**, and **Marketing Cloud Growth/Advanced** enabled
- **Marketing Intelligence** add-on with EMI Mock connector access
- **Einstein Generative AI** enabled
- SF CLI authenticated to the target org

## Estimated Deployment Time

~2.5–3.5 hours (mix of automated deployments and manual configuration steps)

---

# Implementation Plan v2

> **Version**: v2 — Adds AI Grounding Layer (Data Prism, Metadata Studio, Semantic Data Model), fixes 11 design gaps from v1 E2E review
> **Scope**: Org-agnostic template for building a custom Agentforce Marketing Agent with three core capabilities: (1) Data Exploration, (2) Campaign & Journey Creation, and (3) Owned + Paid Performance Analysis.
> **Platform**: MC Growth/Advanced (platform-native), Data Cloud + CRM unified data, Agentforce custom agent
> **Data**: EMI Mock sample connectors (Meta Ads, Google Ads) + Marketing Intelligence Semantic Data Model
> **Estimated Total Time**: ~2.5–3.5 hours (automated + manual steps)

---

## Architecture Overview (v2)

```
┌──────────────────────────────────────────────────────────────┐
│                    Marketing Copilot Bot                       │
│              (AgentforceEmployeeAgent / InternalCopilot)       │
├──────────────────────────────────────────────────────────────┤
│                 Marketing Intelligence Planner                 │
│                     (AiCopilot__ReAct)                         │
├──────────┬──────────────────┬────────────────────────────────┤
│ Plugin 1 │    Plugin 2      │         Plugin 3                │
│ Data     │  Campaign &      │    Performance                  │
│ Explorer │  Journey Ops     │    Analyst                      │
├──────────┼──────────────────┼────────────────────────────────┤
│ Functions│  Functions       │  Functions                      │
│ • Discover│ • Create        │  • Analyze Paid                 │
│   Schema │   Segment       │    Performance                  │
│ • Generate│ • Trigger       │  • Analyze Owned                │
│   SQL    │   Broadcast     │    Performance                  │
│ • Query  │   Flow          │  • Generate                     │
│   DMOs   │                 │    Report                       │
│ • Query  │                 │                                 │
│   CRM    │                 │                                 │
└──────────┴──────────────────┴────────────────────────────────┘
          ↓                ↓                    ↓
   [Apex Services]   [Apex Services]      [Apex + Prompt Templates]
          ↓                ↓                    ↓
┌──────────────────────────────────────────────────────────────┐
│              AI GROUNDING LAYER (NEW in v2)                   │
│  ┌─────────────┐  ┌────────────────┐  ┌──────────────────┐  │
│  │ Data Prism  │  │ Metadata Studio│  │ MI Semantic Data  │  │
│  │ (Auto-scan  │  │ (Admin reviews │  │ Model (Business   │  │
│  │  + vector   │  │  and refines   │  │  metrics, dims,   │  │
│  │  DB store)  │  │  descriptions) │  │  relationships)   │  │
│  └─────────────┘  └────────────────┘  └──────────────────┘  │
└──────────────────────────────────────────────────────────────┘
          ↓                ↓                    ↓
┌──────────────────────────────────────────────────────────────┐
│                    DATA CLOUD + CRM                           │
│  DMOs (__dlm) │ DLOs (__dll) │ CRM Objects │ Data Graphs     │
└──────────────────────────────────────────────────────────────┘
```

### v2 Changes from v1
| Area | v1 | v2 |
|------|----|----|
| AI Grounding | None | Data Prism + Metadata Studio + Semantic Data Model |
| Functions | 7 (claimed 8) | 8 (added Trigger Broadcast Flow) |
| Performance Service | Hardcoded table name | Accepts table name from planner context |
| Campaign Plugin | Segment creation only | + Broadcast Flow trigger for e2e campaigns |
| Segment Target DMO | Inconsistent | `UnifiedIndividual__dlm` for MC Growth |
| Schema Discovery | Raw table/field names | Enriched with SDM business descriptions |
| Metadata Quality | Not addressed | Dedicated checklist + best practices |

---

## Phase 1: Foundation — Marketing Intelligence Setup (~45 min)

### 1.1 CSP Trusted Sites (Automated)
Deploy trusted URLs for agent image functionality.

**Metadata**: `force-app/main/default/cspTrustedSites/Github_Images.cspTrustedSite-meta.xml`

```xml
<?xml version="1.0" encoding="UTF-8"?>
<CspTrustedSite xmlns="http://soap.sforce.com/2006/04/metadata">
    <context>All</context>
    <description>GitHub images for Marketing Intelligence Agent</description>
    <endpointUrl>https://*.githubusercontent.com</endpointUrl>
    <isActive>true</isActive>
</CspTrustedSite>
```

**Deploy**: `sf project deploy start --source-dir force-app/main/default/cspTrustedSites --target-org $ORG_ALIAS`

### 1.2 EMI Mock Connector Setup (Manual — 5 min)
**Navigation**: Setup → Quick Find → "Other Connectors"

1. Click **New Connector** → Select **EMI Mock** → Name: `Meta Ads` → Save
2. Click **New Connector** → Select **EMI Mock** → Name: `Google Ads` → Save

**Critical**: Names must be exactly `Meta Ads` and `Google Ads`.

### 1.3 Marketing Intelligence Data Pipeline (Manual — 10 min)
**Navigation**: App Launcher → Marketing Intelligence → Data Management

1. Click **Create New Data Pipeline**
2. Select both EMI Mock connectors
3. Map fields:
   - `ad_id` → Ad ID
   - `campaign_name` → Campaign Name
   - `clicks` → Clicks
   - `impressions` → Impressions
   - `channel_name` → Marketing Channel Name
   - `channel_type` → Marketing Channel Type
   - `date` → Delivery Date Times
4. Validate → Save and Deploy

**Note**: This pipeline automatically creates the **Marketing Intelligence Semantic Data Model** — the business-language abstraction used by Tableau Next and the AI Grounding Layer.

### 1.4 Third-Party Data via Total Connect (Manual — 10 min)
1. Download sample CSV: `curl -L "https://drive.google.com/uc?export=download&id=1SuIsNHT1exRljvn29NgnwV8LvptaDmw3" -o data/criteo_sample_data.csv`
2. Marketing Intelligence → Data Management → Data Pipelines → Create New Pipeline → Total Connect
3. Upload CSV, change Date column to DateTime
4. Map fields (ad_id, campaign_name, channel_name, etc.)
5. Validate → Save and Deploy

### 1.5 Einstein Data Enrichment (Manual — 5 min)
Marketing Intelligence → Data Management → Data Enrichment:
1. Create → Enrich with Einstein
2. Name: `Channel Harmonization`, Dimension: `Marketing Channel Name`
3. Choose Data Normalization → Review & Create

### 1.6 Campaign Pattern Creation (Manual — 5 min)
Marketing Intelligence → Data Management → Patterns:
1. New Pattern → Name: `Campaign Pattern` → Dimension: `Campaign Name`
2. Select campaign, review positions, name: Country, Campaign, Audience, KPI, Start Date, End Date
3. Publish

### 1.7 Tableau Next Visualization (Automated)
Create CPC by Campaign dashboard using CRM Analytics REST API:

```bash
ACCESS_TOKEN=$(sf org display --target-org $ORG_ALIAS --json | jq -r '.result.accessToken')
INSTANCE_URL=$(sf org display --target-org $ORG_ALIAS --json | jq -r '.result.instanceUrl')

curl -s -X POST "${INSTANCE_URL}/services/data/v62.0/wave/dashboards" \
  -H "Authorization: Bearer ${ACCESS_TOKEN}" \
  -H "Content-Type: application/json" \
  -d @dashboards/meta-ads-cpc-visualization.json
```

### 1.8 Marketing Intelligence Goal (Manual — 3 min)
Marketing Intelligence → Goals → Create a Goal:
- Metric: Impressions, Value: 10000, Type: At Least
- Start: Feb 1 2025, End: Dec 31 2025

---

## Phase 1.5: AI Grounding Layer — Data Prism, Metadata Studio & Semantic Model (~15 min) ← NEW

This phase provisions the AI accuracy features that ground our agent in semantically-enriched metadata. It runs after the data pipeline creates the Marketing Intelligence Semantic Data Model.

### 1.5.1 Einstein Data Prism Provisioning (Manual — 5 min)

**What it does**: Scans all Data Cloud metadata (schema, relationships, sample values, descriptions), auto-generates missing descriptions, and stores them in a vector database. At runtime, returns semantically-matched tables/fields for any natural language query.

**Navigation**: Setup → Quick Find → "Einstein Data Prism"

1. Enable Einstein Data Prism for the org
2. Wait for initial metadata scan to complete (~2-5 min)
3. Verify status shows "Active" with scanned object count

**How it helps our agent**: When the NL-to-SQL prompt template receives schema context, Data Prism ensures field/table descriptions are populated — giving the LLM richer context than raw names alone.

### 1.5.2 Metadata Studio Review (Manual — 8 min)

**What it does**: Provides the admin UI to review and refine the auto-generated semantic descriptions on Data Cloud objects and fields.

**Navigation**: Setup → Quick Find → "Metadata Studio"

1. Open Metadata Studio
2. Navigate to the Marketing Intelligence DLOs (Meta Ads, Google Ads data)
3. Review auto-generated descriptions for key fields:
   - `CPC__c` → Should describe: "Cost per click in the ad platform's currency"
   - `Impressions__c` → Should describe: "Number of times the ad was displayed"
   - `Campaign_Name__c` → Should describe: "Name of the ad campaign as defined in the ad platform"
   - `Marketing_Channel_Name__c` → Should describe: "Normalized name of the marketing channel (e.g., Meta Ads, Google Ads)"
4. Refine any descriptions that are vague or missing business context
5. Navigate to DMOs (ssot__Ad__dlm, ssot__Individual__dlm) and review descriptions
6. Pay special attention to relationship descriptions (how tables join)

**Quality Checklist for Descriptions**:
- [ ] Every field used in common queries has a clear business description
- [ ] Metric fields include their formula/calculation method
- [ ] Dimension fields include example values
- [ ] Relationship fields describe how tables join and in what direction
- [ ] No description is empty for fields the agent will query

### 1.5.3 Verify Marketing Intelligence Semantic Data Model (Manual — 2 min)

**Navigation**: Marketing Intelligence → Workspace → View Semantic Data Model (Winter '26+)

1. Verify the Semantic Data Model shows all data pipeline tables
2. Confirm relationships between DLOs and standard DMOs are visible
3. Note the model name for reference: `Marketing_Intelligence_Semantic_Model`

**Architecture Note — Future State (Path A)**: When Salesforce exposes the Semantic Query API for Agentforce, the agent could query directly through the SDM using business terms (e.g., "CPC by campaign") and let Tableau Semantics generate the SQL. This would replace our custom NL-to-SQL prompt template with a native, more accurate engine. For this demo, we use **Path B**: enriching our schema discovery with SDM-derived metadata while maintaining the 3-layer NL-to-SQL architecture.

---

## Phase 2A: NL-to-SQL Translation Architecture (Critical Design)

The core challenge: how does the agent translate "show me Meta Ads CPC by campaign" into a valid SQL query?

The answer is a **3-layer architecture**, now enhanced with AI Grounding:

### Layer 1: Runtime Schema Discovery (Apex → enriched metadata)

Before any query, the agent discovers what data exists on this org. The enhanced service now includes business descriptions from Data Prism alongside raw schema:

1. **Discover_Marketing_Schema** → calls `GET /ssot/data-lake-objects` + `GET /ssot/data-model-objects`
2. Returns structured schema with field names, types, record counts, AND business descriptions

Enhanced output format:
```
AVAILABLE DATA CLOUD TABLES:

--- FacebookAds_Media_Conversion_XXXXX__dll ---
Type: DLO
Records: 5,200
Description: Facebook/Meta Ads media conversion and performance data
Fields:
  - Campaign_Name__c (Text) — Name of the ad campaign as defined in Meta Ads
  - CPC__c (Number) — Cost per click in the ad platform's currency
  - Clicks__c (Number) — Total number of clicks on the ad
  - Impressions__c (Number) — Number of times the ad was displayed
  - Marketing_Channel_Name__c (Text) — Normalized channel name (e.g., "Meta Ads")
```

### Layer 2: Schema-Aware Prompt Template (NL → SQL)

A **GenAI Prompt Template** (`Marketing_NL_to_SQL`) takes the user's question + enriched schema context and generates valid SQL.

### Layer 3: Validated Query Execution (Apex → error recovery)

Executes generated SQL via `POST /ssot/query`. On error, returns the message to the ReAct loop for self-correction.

### How the ReAct Loop Works End-to-End

```
User: "What's our CPC across Meta Ads campaigns?"
  │
  ├─ Step 1 (Reason): I need to find marketing data tables
  ├─ Step 1 (Act):    Call Discover_Marketing_Schema
  ├─ Step 1 (Observe): Found FacebookAds_Media_Conversion_XXX__dll with CPC__c, Campaign_Name__c
  │
  ├─ Step 2 (Reason): I have the schema with business descriptions, now generate SQL
  ├─ Step 2 (Act):    Call Generate_Marketing_SQL with schema + user question
  ├─ Step 2 (Observe): SQL generated using exact table/field names from schema
  │
  ├─ Step 3 (Reason): Execute this query
  ├─ Step 3 (Act):    Call Query_Marketing_DMOs with the SQL
  ├─ Step 3 (Observe): 15 campaigns returned with CPC data
  │
  └─ Step 4 (Reason): Present results
      └─ Response: "Here are your Meta Ads campaigns ranked by CPC: ..."
```

### For CRM Data (SOQL path)
CRM queries follow a simpler path — standard objects have predictable field names:
1. Planner recognizes CRM intent (Campaign, Lead, Contact, Opportunity)
2. Planner constructs SOQL directly
3. **Query_CRM_Marketing_Data** executes the SOQL

### For Data Graphs (Real-Time lookups)
Data Graphs provide sub-second lookups:
1. Must be pre-created via browser UI (no API for creation)
2. Agent can read via SOQL: `SELECT Id, Name FROM DataGraph`
3. For real-time segment evaluation, use the Paid Media Topic's built-in integration

---

## Phase 2: Custom Agent — Apex Services (~45 min)

### 2.1 Data Explorer Service

**File**: `force-app/main/default/classes/MarketingDataExplorerService.cls`

```apex
public with sharing class MarketingDataExplorerService {

    public class QueryDMORequest {
        @InvocableVariable(required=true description='SQL query to run against Data Cloud DMOs')
        public String sqlQuery;
    }

    public class QueryDMOResponse {
        @InvocableVariable public Boolean success;
        @InvocableVariable public String resultData;
        @InvocableVariable public String message;
        @InvocableVariable public Integer recordCount;
    }

    @InvocableMethod(label='Query Data Cloud DMOs' description='Executes SQL queries against Data Cloud Data Model Objects. Always discover schema first before using this function.')
    public static List<QueryDMOResponse> queryDMOs(List<QueryDMORequest> requests) {
        List<QueryDMOResponse> responses = new List<QueryDMOResponse>();
        for (QueryDMORequest req : requests) {
            QueryDMOResponse resp = new QueryDMOResponse();
            try {
                HttpRequest httpReq = new HttpRequest();
                httpReq.setEndpoint(URL.getOrgDomainUrl().toExternalForm() + '/services/data/v65.0/ssot/query');
                httpReq.setMethod('POST');
                httpReq.setHeader('Content-Type', 'application/json');
                httpReq.setHeader('Authorization', 'Bearer ' + UserInfo.getSessionId());
                httpReq.setBody(JSON.serialize(new Map<String, String>{'sql' => req.sqlQuery}));
                
                HttpResponse httpResp = new Http().send(httpReq);
                resp.success = httpResp.getStatusCode() == 200;
                resp.resultData = httpResp.getBody();
                resp.message = resp.success ? 'Query executed successfully' : 'Query failed: ' + httpResp.getStatus() + ' — ' + httpResp.getBody();
                
                if (resp.success) {
                    Map<String, Object> parsed = (Map<String, Object>) JSON.deserializeUntyped(httpResp.getBody());
                    List<Object> data = (List<Object>) parsed.get('data');
                    resp.recordCount = data != null ? data.size() : 0;
                }
            } catch (Exception e) {
                resp.success = false;
                resp.message = e.getMessage();
            }
            responses.add(resp);
        }
        return responses;
    }
}
```

**File**: `force-app/main/default/classes/MarketingDataExplorerService.cls-meta.xml`
```xml
<?xml version="1.0" encoding="UTF-8"?>
<ApexClass xmlns="http://soap.sforce.com/2006/04/metadata">
    <apiVersion>65.0</apiVersion>
    <status>Active</status>
</ApexClass>
```

### 2.2 Schema Discovery Service (Enhanced with Business Descriptions)

This is the foundation of the NL-to-SQL architecture. **v2 enhancement**: includes business descriptions from Data Cloud metadata to enrich the planner's context.

**File**: `force-app/main/default/classes/MarketingSchemaService.cls`

```apex
public with sharing class MarketingSchemaService {

    public class SchemaRequest {
        @InvocableVariable(required=false description='Filter: dlo, dmo, or all. Default: all')
        public String objectType;
        @InvocableVariable(required=false description='Optional keyword filter for table names (e.g. campaign, ad, individual)')
        public String filterKeyword;
    }

    public class SchemaResponse {
        @InvocableVariable public Boolean success;
        @InvocableVariable public String schemaContext;  // Planner-friendly structured schema with business descriptions
        @InvocableVariable public String message;
        @InvocableVariable public Integer tableCount;
    }

    @InvocableMethod(label='Discover Marketing Data Schema' description='Returns structured schema of all available Data Cloud tables and fields with business descriptions for SQL query generation. Always call this FIRST before generating any SQL query.')
    public static List<SchemaResponse> discoverSchema(List<SchemaRequest> requests) {
        List<SchemaResponse> responses = new List<SchemaResponse>();
        for (SchemaRequest req : requests) {
            SchemaResponse resp = new SchemaResponse();
            try {
                String objType = String.isNotBlank(req.objectType) ? req.objectType : 'all';
                List<Map<String, Object>> tables = new List<Map<String, Object>>();
                
                if (objType == 'all' || objType == 'dlo') {
                    tables.addAll(fetchDLOSchema(req.filterKeyword));
                }
                if (objType == 'all' || objType == 'dmo') {
                    tables.addAll(fetchDMOSchema(req.filterKeyword));
                }
                
                resp.schemaContext = formatSchemaForPlanner(tables);
                resp.tableCount = tables.size();
                resp.success = true;
                resp.message = 'Found ' + tables.size() + ' tables with data';
            } catch (Exception e) {
                resp.success = false;
                resp.message = e.getMessage();
            }
            responses.add(resp);
        }
        return responses;
    }
    
    private static List<Map<String, Object>> fetchDLOSchema(String keyword) {
        List<Map<String, Object>> result = new List<Map<String, Object>>();
        HttpRequest httpReq = new HttpRequest();
        httpReq.setEndpoint(URL.getOrgDomainUrl().toExternalForm() + '/services/data/v65.0/ssot/data-lake-objects');
        httpReq.setMethod('GET');
        httpReq.setHeader('Authorization', 'Bearer ' + UserInfo.getSessionId());
        
        HttpResponse httpResp = new Http().send(httpReq);
        if (httpResp.getStatusCode() == 200) {
            Map<String, Object> body = (Map<String, Object>) JSON.deserializeUntyped(httpResp.getBody());
            List<Object> dlos = (List<Object>) body.get('dataLakeObjects');
            if (dlos != null) {
                for (Object dloObj : dlos) {
                    Map<String, Object> dlo = (Map<String, Object>) dloObj;
                    String name = (String) dlo.get('name');
                    Integer totalRecords = dlo.get('totalRecords') != null ? 
                        ((Decimal) dlo.get('totalRecords')).intValue() : 0;
                    
                    if (totalRecords == 0) continue;
                    if (String.isNotBlank(keyword) && !name.toLowerCase().contains(keyword.toLowerCase())) continue;
                    
                    // v2: Include description from metadata (populated by Data Prism)
                    String tableDescription = dlo.get('description') != null ? (String) dlo.get('description') : '';
                    
                    List<String> fieldDescriptions = new List<String>();
                    List<Object> fields = (List<Object>) dlo.get('fields');
                    if (fields != null) {
                        for (Object fObj : fields) {
                            Map<String, Object> f = (Map<String, Object>) fObj;
                            String fieldName = (String) f.get('name');
                            String dataType = (String) f.get('dataType');
                            String fieldDesc = f.get('description') != null ? (String) f.get('description') : '';
                            String fieldLine = fieldName + ' (' + dataType + ')';
                            if (String.isNotBlank(fieldDesc)) {
                                fieldLine += ' — ' + fieldDesc;
                            }
                            fieldDescriptions.add(fieldLine);
                        }
                    }
                    
                    result.add(new Map<String, Object>{
                        'tableName' => name,
                        'tableType' => 'DLO',
                        'tableDescription' => tableDescription,
                        'recordCount' => totalRecords,
                        'fields' => fieldDescriptions
                    });
                }
            }
        }
        return result;
    }
    
    private static List<Map<String, Object>> fetchDMOSchema(String keyword) {
        List<Map<String, Object>> result = new List<Map<String, Object>>();
        HttpRequest httpReq = new HttpRequest();
        httpReq.setEndpoint(URL.getOrgDomainUrl().toExternalForm() + '/services/data/v65.0/ssot/data-model-objects');
        httpReq.setMethod('GET');
        httpReq.setHeader('Authorization', 'Bearer ' + UserInfo.getSessionId());
        
        HttpResponse httpResp = new Http().send(httpReq);
        if (httpResp.getStatusCode() == 200) {
            Map<String, Object> body = (Map<String, Object>) JSON.deserializeUntyped(httpResp.getBody());
            List<Object> dmos = (List<Object>) body.get('dataModelObject');
            if (dmos != null) {
                for (Object dmoObj : dmos) {
                    Map<String, Object> dmo = (Map<String, Object>) dmoObj;
                    String name = (String) dmo.get('name');
                    String category = (String) dmo.get('category');
                    Boolean isEnabled = (Boolean) dmo.get('isEnabled');
                    
                    if (isEnabled != true) continue;
                    if (category == 'UNASSIGNED') continue;
                    if (String.isNotBlank(keyword) && !name.toLowerCase().contains(keyword.toLowerCase())) continue;
                    
                    String tableDescription = dmo.get('description') != null ? (String) dmo.get('description') : '';
                    
                    List<String> fieldDescriptions = new List<String>();
                    List<Object> fields = (List<Object>) dmo.get('fields');
                    if (fields != null) {
                        for (Object fObj : fields) {
                            Map<String, Object> f = (Map<String, Object>) fObj;
                            String fieldName = (String) f.get('name');
                            String dataType = (String) f.get('dataType');
                            String fieldDesc = f.get('description') != null ? (String) f.get('description') : '';
                            String fieldLine = fieldName + ' (' + dataType + ')';
                            if (String.isNotBlank(fieldDesc)) {
                                fieldLine += ' — ' + fieldDesc;
                            }
                            fieldDescriptions.add(fieldLine);
                        }
                    }
                    
                    result.add(new Map<String, Object>{
                        'tableName' => name,
                        'tableType' => 'DMO (' + category + ')',
                        'tableDescription' => tableDescription,
                        'recordCount' => -1,
                        'fields' => fieldDescriptions
                    });
                }
            }
        }
        return result;
    }
    
    private static String formatSchemaForPlanner(List<Map<String, Object>> tables) {
        String result = 'AVAILABLE DATA CLOUD TABLES:\n\n';
        for (Map<String, Object> t : tables) {
            result += '--- ' + (String) t.get('tableName') + ' ---\n';
            result += 'Type: ' + (String) t.get('tableType') + '\n';
            String desc = (String) t.get('tableDescription');
            if (String.isNotBlank(desc)) {
                result += 'Description: ' + desc + '\n';
            }
            Integer records = (Integer) t.get('recordCount');
            if (records >= 0) {
                result += 'Records: ' + records + '\n';
            }
            result += 'Fields:\n';
            List<String> fields = (List<String>) t.get('fields');
            for (String f : fields) {
                result += '  - ' + f + '\n';
            }
            result += '\n';
        }
        return result;
    }
}
```

### 2.3 CRM Query Service

**File**: `force-app/main/default/classes/MarketingCRMQueryService.cls`

```apex
public with sharing class MarketingCRMQueryService {

    public class CRMQueryRequest {
        @InvocableVariable(required=true description='SOQL query for CRM marketing data (Campaign, Lead, Contact, Opportunity, EmailMessage)')
        public String soqlQuery;
    }

    public class CRMQueryResponse {
        @InvocableVariable public Boolean success;
        @InvocableVariable public String resultData;
        @InvocableVariable public String message;
        @InvocableVariable public Integer recordCount;
    }

    @InvocableMethod(label='Query CRM Data' description='Executes SOQL queries against standard CRM objects like Campaign, Lead, Contact, Opportunity, and EmailMessage for owned channel analysis')
    public static List<CRMQueryResponse> queryCRM(List<CRMQueryRequest> requests) {
        List<CRMQueryResponse> responses = new List<CRMQueryResponse>();
        for (CRMQueryRequest req : requests) {
            CRMQueryResponse resp = new CRMQueryResponse();
            try {
                List<sObject> records = Database.query(req.soqlQuery);
                resp.success = true;
                resp.resultData = JSON.serialize(records);
                resp.recordCount = records.size();
                resp.message = 'Query returned ' + records.size() + ' records';
            } catch (Exception e) {
                resp.success = false;
                resp.message = e.getMessage();
            }
            responses.add(resp);
        }
        return responses;
    }
}
```

### 2.4 Campaign & Segment Service

**File**: `force-app/main/default/classes/MarketingCampaignService.cls`

```apex
public with sharing class MarketingCampaignService {

    public class CreateSegmentRequest {
        @InvocableVariable(required=true description='Segment display name')
        public String displayName;
        @InvocableVariable(required=true description='Segment description')
        public String description;
        @InvocableVariable(required=false description='DMO to segment on. Default: UnifiedIndividual__dlm (required for MC Growth/Advanced)')
        public String segmentOnApiName;
        @InvocableVariable(required=false description='Segment type: Waterfall, Dbt, Dynamic. Default: Waterfall')
        public String segmentType;
        @InvocableVariable(required=false description='SQL model for Dbt segments. Must SELECT the primary key of the segment DMO.')
        public String sqlModel;
    }

    public class CreateSegmentResponse {
        @InvocableVariable public Boolean success;
        @InvocableVariable public String segmentId;
        @InvocableVariable public String segmentApiName;
        @InvocableVariable public String message;
    }

    @InvocableMethod(label='Create Data Cloud Segment' description='Creates a segment in Data Cloud for audience targeting. For MC Growth, segments must target UnifiedIndividual__dlm.')
    public static List<CreateSegmentResponse> createSegment(List<CreateSegmentRequest> requests) {
        List<CreateSegmentResponse> responses = new List<CreateSegmentResponse>();
        for (CreateSegmentRequest req : requests) {
            CreateSegmentResponse resp = new CreateSegmentResponse();
            try {
                String segType = String.isNotBlank(req.segmentType) ? req.segmentType : 'Waterfall';
                // v2 fix: Default to UnifiedIndividual__dlm for MC Growth compatibility
                String segOn = String.isNotBlank(req.segmentOnApiName) ? req.segmentOnApiName : 'UnifiedIndividual__dlm';
                String devName = req.displayName.replaceAll('[^a-zA-Z0-9]', '_');
                
                Map<String, Object> body = new Map<String, Object>{
                    'description' => req.description,
                    'displayName' => req.displayName,
                    'developerName' => devName,
                    'segmentOnApiName' => segOn,
                    'segmentType' => segType
                };
                
                if (segType == 'Dbt' && String.isNotBlank(req.sqlModel)) {
                    body.put('includeDbt', new Map<String, Object>{
                        'models' => new List<Map<String, String>>{
                            new Map<String, String>{'name' => 'm1', 'sql' => req.sqlModel}
                        }
                    });
                    body.put('publishSchedule', 'TwentyFour');
                }

                HttpRequest httpReq = new HttpRequest();
                httpReq.setEndpoint(URL.getOrgDomainUrl().toExternalForm() + '/services/data/v65.0/ssot/segments');
                httpReq.setMethod('POST');
                httpReq.setHeader('Content-Type', 'application/json');
                httpReq.setHeader('Authorization', 'Bearer ' + UserInfo.getSessionId());
                httpReq.setBody(JSON.serialize(body));
                
                HttpResponse httpResp = new Http().send(httpReq);
                Map<String, Object> result = (Map<String, Object>) JSON.deserializeUntyped(httpResp.getBody());
                
                resp.success = httpResp.getStatusCode() == 200 || httpResp.getStatusCode() == 201;
                resp.segmentId = (String) result.get('marketSegmentId');
                resp.segmentApiName = (String) result.get('apiName');
                resp.message = resp.success ? 'Segment created: ' + resp.segmentApiName : 'Failed: ' + httpResp.getBody();
            } catch (Exception e) {
                resp.success = false;
                resp.message = e.getMessage();
            }
            responses.add(resp);
        }
        return responses;
    }
}
```

### 2.5 Broadcast Flow Trigger Service ← NEW in v2

**File**: `force-app/main/default/classes/MarketingBroadcastFlowService.cls`

```apex
public with sharing class MarketingBroadcastFlowService {

    public class TriggerFlowRequest {
        @InvocableVariable(required=true description='API name of the Broadcast Flow to trigger')
        public String flowApiName;
        @InvocableVariable(required=false description='JSON string of input parameters for the flow (e.g., {"ZipCode": "90210"})')
        public String inputParametersJson;
    }

    public class TriggerFlowResponse {
        @InvocableVariable public Boolean success;
        @InvocableVariable public String message;
        @InvocableVariable public String flowInterviewId;
    }

    @InvocableMethod(label='Trigger Broadcast Flow' description='Triggers a Broadcast Flow via REST API with optional runtime parameters. The Broadcast Flow must already exist and be activated.')
    public static List<TriggerFlowResponse> triggerBroadcastFlow(List<TriggerFlowRequest> requests) {
        List<TriggerFlowResponse> responses = new List<TriggerFlowResponse>();
        for (TriggerFlowRequest req : requests) {
            TriggerFlowResponse resp = new TriggerFlowResponse();
            try {
                HttpRequest httpReq = new HttpRequest();
                httpReq.setEndpoint(URL.getOrgDomainUrl().toExternalForm() + 
                    '/services/data/v66.0/actions/custom/flow/' + req.flowApiName);
                httpReq.setMethod('POST');
                httpReq.setHeader('Content-Type', 'application/json');
                httpReq.setHeader('Authorization', 'Bearer ' + UserInfo.getSessionId());
                
                // Build inputs from parameters JSON
                Map<String, Object> body = new Map<String, Object>();
                if (String.isNotBlank(req.inputParametersJson)) {
                    Map<String, Object> params = (Map<String, Object>) JSON.deserializeUntyped(req.inputParametersJson);
                    body.put('inputs', new List<Map<String, Object>>{ params });
                } else {
                    body.put('inputs', new List<Map<String, Object>>{ new Map<String, Object>() });
                }
                
                httpReq.setBody(JSON.serialize(body));
                HttpResponse httpResp = new Http().send(httpReq);
                
                resp.success = httpResp.getStatusCode() == 200;
                if (resp.success) {
                    resp.message = 'Broadcast Flow triggered successfully';
                    // Parse response for flow interview ID
                    List<Object> outputs = (List<Object>) JSON.deserializeUntyped(httpResp.getBody());
                    if (!outputs.isEmpty()) {
                        Map<String, Object> output = (Map<String, Object>) outputs[0];
                        resp.flowInterviewId = (String) output.get('flowInterviewId');
                    }
                } else {
                    resp.message = 'Failed: ' + httpResp.getStatus() + ' — ' + httpResp.getBody();
                }
            } catch (Exception e) {
                resp.success = false;
                resp.message = e.getMessage();
            }
            responses.add(resp);
        }
        return responses;
    }
}
```

### 2.6 Performance Analysis Service (v2 — Fixed Hardcoded Table)

**File**: `force-app/main/default/classes/MarketingPerformanceService.cls`

```apex
public with sharing class MarketingPerformanceService {

    public class AnalyzePerformanceRequest {
        @InvocableVariable(required=true description='Analysis type: paid_summary, owned_summary, channel_comparison, time_series')
        public String analysisType;
        @InvocableVariable(required=false description='Channel filter (e.g., Meta Ads, Google Ads)')
        public String channelFilter;
        @InvocableVariable(required=false description='Date range start (YYYY-MM-DD)')
        public String dateStart;
        @InvocableVariable(required=false description='Date range end (YYYY-MM-DD)')
        public String dateEnd;
        @InvocableVariable(required=false description='v2: DLO table name for paid data (discovered from schema). Required for paid_summary, channel_comparison, time_series.')
        public String paidDataTable;
    }

    public class AnalyzePerformanceResponse {
        @InvocableVariable public Boolean success;
        @InvocableVariable public String resultData;
        @InvocableVariable public String message;
        @InvocableVariable public String summary;
    }

    @InvocableMethod(label='Analyze Marketing Performance' description='Analyzes paid and owned marketing performance. For paid analysis, requires the DLO table name from schema discovery. For owned analysis, queries CRM objects.')
    public static List<AnalyzePerformanceResponse> analyzePerformance(List<AnalyzePerformanceRequest> requests) {
        List<AnalyzePerformanceResponse> responses = new List<AnalyzePerformanceResponse>();
        for (AnalyzePerformanceRequest req : requests) {
            AnalyzePerformanceResponse resp = new AnalyzePerformanceResponse();
            try {
                if (req.analysisType == 'owned_summary') {
                    // v2: CRM-based owned channel analysis
                    resp.resultData = queryOwnedPerformance(req);
                    resp.success = true;
                    resp.message = 'Owned channel analysis complete';
                } else {
                    // Paid channel analysis via Data Cloud
                    if (String.isBlank(req.paidDataTable)) {
                        resp.success = false;
                        resp.message = 'ERROR: paidDataTable is required. Call Discover_Marketing_Schema first to find the correct DLO table name for paid media data.';
                    } else {
                        String sql = buildAnalysisQuery(req);
                        HttpRequest httpReq = new HttpRequest();
                        httpReq.setEndpoint(URL.getOrgDomainUrl().toExternalForm() + '/services/data/v65.0/ssot/query');
                        httpReq.setMethod('POST');
                        httpReq.setHeader('Content-Type', 'application/json');
                        httpReq.setHeader('Authorization', 'Bearer ' + UserInfo.getSessionId());
                        httpReq.setBody(JSON.serialize(new Map<String, String>{'sql' => sql}));
                        
                        HttpResponse httpResp = new Http().send(httpReq);
                        resp.success = httpResp.getStatusCode() == 200;
                        resp.resultData = httpResp.getBody();
                        resp.message = resp.success ? 'Analysis complete' : 'Failed: ' + httpResp.getStatus();
                    }
                }
            } catch (Exception e) {
                resp.success = false;
                resp.message = e.getMessage();
            }
            responses.add(resp);
        }
        return responses;
    }

    private static String buildAnalysisQuery(AnalyzePerformanceRequest req) {
        // v2: Uses the dynamically-provided table name instead of hardcoded value
        String baseTable = req.paidDataTable;
        
        switch on req.analysisType {
            when 'paid_summary' {
                return 'SELECT Marketing_Channel_Name__c, ' +
                       'SUM(Clicks__c) as total_clicks, ' +
                       'SUM(Impressions__c) as total_impressions, ' +
                       'AVG(CPC__c) as avg_cpc, ' +
                       'SUM(Clicks__c) * 100.0 / NULLIF(SUM(Impressions__c), 0) as ctr ' +
                       'FROM ' + baseTable + ' ' +
                       (String.isNotBlank(req.channelFilter) ? 'WHERE Marketing_Channel_Name__c = \'' + String.escapeSingleQuotes(req.channelFilter) + '\' ' : '') +
                       'GROUP BY Marketing_Channel_Name__c ORDER BY total_clicks DESC';
            }
            when 'channel_comparison' {
                return 'SELECT Marketing_Channel_Name__c, Marketing_Channel_Type__c, ' +
                       'SUM(Clicks__c) as total_clicks, ' +
                       'SUM(Impressions__c) as total_impressions, ' +
                       'AVG(CPC__c) as avg_cpc, ' +
                       'SUM(Clicks__c) * 100.0 / NULLIF(SUM(Impressions__c), 0) as ctr, ' +
                       'COUNT(*) as data_points ' +
                       'FROM ' + baseTable + ' ' +
                       'GROUP BY Marketing_Channel_Name__c, Marketing_Channel_Type__c';
            }
            when 'time_series' {
                return 'SELECT Delivery_Date_Times__c, ' +
                       'SUM(Clicks__c) as daily_clicks, ' +
                       'SUM(Impressions__c) as daily_impressions, ' +
                       'AVG(CPC__c) as daily_avg_cpc ' +
                       'FROM ' + baseTable + ' ' +
                       (String.isNotBlank(req.channelFilter) ? 'WHERE Marketing_Channel_Name__c = \'' + String.escapeSingleQuotes(req.channelFilter) + '\' ' : '') +
                       'GROUP BY Delivery_Date_Times__c ORDER BY Delivery_Date_Times__c DESC';
            }
            when else {
                return 'SELECT * FROM ' + baseTable + ' LIMIT 20';
            }
        }
    }
    
    private static String queryOwnedPerformance(AnalyzePerformanceRequest req) {
        // v2: CRM-based owned channel analysis
        String soql = 'SELECT Name, Status, Type, NumberOfLeads, NumberOfConvertedLeads, ' +
                       'NumberOfResponses, NumberOfOpportunities, AmountAllOpportunities, ' +
                       'StartDate, EndDate ' +
                       'FROM Campaign WHERE IsActive = true ' +
                       'ORDER BY StartDate DESC LIMIT 50';
        List<sObject> campaigns = Database.query(soql);
        return JSON.serialize(campaigns);
    }
}
```

### 2.7 NL-to-SQL Prompt Template

**File**: `force-app/main/default/genAiPromptTemplates/Marketing_NL_to_SQL.genAiPromptTemplate-meta.xml`

```xml
<?xml version="1.0" encoding="UTF-8"?>
<GenAiPromptTemplate xmlns="http://soap.sforce.com/2006/04/metadata">
    <activeVersionIdentifier>v1</activeVersionIdentifier>
    <description>Translates natural language marketing questions into valid Data Cloud SQL queries using discovered schema with business descriptions</description>
    <developerName>Marketing_NL_to_SQL</developerName>
    <masterLabel>Marketing NL to SQL</masterLabel>
    <templateVersions>
        <content>You are a Salesforce Data Cloud SQL expert. Your job is to translate the user's natural language question into a valid ANSI SQL query that will run against the Data Cloud Query API.

## AVAILABLE SCHEMA (with business descriptions)
{!$Input:Schema_Context}

## USER QUESTION
{!$Input:User_Question}

## SQL RULES FOR DATA CLOUD
1. Use EXACT table names from the schema above (including __dlm or __dll suffixes)
2. Use EXACT field names from the schema above (including __c suffixes)
3. DMO tables: prefix ssot__ and suffix __dlm (e.g., ssot__Individual__dlm)
4. DLO tables: suffix __dll (e.g., FacebookAds_Media_Conversion_XXXX__dll)
5. All custom fields end in __c (e.g., Campaign_Name__c, ssot__FirstName__c)
6. Use the business descriptions in the schema to understand what each field contains
7. Match the user's business terminology to the field descriptions, not just field names

## SUPPORTED SQL FEATURES
- SELECT, FROM, WHERE, GROUP BY, HAVING, ORDER BY, LIMIT
- JOIN, LEFT JOIN, INNER JOIN (between DMOs and DLOs)
- Aggregates: COUNT(*), SUM(), AVG(), MIN(), MAX()
- Window functions: ROW_NUMBER() OVER (PARTITION BY ... ORDER BY ...)
- CASE WHEN ... THEN ... ELSE ... END
- UNION / UNION ALL
- Subqueries in WHERE and FROM clauses
- NULLIF() for safe division (ALWAYS use to avoid divide-by-zero)
- String functions: SUBSTRING(), POSITION(), CONCAT()

## OUTPUT RULES
- Return ONLY the SQL query — no explanation, no markdown formatting
- Default LIMIT 100 unless user asks for more
- For "top N" or "best N" requests: ORDER BY ... DESC LIMIT N
- For "worst N" or "bottom N" requests: ORDER BY ... ASC LIMIT N
- For percentage calculations: (value * 100.0) / NULLIF(total, 0)
- For averages across groups: always use GROUP BY with the dimension
- Alias aggregated columns with meaningful names (e.g., avg_cpc, total_clicks)</content>
        <inputs>
            <apiName>Schema_Context</apiName>
            <definition>primitive://String</definition>
            <masterLabel>Schema Context</masterLabel>
            <referenceName>Input:Schema_Context</referenceName>
            <required>true</required>
        </inputs>
        <inputs>
            <apiName>User_Question</apiName>
            <definition>primitive://String</definition>
            <masterLabel>User Question</masterLabel>
            <referenceName>Input:User_Question</referenceName>
            <required>true</required>
        </inputs>
        <primaryModel>sfdc_ai__DefaultOpenAIGPT4OmniMini</primaryModel>
        <status>Published</status>
        <versionIdentifier>v1</versionIdentifier>
    </templateVersions>
    <type>einstein_gpt__flex</type>
    <visibility>Global</visibility>
</GenAiPromptTemplate>
```

### 2.8 Report Generation Prompt Template

**File**: `force-app/main/default/genAiPromptTemplates/Marketing_Performance_Report.genAiPromptTemplate-meta.xml`

```xml
<?xml version="1.0" encoding="UTF-8"?>
<GenAiPromptTemplate xmlns="http://soap.sforce.com/2006/04/metadata">
    <activeVersionIdentifier>v1</activeVersionIdentifier>
    <description>Generates marketing performance reports from raw data</description>
    <developerName>Marketing_Performance_Report</developerName>
    <masterLabel>Marketing Performance Report</masterLabel>
    <templateVersions>
        <content>You are a marketing analytics expert. Analyze the following marketing performance data and generate a concise executive report.

Data:
{!$Input:Performance_Data}

Analysis Request: {!$Input:Report_Type}

Requirements:
- Identify top and bottom performing channels/campaigns
- Calculate key metrics (CTR, CPC trends, impression share)
- Highlight anomalies or significant changes
- Provide 3 actionable recommendations
- Use a professional, data-driven tone

Format the response as:
## Executive Summary
[2-3 sentence overview]

## Key Metrics
[Metrics in tabular format]

## Top Performers
[Top 3 with reasons]

## Areas of Concern
[Bottom 3 with impact]

## Recommendations
[3 numbered actionable items]</content>
        <inputs>
            <apiName>Performance_Data</apiName>
            <definition>primitive://String</definition>
            <masterLabel>Performance Data</masterLabel>
            <referenceName>Input:Performance_Data</referenceName>
            <required>true</required>
        </inputs>
        <inputs>
            <apiName>Report_Type</apiName>
            <definition>primitive://String</definition>
            <masterLabel>Report Type</masterLabel>
            <referenceName>Input:Report_Type</referenceName>
            <required>true</required>
        </inputs>
        <primaryModel>sfdc_ai__DefaultOpenAIGPT4OmniMini</primaryModel>
        <status>Published</status>
        <versionIdentifier>v1</versionIdentifier>
    </templateVersions>
    <type>einstein_gpt__flex</type>
    <visibility>Global</visibility>
</GenAiPromptTemplate>
```

---

## Phase 3: Custom Agent — GenAI Functions (8 total) (~20 min)

### 3.1 Discover Schema Function

**File**: `force-app/main/default/genAiFunctions/Discover_Marketing_Schema/Discover_Marketing_Schema.genAiFunction-meta.xml`

```xml
<?xml version="1.0" encoding="UTF-8"?>
<GenAiFunction xmlns="http://soap.sforce.com/2006/04/metadata">
    <description>Discovers all available Data Cloud tables (DLOs and DMOs) with their field names, types, business descriptions, and record counts. Returns a structured schema context for SQL query generation. Use this FIRST before writing any query.</description>
    <invocationTarget>MarketingSchemaService</invocationTarget>
    <invocationTargetType>apex</invocationTargetType>
    <isConfirmationRequired>false</isConfirmationRequired>
    <isIncludeInProgressIndicator>true</isIncludeInProgressIndicator>
    <mappingAttributes>
        <label>Object Type Filter (dlo, dmo, or all)</label>
        <name>input_objectType</name>
        <parameterName>objectType</parameterName>
        <parameterType>input</parameterType>
    </mappingAttributes>
    <mappingAttributes>
        <label>Keyword Filter</label>
        <name>input_filterKeyword</name>
        <parameterName>filterKeyword</parameterName>
        <parameterType>input</parameterType>
    </mappingAttributes>
    <mappingAttributes>
        <label>Schema Context (tables, fields, types, descriptions)</label>
        <name>output_schemaContext</name>
        <parameterName>schemaContext</parameterName>
        <parameterType>output</parameterType>
    </mappingAttributes>
    <mappingAttributes>
        <label>Table Count</label>
        <name>output_tableCount</name>
        <parameterName>tableCount</parameterName>
        <parameterType>output</parameterType>
    </mappingAttributes>
    <masterLabel>Discover Marketing Data Schema</masterLabel>
    <progressIndicatorMessage>Discovering available marketing data tables and fields...</progressIndicatorMessage>
</GenAiFunction>
```

### 3.2 Generate SQL Function

**File**: `force-app/main/default/genAiFunctions/Generate_Marketing_SQL/Generate_Marketing_SQL.genAiFunction-meta.xml`

```xml
<?xml version="1.0" encoding="UTF-8"?>
<GenAiFunction xmlns="http://soap.sforce.com/2006/04/metadata">
    <description>Translates a natural language marketing question into valid Data Cloud SQL using the discovered schema context. Takes schema from Discover_Marketing_Schema and a user question, returns executable SQL.</description>
    <invocationTarget>Marketing_NL_to_SQL</invocationTarget>
    <invocationTargetType>generatePromptResponse</invocationTargetType>
    <isConfirmationRequired>false</isConfirmationRequired>
    <isIncludeInProgressIndicator>true</isIncludeInProgressIndicator>
    <mappingAttributes>
        <label>Schema Context</label>
        <name>input_schemaContext</name>
        <parameterName>Schema_Context</parameterName>
        <parameterType>input</parameterType>
    </mappingAttributes>
    <mappingAttributes>
        <label>User Question</label>
        <name>input_userQuestion</name>
        <parameterName>User_Question</parameterName>
        <parameterType>input</parameterType>
    </mappingAttributes>
    <masterLabel>Generate Marketing SQL</masterLabel>
    <progressIndicatorMessage>Translating your question into a data query...</progressIndicatorMessage>
</GenAiFunction>
```

### 3.3 Query DMOs Function

**File**: `force-app/main/default/genAiFunctions/Query_Marketing_DMOs/Query_Marketing_DMOs.genAiFunction-meta.xml`

```xml
<?xml version="1.0" encoding="UTF-8"?>
<GenAiFunction xmlns="http://soap.sforce.com/2006/04/metadata">
    <description>Executes SQL queries against Data Cloud to explore marketing data including campaigns, ads, impressions, clicks, and conversions</description>
    <invocationTarget>MarketingDataExplorerService</invocationTarget>
    <invocationTargetType>apex</invocationTargetType>
    <isConfirmationRequired>false</isConfirmationRequired>
    <isIncludeInProgressIndicator>true</isIncludeInProgressIndicator>
    <mappingAttributes>
        <label>SQL Query</label>
        <name>input_sqlQuery</name>
        <parameterName>sqlQuery</parameterName>
        <parameterType>input</parameterType>
    </mappingAttributes>
    <mappingAttributes>
        <label>Query Results</label>
        <name>output_resultData</name>
        <parameterName>resultData</parameterName>
        <parameterType>output</parameterType>
    </mappingAttributes>
    <mappingAttributes>
        <label>Record Count</label>
        <name>output_recordCount</name>
        <parameterName>recordCount</parameterName>
        <parameterType>output</parameterType>
    </mappingAttributes>
    <mappingAttributes>
        <label>Status Message</label>
        <name>output_message</name>
        <parameterName>message</parameterName>
        <parameterType>output</parameterType>
    </mappingAttributes>
    <masterLabel>Query Marketing DMOs</masterLabel>
    <progressIndicatorMessage>Querying Data Cloud marketing data...</progressIndicatorMessage>
</GenAiFunction>
```

### 3.4 Query CRM Data Function

**File**: `force-app/main/default/genAiFunctions/Query_CRM_Marketing_Data/Query_CRM_Marketing_Data.genAiFunction-meta.xml`

```xml
<?xml version="1.0" encoding="UTF-8"?>
<GenAiFunction xmlns="http://soap.sforce.com/2006/04/metadata">
    <description>Queries CRM objects like Campaign, Lead, Contact, Opportunity, and EmailMessage using SOQL for owned channel analysis</description>
    <invocationTarget>MarketingCRMQueryService</invocationTarget>
    <invocationTargetType>apex</invocationTargetType>
    <isConfirmationRequired>false</isConfirmationRequired>
    <isIncludeInProgressIndicator>true</isIncludeInProgressIndicator>
    <mappingAttributes>
        <label>SOQL Query</label>
        <name>input_soqlQuery</name>
        <parameterName>soqlQuery</parameterName>
        <parameterType>input</parameterType>
    </mappingAttributes>
    <mappingAttributes>
        <label>CRM Results</label>
        <name>output_resultData</name>
        <parameterName>resultData</parameterName>
        <parameterType>output</parameterType>
    </mappingAttributes>
    <mappingAttributes>
        <label>Record Count</label>
        <name>output_recordCount</name>
        <parameterName>recordCount</parameterName>
        <parameterType>output</parameterType>
    </mappingAttributes>
    <masterLabel>Query CRM Marketing Data</masterLabel>
    <progressIndicatorMessage>Querying CRM marketing data...</progressIndicatorMessage>
</GenAiFunction>
```

### 3.5 Create Segment Function

**File**: `force-app/main/default/genAiFunctions/Create_Marketing_Segment/Create_Marketing_Segment.genAiFunction-meta.xml`

```xml
<?xml version="1.0" encoding="UTF-8"?>
<GenAiFunction xmlns="http://soap.sforce.com/2006/04/metadata">
    <description>Creates a Data Cloud segment for audience targeting. For MC Growth/Advanced, segments automatically target UnifiedIndividual__dlm. Supports Waterfall, Dbt (SQL), and Dynamic segment types.</description>
    <invocationTarget>MarketingCampaignService</invocationTarget>
    <invocationTargetType>apex</invocationTargetType>
    <isConfirmationRequired>true</isConfirmationRequired>
    <isIncludeInProgressIndicator>true</isIncludeInProgressIndicator>
    <mappingAttributes>
        <label>Segment Name</label>
        <name>input_displayName</name>
        <parameterName>displayName</parameterName>
        <parameterType>input</parameterType>
    </mappingAttributes>
    <mappingAttributes>
        <label>Segment Description</label>
        <name>input_description</name>
        <parameterName>description</parameterName>
        <parameterType>input</parameterType>
    </mappingAttributes>
    <mappingAttributes>
        <label>Segment Type</label>
        <name>input_segmentType</name>
        <parameterName>segmentType</parameterName>
        <parameterType>input</parameterType>
    </mappingAttributes>
    <mappingAttributes>
        <label>SQL Model</label>
        <name>input_sqlModel</name>
        <parameterName>sqlModel</parameterName>
        <parameterType>input</parameterType>
    </mappingAttributes>
    <mappingAttributes>
        <label>Segment ID</label>
        <name>output_segmentId</name>
        <parameterName>segmentId</parameterName>
        <parameterType>output</parameterType>
    </mappingAttributes>
    <mappingAttributes>
        <label>Segment API Name</label>
        <name>output_segmentApiName</name>
        <parameterName>segmentApiName</parameterName>
        <parameterType>output</parameterType>
    </mappingAttributes>
    <masterLabel>Create Marketing Segment</masterLabel>
    <progressIndicatorMessage>Creating audience segment in Data Cloud...</progressIndicatorMessage>
</GenAiFunction>
```

### 3.6 Trigger Broadcast Flow Function ← NEW in v2

**File**: `force-app/main/default/genAiFunctions/Trigger_Broadcast_Flow/Trigger_Broadcast_Flow.genAiFunction-meta.xml`

```xml
<?xml version="1.0" encoding="UTF-8"?>
<GenAiFunction xmlns="http://soap.sforce.com/2006/04/metadata">
    <description>Triggers an existing Broadcast Flow to send communications to a Dynamic Segment. The Broadcast Flow must already be created and activated in Flow Builder. Accepts optional runtime parameters for the Dynamic Segment.</description>
    <invocationTarget>MarketingBroadcastFlowService</invocationTarget>
    <invocationTargetType>apex</invocationTargetType>
    <isConfirmationRequired>true</isConfirmationRequired>
    <isIncludeInProgressIndicator>true</isIncludeInProgressIndicator>
    <mappingAttributes>
        <label>Flow API Name</label>
        <name>input_flowApiName</name>
        <parameterName>flowApiName</parameterName>
        <parameterType>input</parameterType>
    </mappingAttributes>
    <mappingAttributes>
        <label>Input Parameters (JSON)</label>
        <name>input_inputParametersJson</name>
        <parameterName>inputParametersJson</parameterName>
        <parameterType>input</parameterType>
    </mappingAttributes>
    <mappingAttributes>
        <label>Status Message</label>
        <name>output_message</name>
        <parameterName>message</parameterName>
        <parameterType>output</parameterType>
    </mappingAttributes>
    <masterLabel>Trigger Broadcast Flow</masterLabel>
    <progressIndicatorMessage>Triggering broadcast flow to send communications...</progressIndicatorMessage>
</GenAiFunction>
```

### 3.7 Analyze Performance Function

**File**: `force-app/main/default/genAiFunctions/Analyze_Marketing_Performance/Analyze_Marketing_Performance.genAiFunction-meta.xml`

```xml
<?xml version="1.0" encoding="UTF-8"?>
<GenAiFunction xmlns="http://soap.sforce.com/2006/04/metadata">
    <description>Analyzes marketing performance across paid and owned channels. For paid analysis, requires the DLO table name from schema discovery. Supports paid_summary, owned_summary, channel_comparison, and time_series analysis types.</description>
    <invocationTarget>MarketingPerformanceService</invocationTarget>
    <invocationTargetType>apex</invocationTargetType>
    <isConfirmationRequired>false</isConfirmationRequired>
    <isIncludeInProgressIndicator>true</isIncludeInProgressIndicator>
    <mappingAttributes>
        <label>Analysis Type</label>
        <name>input_analysisType</name>
        <parameterName>analysisType</parameterName>
        <parameterType>input</parameterType>
    </mappingAttributes>
    <mappingAttributes>
        <label>Channel Filter</label>
        <name>input_channelFilter</name>
        <parameterName>channelFilter</parameterName>
        <parameterType>input</parameterType>
    </mappingAttributes>
    <mappingAttributes>
        <label>Date Start</label>
        <name>input_dateStart</name>
        <parameterName>dateStart</parameterName>
        <parameterType>input</parameterType>
    </mappingAttributes>
    <mappingAttributes>
        <label>Date End</label>
        <name>input_dateEnd</name>
        <parameterName>dateEnd</parameterName>
        <parameterType>input</parameterType>
    </mappingAttributes>
    <mappingAttributes>
        <label>Paid Data Table Name (from schema discovery)</label>
        <name>input_paidDataTable</name>
        <parameterName>paidDataTable</parameterName>
        <parameterType>input</parameterType>
    </mappingAttributes>
    <mappingAttributes>
        <label>Performance Data</label>
        <name>output_resultData</name>
        <parameterName>resultData</parameterName>
        <parameterType>output</parameterType>
    </mappingAttributes>
    <mappingAttributes>
        <label>Analysis Summary</label>
        <name>output_summary</name>
        <parameterName>summary</parameterName>
        <parameterType>output</parameterType>
    </mappingAttributes>
    <masterLabel>Analyze Marketing Performance</masterLabel>
    <progressIndicatorMessage>Analyzing marketing performance data...</progressIndicatorMessage>
</GenAiFunction>
```

### 3.8 Generate Report Function

**File**: `force-app/main/default/genAiFunctions/Generate_Marketing_Report/Generate_Marketing_Report.genAiFunction-meta.xml`

```xml
<?xml version="1.0" encoding="UTF-8"?>
<GenAiFunction xmlns="http://soap.sforce.com/2006/04/metadata">
    <description>Generates an executive marketing performance report from raw data using AI analysis</description>
    <invocationTarget>Marketing_Performance_Report</invocationTarget>
    <invocationTargetType>generatePromptResponse</invocationTargetType>
    <isConfirmationRequired>false</isConfirmationRequired>
    <isIncludeInProgressIndicator>true</isIncludeInProgressIndicator>
    <mappingAttributes>
        <label>Performance Data</label>
        <name>input_performanceData</name>
        <parameterName>Performance_Data</parameterName>
        <parameterType>input</parameterType>
    </mappingAttributes>
    <mappingAttributes>
        <label>Report Type</label>
        <name>input_reportType</name>
        <parameterName>Report_Type</parameterName>
        <parameterType>input</parameterType>
    </mappingAttributes>
    <masterLabel>Generate Marketing Report</masterLabel>
    <progressIndicatorMessage>Generating your marketing performance report...</progressIndicatorMessage>
</GenAiFunction>
```

---

## Phase 4: Custom Agent — Plugins (~10 min)

### 4.1 Data Explorer Plugin

**File**: `force-app/main/default/genAiPlugins/Marketing_Data_Explorer.genAiPlugin-meta.xml`

```xml
<?xml version="1.0" encoding="UTF-8"?>
<GenAiPlugin xmlns="http://soap.sforce.com/2006/04/metadata">
    <canEscalate>false</canEscalate>
    <description>Explore and query marketing data across Data Cloud DMOs/DLOs and CRM objects using natural language</description>
    <developerName>Marketing_Data_Explorer</developerName>

    <genAiFunctions>
        <functionName>Discover_Marketing_Schema</functionName>
    </genAiFunctions>
    <genAiFunctions>
        <functionName>Generate_Marketing_SQL</functionName>
    </genAiFunctions>
    <genAiFunctions>
        <functionName>Query_Marketing_DMOs</functionName>
    </genAiFunctions>
    <genAiFunctions>
        <functionName>Query_CRM_Marketing_Data</functionName>
    </genAiFunctions>

    <genAiPluginInstructions>
        <description>DATA EXPLORATION — NL-TO-SQL WORKFLOW:

STEP 1 — DISCOVER SCHEMA (always do this first):
Call Discover_Marketing_Schema to get all available tables and fields with business descriptions.
Use objectType='all' for broad discovery, or 'dlo'/'dmo' to narrow scope.
Use filterKeyword to focus (e.g., 'campaign', 'ad', 'individual').
SAVE the schemaContext output — you need it for Step 2.
The schema includes business descriptions from Einstein Data Prism — use these to understand what each field contains.

STEP 2 — GENERATE SQL (translate user question):
Call Generate_Marketing_SQL with:
  - Schema_Context = the schemaContext from Step 1
  - User_Question = the user's natural language question
This returns a valid Data Cloud SQL query.

STEP 3 — EXECUTE QUERY:
Call Query_Marketing_DMOs with the generated SQL.
If the query fails with a column/table error:
  - Re-examine the schema from Step 1
  - Adjust the SQL and retry (the ReAct loop handles this automatically)

FOR CRM DATA (Campaigns, Leads, Contacts):
Use Query_CRM_Marketing_Data with SOQL directly — no schema discovery needed.
Common SOQL:
- SELECT Name, Status, NumberOfLeads, NumberOfConvertedLeads FROM Campaign
- SELECT LeadSource, COUNT(Id) cnt FROM Lead GROUP BY LeadSource

IMPORTANT: NEVER hardcode table or field names. Always discover them first via Discover_Marketing_Schema. Table names vary by org.</description>
        <developerName>instruction_data_exploration</developerName>
        <language>en_US</language>
        <masterLabel>Data Exploration Instructions</masterLabel>
    </genAiPluginInstructions>

    <language>en_US</language>
    <masterLabel>Marketing Data Explorer</masterLabel>
    <pluginType>Topic</pluginType>
    <scope>Can query and explore marketing data in Data Cloud and CRM. Read-only operations only.</scope>
</GenAiPlugin>
```

### 4.2 Campaign Operations Plugin (v2 — Enhanced with Broadcast Flow)

**File**: `force-app/main/default/genAiPlugins/Marketing_Campaign_Ops.genAiPlugin-meta.xml`

```xml
<?xml version="1.0" encoding="UTF-8"?>
<GenAiPlugin xmlns="http://soap.sforce.com/2006/04/metadata">
    <canEscalate>false</canEscalate>
    <description>Create audience segments and trigger campaigns via Broadcast Flows</description>
    <developerName>Marketing_Campaign_Ops</developerName>

    <genAiFunctions>
        <functionName>Create_Marketing_Segment</functionName>
    </genAiFunctions>
    <genAiFunctions>
        <functionName>Trigger_Broadcast_Flow</functionName>
    </genAiFunctions>

    <genAiPluginInstructions>
        <description>CAMPAIGN AND SEGMENT OPERATIONS:

CREATING SEGMENTS:
1. Clarify the target audience criteria
2. Recommend segment type:
   - Waterfall: Simple include/exclude — best for one-time sends
   - Dbt: SQL-based — best for complex filtering logic
   - Dynamic: Parameterized — best for recurring sends with runtime parameters (e.g., by region)
3. All segments target UnifiedIndividual__dlm by default (required for MC Growth compatibility)
4. Always confirm with user before creating
5. For Dbt segments, SQL must SELECT the primary key (ssot__Id__c) of the segment DMO

END-TO-END CAMPAIGN FLOW:
1. Create segment (this plugin — automated)
2. Create Broadcast Flow in Flow Builder (manual — guide user through browser steps):
   - Navigate to Flow Builder → New Flow → Broadcast Flow
   - Start element: link to the segment, set Run Type = Sync
   - Include Contact Point Email DMO (ssot__ContactPointEmail__dlm)
   - Add Send Email Message element with Communication Subscription
   - Save and Activate
3. Trigger Broadcast Flow (this plugin — automated via Trigger_Broadcast_Flow)
   - For Dynamic segments: pass runtime parameters as JSON (e.g., {"ZipCode": "90210"})

IMPORTANT: Broadcast Flows MUST be created in the browser UI — they cannot be created via API. The agent should guide the user through the setup, then trigger it programmatically.</description>
        <developerName>instruction_campaign_ops</developerName>
        <language>en_US</language>
        <masterLabel>Campaign Operations Instructions</masterLabel>
    </genAiPluginInstructions>

    <language>en_US</language>
    <masterLabel>Marketing Campaign Operations</masterLabel>
    <pluginType>Topic</pluginType>
    <scope>Can create segments and trigger broadcast flows. Cannot delete segments or modify existing campaigns without confirmation.</scope>
</GenAiPlugin>
```

### 4.3 Performance Analyst Plugin (v2 — Enhanced with owned_summary)

**File**: `force-app/main/default/genAiPlugins/Marketing_Performance_Analyst.genAiPlugin-meta.xml`

```xml
<?xml version="1.0" encoding="UTF-8"?>
<GenAiPlugin xmlns="http://soap.sforce.com/2006/04/metadata">
    <canEscalate>false</canEscalate>
    <description>Analyze paid and owned marketing performance, compare channels, and generate executive reports</description>
    <developerName>Marketing_Performance_Analyst</developerName>

    <genAiFunctions>
        <functionName>Analyze_Marketing_Performance</functionName>
    </genAiFunctions>
    <genAiFunctions>
        <functionName>Generate_Marketing_Report</functionName>
    </genAiFunctions>

    <genAiPluginInstructions>
        <description>PERFORMANCE ANALYSIS:

IMPORTANT — SCHEMA-FIRST FOR PAID ANALYSIS:
Before calling Analyze_Marketing_Performance for paid channels, you MUST first call Discover_Marketing_Schema to get the correct DLO table name. Pass this as the paidDataTable parameter. NEVER skip this step.

When user asks about marketing performance:
1. Determine scope: paid channels, owned channels, or both
2. For paid analysis: first discover schema, then use Analyze_Marketing_Performance with:
   - paid_summary: Aggregate paid channel metrics (clicks, impressions, CPC, CTR)
   - channel_comparison: Cross-channel performance comparison
   - time_series: Trend analysis over time
   - paidDataTable: The DLO table name from schema discovery
3. For owned analysis: use Analyze_Marketing_Performance with:
   - owned_summary: CRM Campaign engagement metrics (leads, conversions, opportunities)
4. For combined "unified view": run BOTH paid and owned analysis, then generate report
5. After getting data, use Generate_Marketing_Report for executive summary

KEY METRICS TO SURFACE:
- Paid: CTR, CPC, Impressions, Clicks by channel
- Owned: Lead generation, Conversion rate, Opportunity pipeline, Campaign ROI
- Combined: Cross-channel cost efficiency, Attribution patterns</description>
        <developerName>instruction_performance_analysis</developerName>
        <language>en_US</language>
        <masterLabel>Performance Analysis Instructions</masterLabel>
    </genAiPluginInstructions>

    <language>en_US</language>
    <masterLabel>Marketing Performance Analyst</masterLabel>
    <pluginType>Topic</pluginType>
    <scope>Can analyze marketing data and generate reports. Read-only analysis with AI-powered insights.</scope>
</GenAiPlugin>
```

---

## Phase 5: Custom Agent — Planner, Bot & Permissions (~15 min)

### 5.1 ReAct Planner (v2 — Enhanced variable mappings)

**File**: `force-app/main/default/genAiPlanners/Marketing_Intelligence_Planner.genAiPlanner-meta.xml`

```xml
<?xml version="1.0" encoding="UTF-8"?>
<GenAiPlanner xmlns="http://soap.sforce.com/2006/04/metadata">
    <attributeMappings>
        <attributeName>Marketing_Data_Explorer.Discover_Marketing_Schema.output_schemaContext</attributeName>
        <attributeType>CustomPluginFunctionAttribute</attributeType>
        <mappingTargetName>Schema_Context</mappingTargetName>
        <mappingType>Variable</mappingType>
    </attributeMappings>
    <attributeMappings>
        <attributeName>Marketing_Data_Explorer.Query_Marketing_DMOs.output_resultData</attributeName>
        <attributeType>CustomPluginFunctionAttribute</attributeType>
        <mappingTargetName>Current_Data</mappingTargetName>
        <mappingType>Variable</mappingType>
    </attributeMappings>
    <attributeMappings>
        <attributeName>Marketing_Performance_Analyst.Analyze_Marketing_Performance.output_resultData</attributeName>
        <attributeType>CustomPluginFunctionAttribute</attributeType>
        <mappingTargetName>Performance_Results</mappingTargetName>
        <mappingType>Variable</mappingType>
    </attributeMappings>
    <attributeMappings>
        <attributeName>Marketing_Campaign_Ops.Create_Marketing_Segment.output_segmentApiName</attributeName>
        <attributeType>CustomPluginFunctionAttribute</attributeType>
        <mappingTargetName>Created_Segment</mappingTargetName>
        <mappingType>Variable</mappingType>
    </attributeMappings>

    <description>Orchestrates marketing intelligence operations with reasoning across data exploration, campaign management, and performance analysis</description>

    <genAiFunctions>
        <genAiFunctionName>EmployeeCopilot__AnswerQuestionsWithKnowledge</genAiFunctionName>
    </genAiFunctions>

    <genAiPlugins>
        <genAiPluginName>Marketing_Data_Explorer</genAiPluginName>
    </genAiPlugins>
    <genAiPlugins>
        <genAiPluginName>Marketing_Campaign_Ops</genAiPluginName>
    </genAiPlugins>
    <genAiPlugins>
        <genAiPluginName>Marketing_Performance_Analyst</genAiPluginName>
    </genAiPlugins>

    <masterLabel>Marketing Intelligence Planner</masterLabel>
    <plannerType>AiCopilot__ReAct</plannerType>

    <plannerInstructions>
        <description>You are a Marketing Intelligence Agent that helps marketers explore data, create campaigns, and analyze performance.

CAPABILITIES:
1. DATA EXPLORATION: Query Data Cloud DMOs/DLOs and CRM objects to explore marketing data
2. CAMPAIGN CREATION: Create audience segments and trigger Broadcast Flows for campaign execution
3. PERFORMANCE ANALYSIS: Analyze paid and owned channel performance with AI-powered insights

CRITICAL: SCHEMA-FIRST QUERY WORKFLOW
For ANY Data Cloud query, ALWAYS follow this 3-step sequence:
  Step 1: Call Discover_Marketing_Schema → get table names, field names, and business descriptions
  Step 2: Call Generate_Marketing_SQL → pass schema + user question → get SQL
  Step 3: Call Query_Marketing_DMOs → execute the generated SQL
NEVER skip Step 1. NEVER hardcode table or field names — they vary by org.
If Step 3 fails with a column/table error, re-examine schema and retry with corrected SQL.

The Schema_Context variable persists across steps — reuse it for follow-up questions in the same session without re-discovering schema.

FOR CRM DATA:
Use Query_CRM_Marketing_Data with standard SOQL directly (no schema discovery needed).

FOR PERFORMANCE ANALYSIS:
Always discover schema FIRST to get the correct paid data table name before calling Analyze_Marketing_Performance.

FOR CAMPAIGN EXECUTION:
1. Create segment via Create_Marketing_Segment (automated)
2. Guide user through Broadcast Flow creation in browser (manual step)
3. Trigger the flow via Trigger_Broadcast_Flow (automated)

WORKFLOW PRINCIPLES:
- Use Data Cloud for paid media metrics (clicks, impressions, CPC, conversions)
- Use CRM for owned channel metrics (Campaigns, Leads, Contacts, Opportunities)
- When user asks for "all channels" or "unified view", query BOTH and combine results
- Confirm with user before any data-modifying operations
- Provide actionable recommendations with every analysis
- After data retrieval, use Generate_Marketing_Report for executive summaries

REASONING APPROACH:
1. Understand the user's marketing question
2. Determine data scope: Data Cloud (paid), CRM (owned), or both
3. Discover schema → Generate SQL → Execute query
4. Analyze and synthesize findings across sources
5. Present clear, actionable insights with recommendations</description>
    </plannerInstructions>
</GenAiPlanner>
```

### 5.2 Bot Configuration

**File**: `force-app/main/default/bots/Marketing_Copilot/Marketing_Copilot.bot-meta.xml`

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Bot xmlns="http://soap.sforce.com/2006/04/metadata">
    <agentType>AgentforceEmployeeAgent</agentType>
    <botMlDomain>
        <label>Marketing Copilot</label>
        <name>Marketing_Copilot</name>
    </botMlDomain>
    <contextVariables>
        <contextVariableMappings>
            <SObjectType>MessagingSession</SObjectType>
            <fieldName>MessagingSession.Id</fieldName>
            <messageType>EmbeddedMessaging</messageType>
        </contextVariableMappings>
        <dataType>Id</dataType>
        <developerName>SessionId</developerName>
        <includeInPrompt>true</includeInPrompt>
        <label>Session ID</label>
    </contextVariables>
    <description>AI-powered marketing assistant that explores data, creates campaigns, and analyzes performance across paid and owned channels. Grounded by Einstein Data Prism, Metadata Studio descriptions, and the Marketing Intelligence Semantic Data Model.</description>
    <label>Marketing Copilot</label>
    <logPrivateConversationData>false</logPrivateConversationData>
    <richContentEnabled>true</richContentEnabled>
    <sessionTimeout>0</sessionTimeout>
    <type>InternalCopilot</type>
</Bot>
```

**File**: `force-app/main/default/bots/Marketing_Copilot/v1.botVersion-meta.xml`

```xml
<?xml version="1.0" encoding="UTF-8"?>
<BotVersion xmlns="http://soap.sforce.com/2006/04/metadata">
    <botDialogs>
        <botSteps>
            <stepIdentifier>step_welcome</stepIdentifier>
            <type>Wait</type>
        </botSteps>
        <developerName>Welcome</developerName>
        <label>Welcome</label>
        <showInFooterMenu>false</showInFooterMenu>
    </botDialogs>
    <conversationGoals>
        <developerName>goal_explore_data</developerName>
        <label>Explore Marketing Data</label>
    </conversationGoals>
    <conversationGoals>
        <developerName>goal_create_campaign</developerName>
        <label>Create Campaign</label>
    </conversationGoals>
    <conversationGoals>
        <developerName>goal_analyze_performance</developerName>
        <label>Analyze Performance</label>
    </conversationGoals>
    <entryDialog>Welcome</entryDialog>
    <mainMenuDialog>Welcome</mainMenuDialog>
    <nlpProviders>
        <language>en_US</language>
        <nlpProviderName>Marketing_Intelligence_Planner</nlpProviderName>
        <nlpProviderType>GenAiPlanner</nlpProviderType>
    </nlpProviders>
    <responseDelayMilliseconds>0</responseDelayMilliseconds>
</BotVersion>
```

### 5.3 Permission Set

**File**: `force-app/main/default/permissionsets/Marketing_Copilot_Access.permissionset-meta.xml`

```xml
<?xml version="1.0" encoding="UTF-8"?>
<PermissionSet xmlns="http://soap.sforce.com/2006/04/metadata">
    <agentAccesses>
        <agentName>Marketing_Copilot</agentName>
        <enabled>true</enabled>
    </agentAccesses>
    <description>Access to Marketing Copilot agent with required object and field permissions for data exploration, campaign creation, and performance analysis</description>
    <hasActivationRequired>false</hasActivationRequired>
    <label>Marketing Copilot Access</label>
    <objectPermissions>
        <allowCreate>false</allowCreate>
        <allowDelete>false</allowDelete>
        <allowEdit>false</allowEdit>
        <allowRead>true</allowRead>
        <modifyAllRecords>false</modifyAllRecords>
        <object>Campaign</object>
        <viewAllFields>false</viewAllFields>
        <viewAllRecords>false</viewAllRecords>
    </objectPermissions>
    <objectPermissions>
        <allowCreate>false</allowCreate>
        <allowDelete>false</allowDelete>
        <allowEdit>false</allowEdit>
        <allowRead>true</allowRead>
        <modifyAllRecords>false</modifyAllRecords>
        <object>Lead</object>
        <viewAllFields>false</viewAllFields>
        <viewAllRecords>false</viewAllRecords>
    </objectPermissions>
    <objectPermissions>
        <allowCreate>false</allowCreate>
        <allowDelete>false</allowDelete>
        <allowEdit>false</allowEdit>
        <allowRead>true</allowRead>
        <modifyAllRecords>false</modifyAllRecords>
        <object>Contact</object>
        <viewAllFields>false</viewAllFields>
        <viewAllRecords>false</viewAllRecords>
    </objectPermissions>
</PermissionSet>
```

---

## Phase 6: Deployment Sequence (~10 min)

Deploy in strict dependency order:

```bash
ORG_ALIAS="your-org-alias"

# 1. Apex Classes (all 6 services)
sf project deploy start \
  --source-dir force-app/main/default/classes/MarketingDataExplorerService.cls \
  --source-dir force-app/main/default/classes/MarketingSchemaService.cls \
  --source-dir force-app/main/default/classes/MarketingCRMQueryService.cls \
  --source-dir force-app/main/default/classes/MarketingCampaignService.cls \
  --source-dir force-app/main/default/classes/MarketingBroadcastFlowService.cls \
  --source-dir force-app/main/default/classes/MarketingPerformanceService.cls \
  --target-org $ORG_ALIAS --wait 10

# 2. Prompt Templates
sf project deploy start \
  --source-dir force-app/main/default/genAiPromptTemplates/Marketing_NL_to_SQL.genAiPromptTemplate-meta.xml \
  --source-dir force-app/main/default/genAiPromptTemplates/Marketing_Performance_Report.genAiPromptTemplate-meta.xml \
  --target-org $ORG_ALIAS --wait 10

# 3. GenAI Functions (all 8)
sf project deploy start \
  --source-dir force-app/main/default/genAiFunctions/Discover_Marketing_Schema \
  --source-dir force-app/main/default/genAiFunctions/Generate_Marketing_SQL \
  --source-dir force-app/main/default/genAiFunctions/Query_Marketing_DMOs \
  --source-dir force-app/main/default/genAiFunctions/Query_CRM_Marketing_Data \
  --source-dir force-app/main/default/genAiFunctions/Create_Marketing_Segment \
  --source-dir force-app/main/default/genAiFunctions/Trigger_Broadcast_Flow \
  --source-dir force-app/main/default/genAiFunctions/Analyze_Marketing_Performance \
  --source-dir force-app/main/default/genAiFunctions/Generate_Marketing_Report \
  --target-org $ORG_ALIAS --wait 10

# 4. GenAI Plugins (3)
sf project deploy start \
  --source-dir force-app/main/default/genAiPlugins/Marketing_Data_Explorer.genAiPlugin-meta.xml \
  --source-dir force-app/main/default/genAiPlugins/Marketing_Campaign_Ops.genAiPlugin-meta.xml \
  --source-dir force-app/main/default/genAiPlugins/Marketing_Performance_Analyst.genAiPlugin-meta.xml \
  --target-org $ORG_ALIAS --wait 10

# 5. GenAI Planner
sf project deploy start \
  --source-dir force-app/main/default/genAiPlanners/Marketing_Intelligence_Planner.genAiPlanner-meta.xml \
  --target-org $ORG_ALIAS --wait 10

# 6. Bot + BotVersion
sf project deploy start \
  --source-dir force-app/main/default/bots/Marketing_Copilot \
  --target-org $ORG_ALIAS --wait 10

# 7. Permission Set
sf project deploy start \
  --source-dir force-app/main/default/permissionsets/Marketing_Copilot_Access.permissionset-meta.xml \
  --target-org $ORG_ALIAS --wait 10

# 8. Assign Permission Set
sf org assign permset --name Marketing_Copilot_Access --target-org $ORG_ALIAS
```

### Deployment Summary (v2)

| Step | Components | Count |
|------|-----------|-------|
| 1 | Apex Classes | 6 (+1 from v1) |
| 2 | Prompt Templates | 2 |
| 3 | GenAI Functions | 8 (+1 from v1) |
| 4 | GenAI Plugins | 3 |
| 5 | GenAI Planner | 1 |
| 6 | Bot + BotVersion | 2 |
| 7 | Permission Set | 1 |
| 8 | CSP Trusted Site | 1 |
| **Total** | | **24 components** |

---

## Phase 7: Default Paid Media Agent Setup (Manual — 8 min)

In addition to the custom agent, configure the default Agentforce agent with Paid Media Topic:

1. **Setup → Agentforce Agents** → Enable Agentforce ON → Enable Default Agent ON → Refresh
2. Click **Agentforce (Default)** → Open in Builder → Deactivate
3. Topics → New → **Add from Asset Library** → Select **Paid Media Topic** → Finish
4. Activate agent

**Test**: Marketing Intelligence → Campaign Summary → Click "Optimize Campaigns" → Reply `2.Meta Ads`

---

## Phase 8: Validation, Metadata Quality & Demo Script (~20 min)

### 8.1 Validation Checklist

| Component | Verification |
|-----------|-------------|
| CSP Trusted Sites | Setup → CSP Trusted Sites → Github_Images active |
| EMI Mock Connectors | Setup → Other Connectors → Meta Ads & Google Ads "Connected" |
| Data Pipeline | MI → Data Management → Pipeline "Active" |
| Einstein Enrichment | MI → Data Enrichment → "Complete" |
| Campaign Pattern | MI → Patterns → 6 dimensions extracted |
| Tableau Viz | CRM Analytics → Meta Ads CPC dashboard visible |
| MI Goal | MI → Goals → 10K impressions goal |
| **Data Prism** | Setup → Einstein Data Prism → "Active" ← NEW |
| **Metadata Studio** | Setup → Metadata Studio → descriptions reviewed ← NEW |
| **Semantic Data Model** | MI Workspace → SDM visible ← NEW |
| Custom Agent | Setup → Agents → Marketing Copilot "Active" |
| Default Agent | Agentforce (Default) → Paid Media Topic → Active |
| Permission Set | Marketing_Copilot_Access assigned to demo user |

### 8.2 Metadata Quality Checklist ← NEW

High-quality metadata descriptions directly control Agentforce accuracy. Review these before demo:

**GenAI Function Descriptions** (controls when planner selects each action):
- [ ] Each function description clearly states what it does AND when to use it
- [ ] Input parameter descriptions include examples and constraints
- [ ] Output parameter descriptions explain what the data contains

**Plugin Instructions** (controls how planner uses functions):
- [ ] Step-by-step workflows are explicit and numbered
- [ ] NEVER/ALWAYS rules are capitalized for emphasis
- [ ] Common error scenarios have recovery instructions

**Planner Instructions** (controls overall reasoning):
- [ ] All three capabilities are described with scope boundaries
- [ ] Schema-first workflow is marked as CRITICAL
- [ ] Variable persistence is documented for multi-turn conversations

**Data Cloud Field Descriptions** (reviewed in Metadata Studio):
- [ ] All metric fields include calculation method
- [ ] All dimension fields include example values
- [ ] Relationship fields describe join direction and cardinality

### 8.3 Demo Script: Three-Act Story

**Act 1 — Data Exploration** (The Discovery)
> "Show me what marketing data we have across all channels"
> "How many campaigns are running on Meta Ads?"
> "What does our CRM Campaign data look like?"

**Act 2 — Performance Analysis** (The Insight)
> "Compare performance across all our paid channels"
> "Which campaigns have the highest CPC? Generate a report"
> "Show me the time series trend for Meta Ads over the last 30 days"
> "Give me a unified view — both paid and owned channel performance"

**Act 3 — Campaign Action** (The Action)
> "Create a segment for users who clicked on our top campaign"
> "Now let's trigger a broadcast to that audience" (agent guides through Flow setup, then triggers)
> "Optimize my Meta Ads campaigns" (triggers Paid Media Topic)
> "Generate an executive summary of our marketing performance"

---

## Prerequisites Checklist

Before running on any org:

- [ ] Salesforce org with Marketing Intelligence license
- [ ] Data Cloud enabled and provisioned
- [ ] Agentforce features enabled
- [ ] Einstein Data Prism enabled (Setup → Einstein Data Prism)
- [ ] MC Growth/Advanced provisioned (for journey/email sends)
- [ ] Tableau/CRM Analytics license (for visualization)
- [ ] Admin permissions on org

**Authentication Note**: All Apex services use `UserInfo.getSessionId()` for Data Cloud API authentication. This works for user-context execution (which is the standard Agentforce execution model). For system-context scenarios, a Named Credential with OAuth would be needed — but is not required for this demo.

## Known Limitations & Gotchas

1. **EMI Mock data**: Only generates last 30 days of data
2. **Standard (UI) segments**: Cannot be created via API — only Waterfall/Dbt/Dynamic
3. **Broadcast Flows**: Must be created via browser UI (no API), but CAN be triggered via API
4. **DLO/DMO names**: Vary by org — agent MUST discover at runtime (schema-first workflow)
5. **Data Cloud Query API**: Uses SQL (not SOQL) — different syntax from CRM queries
6. **Prompt Template model**: Default `sfdc_ai__DefaultOpenAIGPT4OmniMini` — verify available on org
7. **Permission Set**: Must be assigned BEFORE testing agent — most commonly missed step
8. **MC Growth segments**: Must target `UnifiedIndividual__dlm` for Broadcast Flow compatibility
9. **Data Prism**: Requires initial scan time (~2-5 min) after provisioning
10. **Semantic Query API**: Not yet available for Agentforce direct use — current implementation uses NL-to-SQL with SDM-enriched schema as Path B (future: Path A with native Semantic Query API)

---

## Appendix: AI Grounding Architecture (v2 Addition)

### Three Layers of AI Grounding

```
Layer 1 — Einstein Data Prism (Automatic)
  What: Scans all Data Cloud metadata, auto-generates semantic descriptions
  Where: Stored in vector database
  How it helps: At runtime, returns focused tables/fields matching any NL query
  Config: Enable in Setup → Einstein Data Prism

Layer 2 — Metadata Studio (Admin-Curated)
  What: UI for reviewing and refining Data Prism auto-generated descriptions
  Where: Setup → Metadata Studio
  How it helps: Human-refined descriptions improve LLM understanding of field semantics
  Config: Review and refine descriptions for all marketing-relevant fields

Layer 3 — Marketing Intelligence Semantic Data Model (Platform-Native)
  What: Business-language abstraction defining metrics, dimensions, relationships
  Where: Created automatically by MI Data Pipeline
  How it helps: Provides consistent metric definitions (e.g., CPC = Spend / Clicks)
  Config: Verify in MI Workspace → Semantic Data Model view
  Future: Semantic Query API will allow agents to query through SDM directly
```

### How It Flows End-to-End

```
User asks: "What's the CPC for Meta Ads campaigns?"
  │
  ├─ Data Prism: Pre-enriched schema has description "Cost per click" on CPC__c field
  ├─ Metadata Studio: Admin refined description to "Cost per click in platform currency (USD)"
  ├─ SDM: CPC defined as SUM(Spend) / NULLIF(SUM(Clicks), 0)
  │
  ├─ Schema Discovery: Returns table + fields WITH enriched descriptions
  ├─ NL-to-SQL Template: Uses descriptions to generate accurate SQL
  ├─ Query Execution: Runs against Data Cloud
  │
  └─ Result: Accurate CPC data grounded in business definitions
```

### Future-State Architecture (Path A)

When Salesforce exposes the Semantic Query API for Agentforce consumption:

```
User asks: "What's the CPC for Meta Ads campaigns?"
  │
  ├─ Agent calls Semantic Query API with business terms
  ├─ Tableau Semantics translates to optimized SQL using SDM definitions
  ├─ Data Cloud executes query
  │
  └─ Result: Native semantic query — no custom NL-to-SQL needed
```

This would replace Phase 2A's 3-layer architecture with a single native API call, while maintaining the same user experience. The current Path B implementation (SDM-enriched schema + custom NL-to-SQL) provides a solid bridge to this future state.