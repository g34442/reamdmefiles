# GenAI QE Assist — Backend (FastAPI)

This repository contains the **FastAPI backend** for the GenAI QE Assist platform.

## What this service does

The backend orchestrates multiple QE workflows including:
- Test case generation from **Jira**, **free text**, or **requirements documents** (outputs: **BDD/Gherkin** or **qTest**).
- Automation scaffold generation (BDD/Cucumber, Selenium TestNG, Appium TestNG, Playwright).
- API testing artifacts generation from `api-docs.json` and conversion from `testsuite.xml`.
- Test plan generation using templates and configurable sections.
- Performance report analysis from uploaded report artifacts.
- (Planned) agentic test execution.

## User-visible capabilities (mapped to backend responsibilities)

### Test Case Design
- Jira → test cases (BDD/qTest)
- Free Text → test cases (BDD/qTest)
- Requirements Document → test cases (BDD/qTest)
- Optional: export/upload to qTest

### Test Automation
- BDD → Cucumber scaffold + step definitions
- qTest/Jira → Selenium TestNG/Appium TestNG scaffolds
- Test cases → Playwright scaffold (default JS)

### API Testing
- `api-docs.json` → API test cases
- `api-docs.json` → Rest Assured framework
- `testsuite.xml` → Rest Assured conversion

### Test Planning
- Generate test plans from requirements + templates + selected sections

### Performance Testing
- Analyze `summary.html`, `Report5.csv`, `Report3.csv` (in required order) + SLA inputs

## Repository structure (observed)

```text
.github/workflows/
  helios-ct-pipeline.yml
  helios-docker-ocp4-cd-pipeline...
  schedule-embeddings.yml
app/
  RAG/
  database/
  openai_llm/
  utils/
  api_handler.py
  main.py
  playwright_agent.py
  qtest_handler.py
  schemas.py
  server.py
  tdh_handler.py
openshift/
ocp_configs/
Dockerfile
Procfile
requirements.txt
rag_embeddings.py
EMBEDDING_SETUP_GUIDE.md
```

> Some details (exact endpoints, auth flow, exact entry command) **require repository verification**.

## End-to-end request flow (high level)

1. Client UI submits request (Jira/text/file + options)
2. Backend receives request in FastAPI router/handler
3. Input expansion (e.g., Jira ticket expansion) + optional file text extraction
4. Optional retrieval (RAG) to fetch standards/examples
5. LLM prompt chain generation
6. Output validation (format/schema/coverage)
7. Output formatting (BDD vs qTest)
8. Optional qTest upload
9. Response returned to UI

## Local development (minimal)

### Prerequisites
- Python 3.x

### Install
```bash
python -m venv .venv
# activate your venv
python -m pip install -r requirements.txt
```

### Run
> The exact module path for the FastAPI app depends on repo entrypoint configuration.

Try one of the following (verify against Dockerfile/Procfile):
```bash
python -m uvicorn app.main:app --reload --port 8000
```

## Enterprise compliance notes (quick)

- Do **not** log secrets/tokens or sensitive Jira content.
- Treat uploaded files as sensitive data; follow retention and handling guidance.
- If dependency install is blocked by proxy (e.g., HTTP 407), use your organization-approved mirror/proxy configuration.

## Documentation

- VDI setup guide: [VDI_Users_Setup_Guide.md](VDI_Users_Setup_Guide.md)

## Glossary (minimal)

- **BDD/Gherkin**: Feature/Scenario/Given-When-Then structured test format.
- **qTest**: Test management system used for storing and managing test cases.
- **RAG**: Retrieval-Augmented Generation; uses a knowledge base to ground LLM outputs.
- **Embedding**: Vector representation of text used for similarity search.

## Open questions (to finalize accuracy)

- What are the canonical API routes for each capability?
- Is generation synchronous or job-based (job id + polling)?
- What is the production vector store (pgvector/chroma/faiss)?
