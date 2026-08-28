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

```text
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
```
## 2. Naming conventions

### File Naming & Directory Conventions

#### General Rules

- **Strict `kebab-case`**: Use lowercase letters separated by hyphens for all file and directory names (e.g., `user-profile.page.ts`, never `UserProfilePage.ts` or `userProfilePage.ts`). This guarantees cross-platform OS compatibility (Linux CI vs. local macOS/Windows).
- **Functional Suffixes**: Every file must explicitly declare its architectural role using a standardized suffix before the TypeScript extension (`.spec.ts`, `.page.ts`, `.component.ts`, `.fixture.ts`, `.api.ts`).

## File Naming Conventions

| Category          | Name Pattern                    | Example                                | Description                                      |
| ----------------- | ------------------------------- | -------------------------------------- | ------------------------------------------------ |
| **E2E Tests**     | `<module>.<scenario>.spec.ts`   | `checkout.payment-credit-card.spec.ts` | UI user journeys grouped by module and scenario  |
| **API Tests**     | `<entity>.<operation>.spec.ts`  | `users.create-account.spec.ts`         | Standalone backend endpoint test specifications  |
| **Page Objects**  | `<page-name>.page.ts`           | `shopping-cart.page.ts`                | Encapsulates full-page locators and actions      |
| **UI Components** | `<component-name>.component.ts` | `header-navigation.component.ts`       | Reusable UI sub-components shared across pages   |
| **API Clients**   | `<service-name>.api.ts`         | `orders-service.api.ts`                | HTTP request controllers located in `src/api/`   |
| **Fixtures**      | `<area>.fixture.ts`             | `authenticated-user.fixture.ts`        | Custom `test.extend()` setups in `src/fixtures/` |

#### Multi-Application Directory Structure

When testing multiple applications within a single repository, group test specs by application directory while maintaining flat, standardized file names:

```text
tests/
├── e2e/
│   ├── customer-portal/
│   │   ├── auth.login-by-email.spec.ts
│   │   └── checkout.stripe-payment.spec.ts
│   └── admin-panel/
│       ├── users.deactivate-user.spec.ts
│       └── reports.generate-pdf.spec.ts
└── api/
    ├── customer-portal/
    │   └── cart.add-item.spec.ts
    └── admin-panel/
        └── metrics.get-daily.spec.ts
```

## 3. Locator strategy

- **Primary strategy: `getByTestId`**: Use stable, purpose-specific `data-testid` attributes as the preferred locator when available.
- **User-facing fallback**: When a test id is unavailable, use locators in this order: `getByRole`, `getByLabel`, `getByText`, then `getByPlaceholder`.
- **Avoid implementation details**: Do not use CSS selectors or XPath unless no stable test id or user-facing locator can represent the target.
- **Keep locators short**: Avoid unnecessary locator chaining. Add a `data-testid` when an element cannot be identified reliably through the preferred strategies.

## 4. Page Object Model / Fixtures

- **Page Object Model**: Store page-level locators and interactions in `src/pages/` classes. Page Objects expose locators and perform actions, but contain no `expect()` assertions.
- **Reusable components**: Put shared UI areas in component classes using the `.component.ts` suffix.
- **Dependency injection**: Provide Page Objects and API clients through typed Playwright fixtures in `src/fixtures/` using `test.extend()`.
- **Test setup**: Use API clients from `src/api/` to prepare state during the Arrange phase of E2E tests. Tests must not manually instantiate Page Objects when an appropriate fixture exists.
- **Test responsibilities**: Keep business scenarios and assertions in `.spec.ts` files; keep selectors and low-level UI operations in Page Objects or components.

## 5. Waiting & assertions

- **Auto-waiting**: Rely on Playwright's built-in waiting and web-first assertions for synchronization.
- **No arbitrary waits**: Never use `page.waitForTimeout()` or other fixed-duration sleeps.
- **Web-first assertions**: Use awaited, auto-retrying `expect` matchers such as `toBeVisible()`, `toHaveText()`, and `toHaveURL()`.
- **Assertion location**: Keep assertions in test specifications, not in Page Objects, components, API helpers, or fixtures.
- **Actions**: Always await Playwright actions such as `click()` and `fill()`.

## 6. Test data & environment handling

- **State preparation**: Prefer API requests through typed clients in `src/api/` for creating users, records, and other preconditions. Use UI interactions only when the UI behavior itself is under test.
- **Data ownership**: Keep reusable setup data and authenticated state in fixtures; keep scenario-specific values close to the test when they are not shared.
- **Test isolation**: Each test creates or provisions the state it needs and must not depend on data left by another test.
- **Environment configuration**: Read environment-specific settings from Playwright configuration or environment variables. Keep `.env.example` updated with required variable names and safe placeholder values.
- **Secrets**: Never commit credentials, tokens, or other secrets, and never hardcode them in tests or Page Objects.
- **Type safety**: Use explicit TypeScript types for test data, API payloads, and fixture values. Do not use `any`.

## 7. Tagging & parallelization

- **Tags**: Use Playwright tags such as `@smoke` and `@regression` to identify suites used by CI and local development.
- **Tag meaning**: Apply `@smoke` to a small set of critical journeys and `@regression` to broader coverage. A test may have more than one tag when both meanings apply.
- **Isolation requirement**: Tests must be independent and safe to run in any order, in parallel, or on separate shards.
- **Parallel execution**: Prefer parallel execution for independent tests. Avoid shared mutable accounts, records, files, or other external state unless a fixture explicitly isolates them.
- **Sharding**: CI may distribute tests across shards; tests must not rely on execution order or a particular worker.

## 8. CI integration

- **Test scope**: Run API tests from `tests/api/` without launching a browser and run E2E tests from `tests/e2e/` through the configured Playwright project.
- **Selective runs**: Use tags to run the smoke suite on fast feedback paths and the regression suite on broader validation paths.
- **Retries**: Configure retries explicitly for CI. Retries are a diagnostic safeguard, not a substitute for fixing flaky tests.
- **Reporting**: Publish the configured Playwright report and retain traces, screenshots, and video when a test fails, according to the CI retention policy.
- **Failure handling**: CI failures should include the failed test, assertion details, and available Playwright artifacts so the failure can be reproduced locally.
- **Environment parity**: CI must provide all required environment variables through secret or configuration management and must not depend on committed secrets.
