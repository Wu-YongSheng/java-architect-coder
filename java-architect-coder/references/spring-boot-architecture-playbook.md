# Spring Boot Architecture Playbook

Use this guide when designing, implementing, refactoring, or reviewing Spring Boot applications.

## Default Layering

- Controller layer: handles HTTP mapping, authentication context extraction when project-local, request validation, response shaping, and delegation.
- Request/response DTOs: define API contracts. Keep them separate from persistence entities and internal domain objects.
- Application/service layer: orchestrates use cases, transaction boundaries, authorization checks, idempotency, domain calls, repository calls, and integration clients.
- Domain layer: owns business rules, state transitions, invariants, policy decisions, and pattern-based variation points.
- Repository/infrastructure layer: isolates persistence, query details, cache access, external clients, messaging, file storage, and framework-specific adapters.
- Configuration layer: owns typed properties, bean wiring, feature flags, and infrastructure options.

Prefer the existing project layout when it is coherent. Introduce a new layer or package only when it creates a clearer boundary for change.

## Transaction Boundaries

- Place `@Transactional` on service/application methods that represent write use cases.
- Keep read-only methods marked as read-only when the project uses that convention.
- Avoid transactions in controllers, DTO mappers, repository helper methods, and remote clients.
- Do not hold a transaction across slow network calls unless the design explicitly accepts the lock and consistency tradeoff.
- For message publishing plus database updates, prefer outbox, transaction synchronization, or project-local reliable event patterns instead of ad hoc publish-after-save code.

## Validation And API Contracts

- Use Bean Validation for transport-level shape constraints.
- Keep business validation in services or domain objects where it can be reused outside HTTP.
- Return project-standard error responses. Do not invent a second error format.
- Version external APIs deliberately when changing request or response semantics.

## Domain Boundaries

- Name packages and abstractions around business capabilities, not only technical layers, when the project already supports that organization.
- Keep cross-domain calls explicit. Avoid circular dependencies between business modules.
- Use domain services or policies when rules do not naturally belong to one entity.
- Use events for meaningful state changes when consumers can evolve independently. Do not use events to hide direct calls inside one tightly coupled use case.

## Idempotency And Consistency

- Identify operations that may be retried by users, queues, schedulers, or remote clients.
- Use idempotency keys, unique constraints, request records, or state-machine guards for duplicate-sensitive writes.
- Prefer database constraints for critical uniqueness and consistency. Application checks alone are race-prone.
- Define retry behavior and compensation for external integration failures.

## Caching

- Cache only data with clear freshness tolerance.
- Define key structure, TTL, invalidation path, serialization shape, and stampede protection for hot keys.
- Avoid caching mutable security or permission decisions without a strong invalidation story.
- Keep cache failure behavior explicit. The app should not silently return wrong data when cache state is stale.

## Async Work And Messaging

- Use async execution for latency isolation or throughput, not to hide slow code.
- Configure bounded executors with named threads, timeout behavior, and error handling.
- For messages, define topic/queue ownership, payload schema, idempotent consumer behavior, retry policy, dead-letter handling, and observability.
- Keep message payloads stable and avoid leaking internal persistence entities.

## Observability

- Log business identifiers at use-case boundaries and external dependency boundaries.
- Emit or preserve trace/request ids according to the project standard.
- Add metrics for important counters, latency, failures, retries, queue lag, and fallback paths when the project has a metrics stack.
- Make operational failures diagnosable without logging sensitive data.

## Testing Slices

- Unit tests: domain rules, policy classes, strategy selection, mappers with branching logic, and service orchestration with mocked ports.
- MVC tests: controller validation, status codes, serialization, auth behavior, and error mapping.
- Repository tests: custom queries, locking behavior, pagination, soft delete filters, and database constraints.
- Integration tests: transaction behavior, event publishing, messaging, cache behavior, and external client adapters when contracts are important.

## Production Readiness Review

- Can the feature be rolled back without data corruption?
- Are migrations backward compatible with the running application version?
- Are timeouts, retries, and circuit-breaking behavior explicit for remote calls?
- Are write paths idempotent where duplicate requests are plausible?
- Are logs, metrics, and error messages enough to operate the feature?
- Does the implementation localize future changes to the expected package or interface?
