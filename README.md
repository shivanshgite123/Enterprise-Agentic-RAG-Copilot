# Insurance Policy Intelligence Agentic RAG Copilot

An end-to-end **Forward Deployed Engineer (FDE) project** that transforms an Agentic RAG workflow into a deployable insurance policy intelligence application using **LangGraph, FastAPI, Pinecone, Groq, HuggingFace Embeddings, HTML, CSS, and JavaScript**.

**Customer:** HDFC ERGO General Insurance Company Limited  
**Industry:** General Insurance  
**Use Case:** Insurance Policy Intelligence and Claims Document Assistance  
**Project Type:** Forward Deployed Engineer (FDE) — Agentic RAG Application

> **Disclaimer:** This is a proposed portfolio project, not an official HDFC ERGO product. It does not imply affiliation, endorsement, or production deployment by HDFC ERGO. Examples are illustrative. Actual coverage, exclusions, waiting periods, and claim requirements must be verified against the applicable official policy documents.

---

## 1. Business Problem

### Customer

**HDFC ERGO General Insurance Company Limited**

### Problem

Insurance companies maintain large collections of policy wordings, policy schedules, product brochures, claim forms, exclusions, add-on coverage documents, and claims-processing guidelines.

Customers, insurance agents, and claims-support employees frequently need to find accurate information about:

- Policy coverage and benefits.
- Waiting periods and exclusions.
- Coverage limits and sublimits.
- Required documents for reimbursement claims.
- Claim submission procedures.
- Policy-specific conditions and endorsements.
- Differences between policy versions.

Finding an accurate answer can require searching multiple documents and verifying the exact wording applicable to a particular policy.

Traditional keyword search may return irrelevant passages. A conventional chatbot may retrieve the wrong policy, overlook an exclusion, or generate unsupported information about coverage.

### Example

A customer asks:

> **"Does my health insurance policy cover hospitalization expenses?"**

The system must identify the applicable policy, retrieve the relevant coverage clause, and check related limitations, exclusions, and conditions before answering.

Another customer asks:

> **"Will my claim definitely be approved if I submit these hospital bills?"**

The system should explain the relevant claim requirements and identify any missing information. It must not promise claim approval or make an unauthorized claim decision.

### Business Goal

Build an Insurance Policy Intelligence Copilot that:

1. Retrieves relevant official policy documents.
2. Identifies the correct product and policy version.
3. Evaluates whether retrieved evidence supports the answer.
4. Checks related conditions, exclusions, limits, and waiting periods.
5. Rewrites weak queries and retries retrieval when appropriate.
6. Generates grounded answers with document citations.
7. Identifies missing information and requests clarification.
8. Maintains a workflow trace for transparency and debugging.
9. Helps users prepare claim-document checklists.
10. Escalates unresolved interpretations and claim decisions to authorized human reviewers.

---

## 2. Why This Is an FDE Project

A Forward Deployed Engineer does more than build an LLM notebook. The FDE understands the customer's operational workflow and delivers a usable application integrating data, business rules, APIs, user interfaces, security, evaluation, and deployment.

```text
Customer Problem
      ↓
Discovery & Requirements
      ↓
Insurance Document Analysis
      ↓
Solution Architecture
      ↓
Knowledge Integration
      ↓
Agentic RAG Development
      ↓
API Development
      ↓
User Interface
      ↓
Security + Audit + Testing
      ↓
Deployment
      ↓
Observe + Improve
```

This repository demonstrates the end-to-end FDE lifecycle for a document-intensive insurance workflow.

---

## 3. Simple Architecture

```text
                   ┌─────────────────────┐
                   │ Customer / Employee │
                   └──────────┬──────────┘
                              │
                              ▼
                   ┌─────────────────────┐
                   │ HTML/CSS/JavaScript │
                   │      Web UI         │
                   └──────────┬──────────┘
                              │ POST /api/chat
                              ▼
                   ┌─────────────────────┐
                   │       FastAPI       │
                   └──────────┬──────────┘
                              │
                              ▼
                   ┌─────────────────────┐
                   │     LangGraph       │
                   │ Agentic RAG Control │
                   └──────────┬──────────┘
                              │
               ┌──────────────┴──────────────┐
               │                             │
               ▼                             ▼
       ┌────────────────┐           ┌────────────────┐
       │ Private Policy │           │ Approved       │
       │ Knowledge Base │           │ External Search│
       │ Pinecone       │           │ Optional       │
       └────────┬───────┘           └────────┬───────┘
                │                            │
                └─────────────┬──────────────┘
                              ▼
                   ┌─────────────────────┐
                   │      Groq LLM       │
                   │ Grounded Response   │
                   └──────────┬──────────┘
                              │
                              ▼
                   ┌─────────────────────┐
                   │ Citations + Trace   │
                   │ Human Escalation    │
                   └─────────────────────┘
```

**Important:** External search is optional and should only be used when permitted by the deployment. Customer-specific policy questions should primarily be answered using the correct official policy documents.

---

## 4. Agentic RAG Workflow

```text
Question
   ↓
[1] Route Question
   ├── Greeting / simple conversation → Direct Answer
   │
   └── Policy or claims question
                ↓
[2] Identify Product and Policy Version
                ↓
[3] Retrieve from Private Pinecone KB
                ↓
[4] Grade Private Evidence
       ┌────────┴────────┐
       │                 │
     GOOD               WEAK
       │                 │
       ▼                 ▼
Check Related       [5] Clarify Question
Clauses                  or Rewrite Query
       │                 │
       ▼                 ▼
Check Exclusions    Retry Retrieval
and Conditions           │
       │                 ▼
       │            Evidence Sufficient?
       │              ┌──┴───┐
       │             YES     NO
       │              │       │
       ▼              ▼       ▼
Generate Cited    Generate   Explain Limits
Answer            Cited      or Escalate
                  Answer
```

### Why Agentic?

Normal RAG approximately follows:

```text
Question → Retrieve → Generate
```

This project makes decisions based on the question and evidence:

```text
Question
 → Identify policy context
 → Retrieve documents
 → Evaluate evidence
 → Check conditions and exclusions
 → Rewrite or retry when necessary
 → Generate a grounded answer
 → Cite sources
 → Escalate unresolved cases
```

The system controls what to do next based on its current state instead of blindly generating an answer from the first retrieved passages.

---

## 5. Main Technology Stack

| Layer | Technology | Purpose |
|---|---|---|
| Agent workflow | LangGraph | Stateful routing, conditional decisions, and retries |
| LLM | Groq | Question classification, evidence grading, rewriting, and answer generation |
| Embeddings | HuggingFace Sentence Transformers | Semantic representation of policy documents and questions |
| Vector database | Pinecone | Retrieval of approved insurance knowledge |
| External search | Tavily, optional | Secondary search when permitted and appropriate |
| API | FastAPI | Chat, health-check, and document-ingestion endpoints |
| Frontend | HTML/CSS/JavaScript | User interface for questions, answers, citations, and traces |
| Audit | SQLite | Basic workflow and decision logging |
| Document processing | PDF/TXT/Markdown/DOCX loaders | Document extraction and chunking |
| Packaging | Docker | Reproducible application deployment |

### Embedding Model

A local embedding model such as `sentence-transformers/all-MiniLM-L6-v2` can be used for semantic retrieval. It produces 384-dimensional vectors.

The Pinecone index dimension must match the selected embedding model. If you change the embedding model, verify the vector dimensions and index configuration before ingestion.

---

## 6. Project Structure

```text
Insurance-Agentic-RAG-Copilot/
│
├── app/
│   ├── api/
│   │   └── routes.py
│   │
│   ├── core/
│   │   ├── config.py
│   │   └── logging.py
│   │
│   ├── rag/
│   │   ├── state.py
│   │   ├── vectorstore.py
│   │   └── workflow.py
│   │
│   ├── services/
│   │   ├── audit.py
│   │   └── ingestion.py
│   │
│   └── main.py
│
├── data/
│   └── sample_kb/
│       ├── README_HDFC_ERGO.md
│       ├── insurance_policy.md
│       └── official_sources/
│
├── static/
│   ├── css/
│   │   └── style.css
│   └── js/
│       └── app.js
│
├── templates/
│   └── index.html
│
├── tests/
│   ├── test_ingestion.py
│   └── test_workflow.py
│
├── uploads/
├── .env.example
├── .gitignore
├── Dockerfile
├── ingest_sample_kb.py
├── requirements.txt
├── run.py
└── README.md
```

**Note:** This is the proposed repository structure. File names and features should match the actual implementation as you develop the project.

---

## 7. Setup

### Step 1 — Create a virtual environment

```bash
python -m venv venv
```

Windows:

```bash
venv\Scripts\activate
```

macOS/Linux:

```bash
source venv/bin/activate
```

### Step 2 — Install dependencies

```bash
pip install -r requirements.txt
```

### Step 3 — Configure environment variables

Create a `.env` file in the project root.

```env
# LLM
GROQ_API_KEY=your_groq_api_key_here
GROQ_MODEL=your_supported_groq_model

# Vector database
PINECONE_API_KEY=your_pinecone_api_key_here
PINECONE_INDEX_NAME=insurance-policy-rag
PINECONE_NAMESPACE=hdfc-ergo-policy-kb

# Embeddings
EMBEDDING_MODEL=sentence-transformers/all-MiniLM-L6-v2

# Optional external search
TAVILY_API_KEY=your_tavily_api_key_here

# Security
ADMIN_API_KEY=replace_with_a_secure_random_secret

# Application
APP_ENV=development
```

Configure only the providers actually used by your implementation. For example, an OpenAI API key is not required if the application uses Groq for its LLM and a local HuggingFace embedding model.

### Environment Variable Reference

| Variable | Description | Required |
|---|---|---|
| `GROQ_API_KEY` | API key for the configured Groq LLM | Yes, if using Groq |
| `GROQ_MODEL` | Supported Groq model identifier | Yes, if using Groq |
| `PINECONE_API_KEY` | Pinecone API key | Yes, if using Pinecone |
| `PINECONE_INDEX_NAME` | Vector index name | Yes, if using Pinecone |
| `PINECONE_NAMESPACE` | Namespace for the selected knowledge collection | Optional, depending on implementation |
| `EMBEDDING_MODEL` | Embedding model identifier | Yes |
| `TAVILY_API_KEY` | Optional external-search API key | Only if external search is enabled |
| `ADMIN_API_KEY` | Secret used to protect administrative ingestion endpoints | Yes, if such endpoints are implemented |
| `APP_ENV` | Application environment | Optional |

### Security Notes

- Never commit `.env` to version control.
- Add `.env`, `venv/`, `__pycache__/`, and sensitive uploads to `.gitignore`.
- Use secure, randomly generated secrets.
- Authenticate users and authorize access to customer-specific documents.
- Do not expose customer policy schedules or claims documents to unauthorized users.
- Do not send sensitive personal data to external providers unless the deployment's approved privacy controls permit it.

### Step 4 — Add the policy knowledge

Place your test documents in:

```text
data/sample_kb/
```

For example:

```text
data/sample_kb/
├── README_HDFC_ERGO.md
├── insurance_policy.md
└── official_sources/
```

Use fictional policy content only for demonstrations unless you have selected and verified the applicable official documents.

For realistic evaluation, download the exact official policy wording and relevant claim documents for one selected product from:

- [HDFC ERGO Health Policy Wordings](https://www.hdfcergo.com/download/policy-wordings/health)
- [HDFC ERGO Policy Wordings by Category](https://www.hdfcergo.com/download/policy-wordings)

Confirm the product name, UIN, policy period, and applicable version before ingestion.

### Step 5 — Ingest sample documents

```bash
python ingest_sample_kb.py
```

The ingestion pipeline should perform:

```text
Policy Documents
       ↓
Load and Extract Text
       ↓
Preserve Page / Clause Metadata
       ↓
Chunk Documents
       ↓
Generate Embeddings
       ↓
Index in Pinecone
       ↓
Available for Retrieval
```

### Step 6 — Run the application

```bash
python run.py
```

Open the local application:

```text
http://127.0.0.1:8000
```

FastAPI API documentation:

```text
http://127.0.0.1:8000/docs
```

These URLs apply when the application is configured to run locally on port `8000`.

---

## 8. Demonstration Scenarios

### Demo A — Policy Coverage Question

Ask:

> **Does my health insurance policy cover hospitalization expenses?**

Expected workflow:

```text
Question
   ↓
Identify Policy
   ↓
Private KB Retrieval
   ↓
Evidence Grade → GOOD
   ↓
Retrieve Conditions and Exclusions
   ↓
Generate Answer with Citations
```

Expected behavior:

The system explains what the applicable policy documents say, identifies relevant limitations, and cites the source clauses.

**Teaching point:** The model should explain the evidence rather than produce an unsupported yes-or-no answer.

### Demo B — Claim Document Checklist

Ask:

> **What documents should I prepare for a reimbursement claim?**

Expected workflow:

```text
Question
   ↓
Identify Product and Claim Type
   ↓
Retrieve Claim Instructions
   ↓
Grade Evidence
   ↓
Generate Document Checklist
   ↓
Cite Official Sources
```

Expected behavior:

The system creates a checklist from the applicable claim instructions and states when additional documents may be required depending on the claim circumstances.

**Teaching point:** RAG can transform lengthy documents into practical, source-supported guidance.

### Demo C — Waiting Period or Exclusion

Ask:

> **Is this treatment covered immediately after I purchase the policy?**

Expected workflow:

```text
Question
   ↓
Identify Applicable Policy
   ↓
Retrieve Waiting-Period Clause
   ↓
Retrieve Exclusions and Conditions
   ↓
Evaluate Evidence
   ↓
Answer or Ask for Clarification
```

Expected behavior:

The system identifies the relevant waiting-period and exclusion clauses. It must not invent a waiting period when the correct policy wording is unavailable.

**Teaching point:** Policy answers often require several related clauses, not just one matching passage.

### Demo D — Conflicting Documents

Ask:

> **One brochure says this benefit is included, but another document appears to exclude it. Which applies?**

Expected workflow:

```text
Question
   ↓
Retrieve Both Documents
   ↓
Compare Product, UIN, Version and Dates
   ↓
Check Schedule and Endorsements
   ↓
Resolve with Evidence or Escalate
```

Expected behavior:

The system checks document applicability and version metadata. If the conflict remains unresolved, it explains the uncertainty and refers the issue for authorized review.

**Teaching point:** Version awareness is critical in enterprise document retrieval.

### Demo E — Claim Approval Uncertainty

Ask:

> **Will my claim definitely be approved?**

Expected behavior:

The system explains what can be verified from the policy and claim documents, identifies missing information, and makes clear that the official claim decision belongs to the authorized claims process.

**Teaching point:** A reliable assistant distinguishes between explaining policy terms and making a binding decision.

### Demo F — Direct Conversation

Ask:

> **Hello!**

Expected workflow:

```text
Router → DIRECT
       ↓
Direct Answer
```

Expected behavior:

The system responds naturally without unnecessary policy retrieval.

**Teaching point:** Not every user message requires a vector search or LLM-heavy workflow.

---

## 9. Document Upload and Ingestion

If the application implements an authorized document-upload feature, users with the required permissions can upload supported policy and claims documents.

Possible file formats:

- `.pdf`
- `.txt`
- `.md`
- `.docx`

Expected pipeline:

```text
Upload Document
       ↓
Authenticate and Authorize
       ↓
Validate File Type and Size
       ↓
Extract Text
       ↓
Chunk and Attach Metadata
       ↓
Generate Embeddings
       ↓
Index in Pinecone
       ↓
Verify Retrieval
```

Recommended metadata includes:

- Product name and UIN.
- Policy version or effective date.
- Document type.
- Section or clause number.
- Source document name.
- Page number.
- Source URL.
- Retrieval date.

**Important:** Uploaded documents should not become available to all users automatically. Apply authorization and document-access controls before indexing or retrieval.

---

## 10. API Endpoints

The following endpoints are a proposed interface. Ensure the actual FastAPI implementation matches the documented routes and response schemas.

### `GET /api/health`

Checks whether the application is running.

Example response:

```json
{
  "status": "ok"
}
```

### `POST /api/chat`

Example request:

```json
{
  "question": "What documents should I prepare for a reimbursement claim?"
}
```

Example response:

```json
{
  "answer": "The applicable claim instructions list the relevant supporting documents. Please verify the checklist against your policy and claim circumstances.",
  "source_used": "private_kb",
  "trace": [
    "Question classification",
    "Policy document retrieval",
    "Evidence grading",
    "Related clause retrieval",
    "Grounded answer generation"
  ],
  "citations": [
    {
      "document": "example_claim_instructions.pdf",
      "page": 4,
      "clause": "Example section"
    }
  ],
  "needs_human_review": false
}
```

**This is an illustrative response schema, not a real policy answer.** Citation values must be populated from actual retrieved documents, never fabricated.

### `POST /api/ingest`

An administrative endpoint for adding approved documents to the knowledge base.

If implemented, protect it using authentication and authorization. An API key may be supplied through an `X-Admin-Key` header in a portfolio demonstration, but production systems should use appropriate identity management, secret rotation, and access controls.

---

## 11. Evaluation and Testing

Evaluate the application using verified policy questions and expected evidence.

| Test ID | Scenario | Expected result |
|---|---|---|
| `INS-001` | Policy coverage | Retrieves relevant coverage clauses and related conditions |
| `INS-002` | Waiting period | Retrieves the correct clause for the identified policy |
| `INS-003` | Exclusion | Checks relevant exclusions before answering |
| `INS-004` | Claim documents | Generates a checklist grounded in claim instructions |
| `INS-005` | Policy version conflict | Detects incompatible or conflicting source documents |
| `INS-006` | Missing product details | Requests clarification instead of guessing |
| `INS-007` | Insufficient evidence | Communicates limitations or escalates |
| `INS-008` | Claim approval request | Avoids unauthorized promises or decisions |
| `INS-009` | Citation validation | Ensures citations support the generated answer |
| `INS-010` | Direct greeting | Avoids unnecessary retrieval |

Measure:

- Retrieval relevance and recall.
- Answer correctness against verified policy documents.
- Citation accuracy.
- Unsupported-claim or hallucination rate.
- Correct identification of policy versions.
- Appropriate escalation rate.
- Response latency and cost.
- User feedback and task completion.

---

## 12. Security, Governance, and Limitations

Insurance policy and claim workflows may involve personal and financial information. The application should:

- Authenticate users and enforce role-based authorization.
- Restrict retrieval to documents the user is allowed to access.
- Protect API keys and uploaded files.
- Validate uploaded document types and sizes.
- Avoid storing unnecessary sensitive information in audit logs.
- Treat retrieved text and external search results as untrusted data.
- Cite source documents and page or clause references when available.
- Identify uncertainty and conflicting evidence explicitly.
- Prevent the LLM from inventing policy clauses, limits, or claim requirements.
- Escalate disputed interpretations and official claim decisions to authorized reviewers.

The copilot is an informational assistant. It does not replace the official policy contract, the insurer's authorized claims process, or professional advice.

---

## 13. Expected Business Value

The proposed application aims to:

1. Help customers and employees find policy information faster.
2. Reduce repetitive policy-related support questions.
3. Improve access to relevant coverage conditions and exclusions.
4. Make claim-document requirements easier to understand.
5. Provide traceable answers supported by source documents.
6. Reduce unsupported statements about coverage and claim outcomes.
7. Identify missing information before users rely on an answer.
8. Support controlled updates when policy documents change.
9. Improve transparency through citations and workflow traces.
10. Help insurance operations teams focus on cases requiring human judgment.

These are intended benefits, not measured results. A pilot should establish a baseline and verify improvements using the evaluation metrics above.

---

## 14. FDE Lifecycle

A Forward Deployed Engineer must turn a business problem into a reliable product.

```text
Understand Customer Workflow
          ↓
Define Requirements
          ↓
Identify Approved Data Sources
          ↓
Build Ingestion Pipeline
          ↓
Develop Agentic RAG
          ↓
Implement APIs and UI
          ↓
Add Security and Audit
          ↓
Evaluate with Verified Cases
          ↓
Deploy
          ↓
Monitor and Improve
```

This project demonstrates the integration of business requirements, enterprise data, agentic orchestration, application engineering, security, and evaluation.

---

## 15. Conclusion

The Insurance Policy Intelligence Agentic RAG Copilot is a practical enterprise AI portfolio project focused on helping users understand complex insurance documents without relying on unsupported chatbot responses.

By combining policy-aware retrieval, evidence evaluation, metadata filtering, conditional routing, query rewriting, source citations, and human escalation, the project demonstrates how Agentic RAG can support a document-heavy insurance workflow.

**Final takeaway:**

Customer Problem → Policy Data Integration → Agentic Retrieval → Evidence Evaluation → Grounded Answer → Citation and Audit → Human Escalation → Testing → Deployment → Continuous Improvement

**Project status:** Proposed portfolio design. No affiliation, endorsement, or production deployment by HDFC ERGO is implied.
