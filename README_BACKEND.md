# GenAI QE Assist — Backend (FastAPI)

This repository contains the **FastAPI backend** for the GenAI QE Assist platform (or) **Digital Mini Bot(DMB)**

## Features

The backend has multiple QE workflows including:
- Test case generation from **Jira**, **free text**, or **requirements documents** (outputs: **BDD/Gherkin** or **qTest**).
- Automation scaffold generation (BDD/Cucumber, Selenium TestNG, Appium TestNG, Playwright).
- API testing artifacts generation from `api-docs.json` and conversion from `testsuite.xml`.
- Test plan generation using templates and configurable sections.
- Performance report analysis from uploaded report artifacts.

## User-visible agents (mapped to backend responsibilities)

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

## End-to-end request flow (high level)

1. Client UI submits request (Jira/text/file + options)
2. Backend receives request in FastAPI router/handler
3. Input expansion (e.g., Jira ticket expansion) + optional file text extraction
4. Optional retrieval (RAG) to fetch standards/examples
5. LLM prompt chain generation
6. Output validation (pydantic)
7. Output formatting (BDD vs qTest)
8. Response returned to UI

## Local development

### Prerequisites
- Python 3.x
- pip 26.x

## Getting Started

### 1. Clone the Repository

```bash
git clone <repository-url>
# change the directory
cd <>
```

### 2. Install
```bash
# create virtual environment
python -m venv .venv
# After activating your venv install the packages
python -m pip install -r requirements.txt
```
### 3. Configure Environment Variables

Set the environment variables

### 4. Run command
```bash
python -m uvicorn app.main:app --reload --port 8000
```

## Development Team @RBC
