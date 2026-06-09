# Design Patterns For Long-Term Java Code

Use design patterns to reduce change cost, not to decorate code. Before introducing a pattern, state the repeated variation or future change pressure, the simpler alternative considered, and the test boundary that proves the abstraction works.

## Selection Rules

- Prefer no pattern when a named method, small class, or enum is enough.
- Prefer the smallest pattern that isolates the volatile decision.
- Prefer patterns already used coherently in the project.
- Avoid adding interfaces with one implementation unless they isolate infrastructure, define a domain port, or protect a near-term variation point.
- Keep pattern names out of class names unless the name improves project readability.
- Test the stable contract and at least two meaningful variants when adding a variation pattern.

## Strategy

- Use when one use case must choose among interchangeable algorithms or business policies.
- Do not use for two tiny branches that are unlikely to grow.
- In Spring Boot, inject a list or map of strategy beans when runtime selection is needed. Keep the selector small and deterministic.
- Long-term risk: too many micro-strategies can hide simple flow. Group related policies when they change together.

## Factory

- Use when object creation depends on business type, external provider, configuration, or complex construction rules.
- Do not use when a constructor or builder is clear enough.
- In Spring Boot, factories can select beans, create domain objects, or adapt request data into commands.
- Long-term risk: factory switch statements can become a second business rule engine. Move behavior into the created types when rules belong there.

## Template Method

- Use when several workflows share a stable sequence with a few controlled extension steps.
- Do not use when the sequence itself varies heavily.
- In Java, prefer composition over inheritance unless the hierarchy is stable and small.
- Long-term risk: inheritance can make behavior hard to trace and test. Keep hooks narrow and documented.

## Adapter

- Use to isolate external APIs, SDKs, legacy systems, file formats, or infrastructure details behind a project-owned interface.
- Do not expose vendor objects across domain or service boundaries.
- In Spring Boot, adapters often live under `infrastructure`, `client`, or project-local equivalent packages.
- Long-term risk: a thin pass-through adapter adds little value. Map errors, timeouts, retries, and data shapes at the boundary.

## Decorator

- Use to add cross-cutting behavior around a stable interface, such as caching, metrics, tracing, validation, or fallback.
- Do not use when Spring AOP, filters, interceptors, or simple composition are already the project standard.
- In Spring Boot, decorators should have explicit bean naming or primary selection to avoid wiring ambiguity.
- Long-term risk: stacked decorators can obscure execution order. Keep the chain visible and tested.

## Chain Of Responsibility

- Use when a request passes through ordered handlers and each handler may accept, reject, enrich, or pass along.
- Do not use when one service method with clear branches is easier to read.
- In Spring Boot, order handlers explicitly with `@Order`, configuration, or a deterministic registry.
- Long-term risk: implicit ordering bugs. Test handler order and stop/continue behavior.

## Specification

- Use when business predicates need composition, reuse, or clear naming, especially for eligibility and policy checks.
- Do not use for one-off boolean helpers.
- Keep specifications side-effect free.
- Long-term risk: mixing persistence query specifications and domain rule specifications can blur boundaries. Name and package them separately.

## State

- Use when behavior depends strongly on lifecycle state and transitions are central to the domain.
- Do not use when an enum plus a transition table is sufficient.
- In Spring Boot, keep state transition rules in domain code and persistence updates in the service transaction boundary.
- Long-term risk: scattered transition rules. Centralize allowed transitions and invalid transition errors.

## Observer Or Event

- Use when other components need to react to a meaningful domain event without coupling the producer to every consumer.
- Do not use to make a synchronous use case harder to follow when direct calls are clearer.
- In Spring Boot, distinguish in-process application events from durable messages. Use durable messaging for cross-service reliability.
- Long-term risk: hidden side effects. Document event payloads, transactional timing, retries, and consumer idempotency.

## Builder

- Use when constructing immutable objects or test fixtures with many optional fields.
- Do not use for simple DTOs with a few fields unless the project standard requires it.
- Prefer generated builders only when the project already uses Lombok or a similar tool.
- Long-term risk: builders can bypass invariants. Validate required fields and domain constraints.

## Facade

- Use to provide a stable use-case API over several lower-level services or infrastructure details.
- Do not use as a dumping ground for unrelated orchestration.
- In Spring Boot, a facade can be useful at module boundaries, integration boundaries, or anti-corruption boundaries.
- Long-term risk: facades can become god services. Split by business capability when responsibilities diverge.

## Pattern Justification Template

When recommending or implementing a pattern, answer:

1. What business or technical variation is expected to change?
2. Why is a simpler method, enum, or small class insufficient?
3. What contract remains stable for callers?
4. Which implementation details can vary independently?
5. What tests prove the abstraction and at least one variant?
