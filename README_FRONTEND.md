# GenAI QE Assist — Frontend (Angular 14 + Bootstrap 5)

This repository contains the **Angular 14 + Bootstrap 5 frontend** for the GenAI QE Assist platform.

## What this UI provides

The UI exposes a set of QE "agents" and workflows to:
- Generate test cases from Jira/free text/requirement documents (BDD or qTest).
- Generate automation scaffolds (Cucumber/Selenium/Appium/Playwright).
- Generate API testing artifacts from `api-docs.json` and convert ReadyAPI suites.
- Generate test plans using templates and configurable sections.
- Analyze performance testing reports.

## Main screens / workflows

### Agents → Test Case Design
- **Test Case Generator — Jira**
- **Test Case Generator — Free Text**
- **Test Case Generator — Requirements Document**
- Output format: **BDD** or **qTest**
- Optional: upload generated cases to qTest

### Agents → Test Automation
- BDD → Cucumber scaffold
- qTest/Jira → Selenium TestNG / Appium TestNG
- Test cases → Playwright (default JS)

### Agents → API Testing
- Upload `api-docs.json` to generate test cases or Rest Assured framework
- Upload `testsuite.xml` to convert ReadyAPI → Rest Assured

### Agents → Test Planning
- Project name + requirements + template + section selection

### Agents → Performance Testing
- Upload `summary.html`, `Report5.csv`, `Report3.csv` + SLA inputs

## Repository structure (observed)

```text
.github/workflows/
_tests_/
cypress/
docs/
helios/
node_server/
ocp_configs/
openshift/
src/
angular.json
package.json
proxy.conf.js
karma.conf.js
cypress.config.ts
Dockerfile
docker-entrypoint.sh
ngsw-config.json
```

## End-to-end request flow (high level)

1. User selects a workflow and provides inputs (Jira/text/files)
2. Angular component validates inputs and builds payload
3. Angular service calls backend
4. Interceptors apply cross-cutting behavior (auth headers, error mapping) *(verify in repo)*
5. UI renders results (BDD/qTest) and optionally triggers qTest upload

## Local development (minimal)

### Prerequisites
- Node.js + npm

### Install
```bash
npm ci
```

### Run
```bash
npm start
```

> `npm start` runs Angular dev server with `proxy.conf.js`.

## Enterprise compliance notes (quick)

- Do **not** paste secrets/tokens into UI fields.
- Treat Jira and uploaded documents as sensitive.
- Avoid storing outputs containing sensitive data outside approved locations.

## Documentation

- VDI setup guide: [VDI_Users_Setup_Guide.md](VDI_Users_Setup_Guide.md)

## Glossary (minimal)

- **BDD/Gherkin**: Feature/Scenario/Given-When-Then test format.
- **qTest**: Test management tool used for test case storage and export.
- **Interceptor**: Angular HTTP middleware for auth/logging/error handling.

## Open questions (to finalize accuracy)

- What is the backend base URL and proxy mapping path?
- How does auth work in the browser (token source/expiry/refresh)?
