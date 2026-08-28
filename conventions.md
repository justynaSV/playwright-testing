# Playwright Conventions

> Placeholder — to be filled in. Sections below are the intended scope; add content under each as decisions are made.

## 1. Project structure

### Directory Roles & Architectural Responsibilities

- tests/e2e/: Contains End-to-End test specs (\*.spec.ts) validating critical user journeys via the UI. Focuses exclusively on business assertions and scenarios without handling low-level selectors.

- tests/api/: Contains standalone API test specs (\*.spec.ts). Utilizes Playwright's native request context to validate backend endpoints directly without initializing a browser instance.

- src/pages/: Holds Page Object Model classes (e.g., login.page.ts, checkout.page.ts). Encapsulates element locators and page-level interactions. Strict rule: Contains zero expect() assertions.

- src/api/: Holds API controllers and request helpers (e.g., auth.controller.ts). Used both for API testing and for ultra-fast state preparation (Arrange phase) in E2E tests.

src/fixtures/: Serves as the backbone of dependency injection. Extends Playwright’s native test object via test.extend() to provide fully typed Page Objects and API clients directly into test signatures, eliminating manual new Page() instantiations.

- .github/: Houses team-wide AI instructions to ensure generated code automatically complies with project architectural standards.

  .
  ├── .github/
  │ └── copilot-instructions.md # AI coding guidelines & repository standards
  ├── src/
  │ ├── api/ # API clients, payload builders, and request helpers
  │ ├── fixtures/ # `test.extend()` custom fixtures (POM & API injection)
  │ └── pages/ # Page Object Model classes (UI element mapping)
  ├── tests/ # Default Playwright root test directory
  │ ├── api/ # Backend API test specifications (_.spec.ts)
  │ └── e2e/ # End-to-End user flow specifications (_.spec.ts)
  ├── .env.example # Template for environment-specific variables
  ├── .gitignore # Git exclusions for dependencies and test artifacts
  ├── package.json # Project scripts and dependencies
  ├── playwright.config.ts # Global Playwright configuration file
  └── tsconfig.json # TypeScript compiler configuration

## 2. Naming conventions

_TBD: spec file names, test titles, describe block structure._

## 3. Locator strategy

_TBD: preferred locator priority (e.g. role/testid over CSS/XPath), rules for adding `data-testid`._

## 4. Page Object Model / Fixtures

_TBD: whether to use POM, fixtures, or both; how shared setup is structured._

## 5. Waiting & assertions

_TBD: auto-waiting expectations, avoiding hard waits, preferred assertion style (`expect` matchers)._

## 6. Test data & environment handling

_TBD: fixtures vs inline data, environment/config handling, avoiding hardcoded secrets._

## 7. Tagging & parallelization

_TBD: tag conventions for CI (e.g. `@smoke`, `@regression`), sharding/parallel run setup._

## 8. CI integration

_TBD: how tests run in CI, reporting, retries policy._
