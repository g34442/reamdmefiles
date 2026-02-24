# GenAI QE Assist — Onboarding & Project Overview

> **Audience:** Engineers onboarding to the GenAI QE Assist platform (Backend: FastAPI, Frontend: Angular 14 + Bootstrap 5).  
> **Goal:** Provide a clear, enterprise-friendly overview of what the system does, how it is organized, and how to work safely.

---

## 1. What this product is

GenAI QE Assist is a **multi-capability QE productivity platform** used by a large internal user base (hundreds of users). It helps QE teams:

- **Design test cases** from Jira requirements, free text, or requirement documents.
- **Generate automation scaffolds** (BDD/Cucumber, Selenium TestNG, Appium TestNG, Playwright).
- **Generate API test artifacts** and frameworks (from OpenAPI-like specs) and convert ReadyAPI suites.
- **Generate test plans** using templates and customizable sections.
- **Analyze performance testing reports** using uploaded report artifacts.
- (Planned) **Agentic test execution** (autonomous browser actions and script generation).

This repository set is **enterprise-oriented** with DevOps configuration present and security/compliance expectations.

---

## 2. Key user flows (what the UI supports)

### 2.1 Test Case Design
**A) Test Case Generator — Jira**
- Input: Jira Ticket ID(s) + optional context + optional file uploads
- Output formats: **BDD** (Gherkin) or **qTest**
- Optional: Upload generated cases to qTest

**B) Test Case Generator — Free Text**
- Input: free-text requirement + optional context + optional file uploads
- Output formats: **BDD** or **qTest**

**C) Test Case Generator — Requirements Document**
- Input: requirement document upload + optional context + optional file uploads
- Output formats: **BDD** or **qTest**


### 2.2 Test Automation
- **BDD Automation Framework:** generates Cucumber scaffolding and step definitions from BDD text
- **Selenium TestNG Framework:** generates Selenium + TestNG structure from qTest cases
- **Selenium Framework — Jira:** generates Selenium scaffold from Jira-derived cases
- **Appium TestNG Framework:** generates Appium + TestNG scaffold from qTest cases
- **Playwright Framework Generator:** generates Playwright automation code (default: JavaScript) from test cases


### 2.3 API Testing
- **API Test Case Generator:** generates API test cases from `api-docs.json`
- **Rest Assured Framework:** generates Rest Assured scaffold from `api-docs.json`
- **Convert ReadyAPI to Rest Assured:** converts `testsuite.xml` to Rest Assured


### 2.4 Business Analysis
- Optimizes business requirements from **free text** or **Jira ID**


### 2.5 Test Planning
- Generates test plans from: project name + functional/non-functional requirements
- Supports templates and customizable sections


### 2.6 Performance Testing
- Generates performance analysis from:
  - `summary.html`
  - `Report5.csv`
  - `Report3.csv`
  (uploaded in the required order)
- Requires additional inputs (stabilization time, SLA, pacing, TPS target)


### 2.7 Agentic Test Execution (Coming soon)
- Planned autonomous execution: open browser, execute scenarios, capture screenshots, generate scripts and reports

---

## 3. Repositories & high-level structure

### 3.1 Backend (FastAPI) repository
**Primary purpose:** API orchestration for generation workflows, integrations, retrieval (RAG), export (qTest), and optional automation execution.

Observed top-level structure (based on current repo evidence):

```text
backend/
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
  dast-scan/
  data/
  helios/
  ocp_configs/
  openshift/
  scripts/
  Dockerfile
  EMBEDDING_SETUP_GUIDE.md
  Procfile
  README.md
  docker-entrypoint.sh
  requirements.txt
  rag_embeddings.py
  rbc-ca-certs.pem
  rbc-certs.cer
  run.py
  tests/
```

> **Note:** Actual routing layout (e.g., `APIRouter` modules and endpoints) **requires repository verification**.


### 3.2 Frontend (Angular 14 + Bootstrap 5) repository
**Primary purpose:** UI for selecting capabilities (agents), collecting inputs (Jira/text/files), rendering outputs (BDD/qTest), and triggering exports.

Observed top-level structure (based on current repo evidence):

```text
frontend/
  .github/workflows/
  cypress/
  docs/
  helios/
  node_server/
  ocp_configs/
  openshift/
  src/
  angular.json
  package.json
  package-lock.json
  proxy.conf.js
  karma.conf.js
  cypress.config.ts
  Dockerfile
  docker-entrypoint.sh
  ngsw-config.json
  README.md
```

---

## 4. End-to-end request flow (high level)

A typical generation request follows this sequence:

1. **UI action** (user chooses a capability and submits Jira/text/file inputs)
2. **Angular component** collects form data
3. **Angular service** sends HTTP request
4. **HTTP interceptors** add cross-cutting concerns (auth headers/correlation ID, error mapping) *(requires repo verification)*
5. **FastAPI router/handler** receives request
6. **Dependency chain** resolves config/auth/db/clients *(requires repo verification)*
7. **Workflow orchestration**
   - expand input (e.g., Jira ticket expansion)
   - optional retrieval (RAG)
   - LLM prompt chain generation
   - validations (schema/format/coverage)
8. **Format adapter** produces BDD (Gherkin) or qTest structure
9. Optional: **qTest export** via qTest handler
10. **Response** returned to UI
11. **UI rendering** of output + optional export confirmation

---

## 5. Enterprise compliance & safe usage guidelines

This project runs in an enterprise environment. Follow these rules:

### 5.1 Data handling
- Do **not** paste secrets, credentials, tokens, or client-sensitive data into prompts or logs.
- Treat Jira content and attachments as **sensitive**; follow team guidance for retention and storage.

### 5.2 Logging and telemetry
- Log **metadata**, not sensitive content (avoid request bodies with secrets).
- Use correlation IDs and structured logs where available *(requires repo verification)*.

### 5.3 External connectivity / proxy
- Developer environments may require an enterprise proxy for dependency downloads.
- If `pip install` fails with **HTTP 407 Proxy Authentication Required**, configure pip to use the internal PyPI mirror and approved proxy settings (follow internal guidance).

### 5.4 Certificates
- If TLS inspection is used, corporate CA certificates may be required for HTTPS calls.

---

## 6. Local development (quick start)

> Exact commands can vary by environment and repo configuration. Where unclear, items are marked **requires repository verification**.

### 6.1 Backend
1. Create and activate a virtual environment
2. Install dependencies:
   - `python -m pip install -r requirements.txt`
3. Start the API server:
   - `uvicorn <module>:<app> --reload --port 8000` *(requires repository verification for module path)*

### 6.2 Frontend
1. Install dependencies:
   - `npm ci`
2. Start dev server (uses proxy config):
   - `npm start`
3. Open UI:
   - `http://localhost:4200`

### 6.3 Frontend ↔ Backend connectivity
- The frontend uses `proxy.conf.js` to route API calls to the backend in development.
- Verify the proxy routes match the backend base paths *(requires repository verification)*.

---

## 7. Testing

### Backend
- Unit/integration tests are expected under `tests/`.
- Run:
  - `pytest` *(requires repository verification for exact command options)*

### Frontend
- Unit tests:
  - `npm test`
- Lint:
  - `npm run lint`
- E2E:
  - `npm run e2e`

---

## 8. Operational notes (production mindset)

- Scheduled jobs exist for embeddings refresh (see workflow `schedule-embeddings.yml`).
- Deployment assets for OpenShift exist under `openshift/` and `ocp_configs/`.
- Any changes that affect: **auth**, **export to qTest**, **Jira integration**, **RAG index**, or **pipelines** should be treated as **high risk** and tested thoroughly.

---

## 9. Documentation links

- **VDI setup guide:** [`VDI_Users_Setup_Guide.md`](VDI_Users_Setup_Guide.md)
  - Location and content **to be confirmed** (you will provide guidance later).

---

## 10. “Requires repository verification” items

The following details must be confirmed from the actual code/config:

- Backend entrypoint command (Dockerfile/Procfile exact run command)
- Actual FastAPI route paths for each capability
- Auth mechanism used end-to-end (JWT/OAuth2/LDAP specifics)
- Interceptor responsibilities on frontend (auth, correlation, retry)
- Vector store backend used in practice (pgvector vs chroma vs faiss)
- qTest export payload mapping and idempotency strategy

---

## 11. Onboarding checklist (for new engineers)

- [ ] Access: repo + environment + VDI
- [ ] Run frontend locally and reach the UI
- [ ] Run backend locally and reach Swagger/OpenAPI *(requires repository verification for URL)*
- [ ] Perform one known flow (Free Text → BDD) end-to-end
- [ ] Identify where prompts, validators, and export adapters live
- [ ] Make one small PR (docs/test/low-risk improvement)

---

## Questions for the maintainer (to finalize this README)

1. What are the **actual backend base URLs** and key endpoints for each capability?
2. Is generation **synchronous** or **async job-based** (job id + polling)?
3. What is the **official vector store** in production (pgvector/chroma/faiss)?
4. What is the **official auth** approach (JWT, OAuth2, LDAP, SSO)?
5. Where should `VDI_Users_Setup_Guide.md` live in the repo (root? docs/?), and what should it contain?

