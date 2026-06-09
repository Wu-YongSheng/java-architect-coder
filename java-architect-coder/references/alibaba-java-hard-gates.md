# Alibaba Java Hard Gates

Use this checklist as mandatory review guidance for Java/Spring Boot code. It is a practical enforcement guide, not a copy of the Alibaba Java Development Manual.

## Naming And Structure

- Use meaningful English identifiers. Avoid pinyin, unclear abbreviations, and names that hide business meaning.
- Class names use `UpperCamelCase`; methods, fields, and local variables use `lowerCamelCase`; constants use `UPPER_SNAKE_CASE`.
- Boolean fields avoid confusing `is` prefixes when serialization frameworks may generate ambiguous getters.
- Package names are lowercase and stable. Do not mix unrelated business domains in one package.
- Keep DTO, entity, domain object, request, response, and persistence model roles distinct. Do not expose persistence entities directly as API contracts.

## Constants And Magic Values

- Replace repeated literals, status codes, timeouts, cache keys, topic names, and error codes with named constants or enums.
- Use enums for closed business state sets. Keep enum behavior close to the enum when it improves readability.
- Do not store mutable objects in public constants.

## Collections And Null Handling

- Return empty collections instead of `null` for collection results unless `null` has explicit business meaning.
- Check collection emptiness with project-standard utilities or `isEmpty`; do not rely on size comparisons for intent.
- Use generics precisely. Avoid raw types and unchecked casts.
- Do not modify a collection while iterating unless using a safe iterator or collection-specific method.
- Avoid `Optional` as an entity field, DTO field, or method parameter. Use it sparingly for return values when it clarifies absence.

## Exceptions And Error Handling

- Do not swallow exceptions. Either handle them with a business-safe fallback or wrap them with useful context.
- Do not catch `Exception` or `Throwable` broadly unless the boundary is intentional and logs or maps the failure correctly.
- Use domain-specific exceptions or project-standard error codes for business failures.
- Preserve the original exception as the cause when wrapping.
- Avoid using exceptions for normal control flow.

## Logging

- Use parameterized logging instead of string concatenation.
- Include enough context to diagnose the failure, such as business id, request id, user id, or external dependency name.
- Never log passwords, tokens, credentials, private keys, full identity numbers, or sensitive payloads.
- Avoid noisy info logs in hot paths. Use debug logs for detailed diagnostic data.
- Log exceptions with the stack trace at the owning boundary. Do not repeatedly log and rethrow through every layer.

## Concurrency

- Do not create thread pools with `Executors` factory shortcuts for production paths. Use explicit `ThreadPoolExecutor` configuration with bounded queues and named threads.
- Ensure shared mutable state is protected by immutability, confinement, locks, concurrent collections, or atomic types.
- Avoid blocking calls inside common pool tasks or event loops.
- Define timeout, retry, cancellation, and back-pressure behavior for async or parallel work.
- Make scheduled jobs idempotent when retries or overlapping execution are possible.

## Database And Transactions

- Put transaction boundaries at service/application orchestration methods, not controllers.
- Keep transactions short. Do not include slow remote calls inside a database transaction unless there is a deliberate consistency reason.
- Avoid N+1 queries. Use batch queries, joins, or explicit loading strategies.
- Use pagination for unbounded queries.
- Keep query conditions indexed when they are expected to run on large tables.
- Do not concatenate SQL with untrusted input. Use parameter binding.
- Make data updates idempotent when external retries are possible.

## Spring Boot Boundaries

- Controllers validate transport input and delegate. They should not contain business orchestration.
- Services express use cases and transaction boundaries. Avoid god services with unrelated responsibilities.
- Repositories hide persistence details and should not leak ORM-specific behavior into controllers.
- Integration clients isolate remote API details, timeouts, retries, and error mapping.
- Configuration properties should be typed and validated.

## Tests

- Add unit tests for domain rules, branching behavior, and selected design pattern boundaries.
- Add slice or integration tests for controller contracts, repository queries, transactions, and serialization behavior when those are touched.
- Tests should assert business outcomes and important side effects, not private implementation details.
- Cover failure paths for exceptions, remote dependency failures, duplicate requests, and invalid input.

## Must Fix Before Completion

- Swallowed exceptions or lost root causes.
- Unsafe thread pools, unbounded queues, unnamed threads, or uncontrolled async work.
- Missing or misplaced transaction boundaries for write use cases.
- Persistence entities exposed directly as external API contracts.
- Sensitive data in logs.
- SQL injection risk or unbounded data queries.
- Business state represented by scattered magic strings or duplicated condition chains.
- New code with no meaningful tests when behavior is non-trivial.
