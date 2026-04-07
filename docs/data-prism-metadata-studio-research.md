# Einstein Data Prism & Metadata Studio Research

> Research findings on how Einstein Data Prism and Metadata Studio strengthen Agentforce agent accuracy through semantic metadata enrichment and AI grounding. Both features are directly relevant to our Marketing Agent's NL-to-SQL architecture and should be incorporated into the implementation plan.

## Einstein Data Prism

Einstein Data Prism enriches the Data Cloud schema with **semantic descriptions** that correlate natural language phrases with Data Cloud data structures. It provides more accurate and relevant responses when users interact with AI features.

### How It Works

After Data Prism is provisioned, **all metadata is scanned** — schema, relationships, sample values, and descriptions. Missing descriptions are **auto-generated and saved in a vector database**.

At runtime, when a user asks a natural language question, Data Prism returns the **tables and fields that best match the utterance** as focused grounding data. This dramatically improves LLM accuracy by narrowing context to only relevant schema elements.

### Relevance to Our Agent

Our NL-to-SQL Layer 1 (MarketingSchemaService) currently returns ALL tables and fields. Data Prism could provide **pre-filtered, semantically matched schema** — reducing token usage and improving SQL generation accuracy. This is a natural complement to our 3-layer architecture.

## Metadata Studio

Metadata Studio is the **UI interface for Einstein Data Prism** — it's how admins view and edit the auto-generated semantic descriptions on Data Cloud objects and fields. The URL path `generative_ai_prism_metadata_studio` confirms it's part of the Data Prism family.

### Key Capabilities

- View all auto-generated descriptions across DMOs/DLOs
- Edit/refine descriptions to improve AI accuracy
- Add business context that LLMs use for grounding
- Review and approve semantic annotations before they take effect

### Relevance to Our Agent

Metadata Studio is a **setup/configuration step** — during demo preparation, the admin would use Metadata Studio to review and refine the auto-generated descriptions on Marketing Intelligence tables, ensuring the agent gets the highest quality semantic context.

## Key Metrics

- **Data Prism Type**: Auto-Semantic — Scans schema → auto-generates descriptions → stores in vector DB
- **Metadata Studio Type**: Admin UI — View/edit Data Prism descriptions — refinement layer
- **Impact on NL-to-SQL**: High — Pre-filters schema to relevant tables, reducing hallucination
- **Implementation Effort**: Low — Provisioning + review step — no custom code needed

## Broader Grounding Architecture

Salesforce's grounding strategy uses multiple layers:

### Dynamic Data Graphs (Customer 360 Profile)
Real-time knowledge graphs delivering LLM-friendly JSON with millisecond latency. Already partially covered in our plan for real-time segment evaluation.

### Agentforce Data Library (RAG)
Automates Data Cloud stream creation, object mapping, and search index/retriever setup. Relevant for unstructured knowledge grounding (not yet in our scope).

### Metadata-First AI
Object/field descriptions, topic instructions, and action descriptions all serve as contextual signals for the planner. Our plan already leverages this through detailed plugin instructions and function descriptions.

### Hybrid Retrieval
Data Cloud combines semantic similarity search + keyword matching for retrieval. Data Prism adds the semantic layer that powers this.

> ℹ️ **Recommendation for Plan v2**: Add a new Phase 1.5 (post-foundation, pre-custom-agent) for Einstein Data Prism provisioning and Metadata Studio review. This is a low-effort, high-impact enhancement that directly improves our NL-to-SQL accuracy by providing the agent with semantically enriched schema metadata. Also add metadata description best practices to the planner instructions and a Metadata Quality Checklist to Phase 8.
