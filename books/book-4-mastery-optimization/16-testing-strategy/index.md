# DOC 16 — Testing Strategy

**Purpose:** Build confidence in production code

This document teaches testing strategies across all layers of the application.

## Concepts

### Part 1 — Foundations
- [x] [Why Testing Exists](./01-why-testing-exists.md) — The confidence and feedback loop that tests provide
- [ ] Testing Pyramid — The balance between unit, integration, and UI tests in a production app
- [ ] Unit Test Anatomy — Structure of a test: arrange, act, assert

### Part 2 — Testing Async Code
- [x] [Testing Coroutines (runTest)](./04-testing-coroutines.md) — Using runTest to safely execute suspend functions in tests
- [ ] TestDispatcher — Controlling coroutine execution timing in tests with StandardTestDispatcher and UnconfinedTestDispatcher
- [ ] Testing Flow (Turbine) — Using the Turbine library to collect and assert Flow emissions

### Part 3 — Testing Architecture Layers
- [ ] Testing ViewModel — Asserting state emissions from ViewModels using TestDispatcher
- [x] [Fake vs Mock](./08-fake-vs-mock.md) — When to write a fake implementation vs use a mocking library
- [ ] Testing Repository — Verifying repository logic using fake data sources
- [x] [Testing Room (in-memory database)](./10-testing-room.md) — Running Room tests against an in-memory database without the filesystem

### Part 4 — Network Testing
- [ ] Testing Networking (MockWebServer) — Using OkHttp's MockWebServer to simulate API responses in tests

### Part 5 — Compose Testing
- [x] [Testing Compose (ComposeTestRule)](./12-testing-compose.md) — Setting up the Compose test harness with createComposeRule
- [ ] Compose Test Semantics — How Compose exposes UI elements to tests via the semantics tree
- [ ] Compose Test Actions (click, scroll, type) — Driving user interaction in Compose tests
- [ ] Compose Test Assertions — Verifying displayed state using Compose test matchers

### Part 6 — Advanced Testing
- [ ] Integration Testing — Testing multiple real components together without mocks
- [ ] Screenshot Testing — Detecting unintended visual regressions in Compose UI
- [ ] Test Organization (fixtures, factories) — Structuring test helpers and shared test infrastructure
- [ ] Test Data Patterns — Building reusable test data with builders and factories
- [ ] Production Testing Strategy — How large teams organize, maintain, and prioritize their test suites

## Reading Order

Concepts should be read in the order listed because:

1. **Understand why** (Why Testing Exists, Testing Pyramid)
2. **Learn the basics** (Unit Test Anatomy)
3. **Learn async testing** (runTest, TestDispatcher, Turbine)
4. **Test each layer** (ViewModel, Repository, Room, Networking)
5. **Test the UI** (Compose testing from setup to assertions)
6. **Master advanced patterns** (Integration, screenshot, test organization)

## How This Fits Into The Atlas

This document answers: **How do I verify that my systems work correctly?**

After learning to build complete systems (Books 1-3), readers learn how to test them.

The next document, **DOC 17 — Performance & Optimization**, teaches optimization techniques.

---

**Start with:** [Why Testing Exists](./01-why-testing-exists.md)
