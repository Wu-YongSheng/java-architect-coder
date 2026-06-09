# Graphify Architecture Integration

Use `$graphify` as the architecture map for Java/Spring Boot projects when text search alone is not enough. Do not copy graphify's workflow into this skill; invoke or follow the installed `$graphify` skill so its current behavior stays authoritative.

## When To Use Graphify

- Use it for broad codebase architecture questions, module relationships, file relationships, dependency paths, ownership boundaries, and unfamiliar large projects.
- Use it when the user asks questions such as "what calls this", "trace this flow", "how are these modules connected", "where is this concept implemented", or "what are the god nodes".
- Use it before proposing major refactors when the blast radius is unclear.
- Use it during code review when a finding depends on cross-module coupling or hidden dependencies.
- Skip it for small, local edits where `rg` and direct file reads give enough context.

## Existing Graph First

- If `graphify-out/graph.json` exists in the target project and the user asks a natural-language question about the codebase, query the existing graph first.
- Do not rebuild an existing graph unless the user asked for an update, the graph is stale for the current task, or the relevant files are absent from the graph.
- Use graph answers as navigation aid, then inspect the cited source files before making code changes.

## Reuse-First Coding

- Before writing new Java code, use the graph and source search to find existing implementations of the same capability.
- Look specifically for existing services, ports, adapters, strategies, factories, validators, mappers, repositories, clients, DTOs, enums, constants, exception types, configuration properties, and test fixtures.
- Query the graph with intent-level questions, such as "where is order status validation implemented", "what components call PaymentClient", "what strategies implement discount calculation", or "which mapper converts UserEntity".
- Prefer extending an existing variation point over creating a parallel abstraction.
- Prefer consolidating duplicate logic when the requested change touches an already duplicated area and the consolidation is small enough for the task.
- Create a new component only after confirming that existing components have incompatible ownership, wrong abstraction level, unsafe side effects, or would become less cohesive if extended.
- In the implementation summary, explicitly list the reuse decision: reused, extended, consolidated, or created new.

## New Graph Guidance

- Suggest or run `$graphify <project-path> --mode deep` for large, unfamiliar Java projects where architectural relationships matter.
- For normal Java/Spring Boot architecture discovery, a default `$graphify <project-path>` run is enough.
- For dependency direction questions, prefer directed graph mode when the underlying graphify version supports it.
- Warn the user before running graphify on very large repositories if the graphify detection step reports a large corpus or asks to narrow scope.

## Architect Workflow With Graphify

1. Build or query the graph to identify modules, god nodes, surprising connections, and likely boundaries.
2. Use graph paths to locate source files and relationship evidence.
3. Identify existing components that may already solve the requested problem.
4. Read the actual Java/Spring code before deciding architecture changes.
5. Combine graph evidence with Alibaba hard gates, Spring Boot boundaries, transaction rules, and design-pattern selection.
6. In the final answer, distinguish graph-derived observations from direct code inspection.

## Guardrails

- Never invent graph edges or relationships that graphify did not report.
- Treat inferred or ambiguous graph relationships as leads, not proof.
- Do not let the graph replace tests, compilation, or source inspection.
- Do not add abstractions only because the graph looks dense; use density as a signal to investigate coupling and change pressure.
- Do not create a duplicate component just because it is faster than understanding the existing one.
