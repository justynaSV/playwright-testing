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

```

```
