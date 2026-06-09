# Business Requirement To Code Shape

Use this guide when implementing a new feature or turning a vague business request into Java/Spring Boot design. The goal is to choose the simplest code shape that preserves the right future flexibility.

## Requirement Understanding

Before writing code, identify:

- Business capability: what user or system outcome changes?
- Actors: who triggers it, who consumes it, and who owns the data?
- Command or query: does this mutate state, read state, or orchestrate both?
- Invariants: what must always remain true after the operation?
- Lifecycle state: does behavior depend on status, phase, approval, payment, fulfillment, or retry state?
- Variation axes: what is likely to vary by type, channel, tenant, provider, region, rule, product, or time?
- Consistency boundary: which writes must succeed or fail together?
- Idempotency: can the same request, message, or job run twice?
- Failure behavior: retry, compensate, reject, fallback, or manual intervention?
- Observability: what identifiers, metrics, and logs will operators need?

If these answers are unclear and cannot be inferred from the codebase, ask the smallest high-impact question before designing.

## Code Shape Decision

Choose the smallest sufficient shape:

- Simple service method: use when the feature is one use case, has low variation, and existing service ownership is clear.
- Private helper method: use when extracting readability inside one class and no other caller should depend on it.
- Domain method: use when the rule protects an invariant or state transition of a domain object.
- Domain policy or specification: use when a named business rule is reused, composed, or independently tested.
- Application service: use when the code orchestrates repositories, transactions, domain rules, events, and integration clients.
- Interface or port: use when infrastructure can vary, an external dependency must be isolated, tests need a stable boundary, or the domain should not know a vendor/framework.
- Strategy: use when business behavior has multiple interchangeable variants selected by type, channel, tenant, provider, or rule.
- Factory: use when object or strategy creation has meaningful business selection logic.
- Event: use when other components should react to a business fact without coupling the producer to each consumer.
- Facade: use when a module needs a stable entrypoint over several lower-level collaborators.
- Workflow/state machine: use when the feature has explicit lifecycle transitions, retries, compensations, or long-running steps.

Do not create an interface only because "future changes may happen". Create it when the variation axis is visible, the boundary protects ownership, or the abstraction improves testing and replacement.

## Service Method Versus Interface

Prefer a concrete service method when:

- There is one implementation and no credible near-term variation.
- The service belongs clearly to one bounded business capability.
- The behavior does not hide infrastructure or vendor details.
- Tests can verify behavior without awkward mocking.
- An interface would only mirror the class methods.

Prefer an interface, port, or strategy when:

- Multiple implementations already exist or the new requirement introduces real variants.
- The caller should depend on a stable business contract instead of a vendor, SDK, persistence model, or remote API.
- The code must choose behavior by business type, tenant, channel, region, or provider.
- The boundary enables focused tests without loading heavy infrastructure.
- The abstraction prevents duplicate condition chains from spreading.

## Abstraction Quality Gates

Before introducing an abstraction, confirm:

- The interface name describes business capability, not implementation mechanics.
- Methods express use-case intent and stable inputs/outputs.
- The abstraction has at least one clear owner package.
- Implementations are cohesive and do not require callers to know internal branching.
- Tests cover the contract and at least one meaningful implementation path.
- The abstraction reduces coupling or duplication now, not only in an imagined future.

## Avoid Overengineering

- Do not build a plugin system for a one-off rule.
- Do not split every service into interface plus implementation by default.
- Do not introduce DDD tactical patterns unless they clarify actual business invariants.
- Do not convert simple CRUD into a strategy hierarchy.
- Do not create generic utilities for business-specific behavior.
- Do not add events when direct synchronous flow is clearer and consumers are not independent.

## Feature Implementation Output

When proposing or implementing a new feature, report:

- Business understanding: the capability, actor, state change, and invariant.
- Reuse result: existing components found and how they are reused or extended.
- Code-shape decision: service method, domain behavior, interface, strategy, event, or other shape, with rationale.
- Transaction and consistency boundary.
- Failure and idempotency behavior.
- Tests that protect the business rule and abstraction boundary.
