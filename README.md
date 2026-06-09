# Java Architect Coder

一个面向 Java / Spring Boot 项目的 Codex skill，让智能体像架构师一样理解业务需求、阅读现有代码、复用已有能力，并在 Alibaba Java 规约约束下编写可长期维护的代码。

这个 skill 的重点不是“更快生成代码”，而是让 Codex 在写代码前先回答几个架构师会关心的问题：

- 这个需求真正对应什么业务能力？
- 系统里是否已经有类似 service、strategy、mapper、repository、client、validator 或测试夹具？
- 直接加一个 service 方法是否足够？
- 是否需要抽象 interface / port / strategy / event / facade？
- 事务、幂等、失败处理、观测性和测试边界应该放在哪里？
- 新代码是否符合 Alibaba Java 开发规约？

## Capabilities

- **业务感知的新功能设计**：先识别业务目标、参与者、状态变化、不变量、变化轴、事务边界、幂等和失败行为，再决定代码形态。
- **Reuse-first 编码**：写新代码前优先查找和复用已有组件，避免重复造轮子。
- **Graphify 辅助架构理解**：当项目较大或模块关系复杂时，优先利用 `$graphify` 和已有 `graphify-out/graph.json` 查询代码图。
- **Spring Boot 架构分层**：关注 controller、DTO、application service、domain、repository、adapter、client、configuration 等边界。
- **设计模式决策**：支持 Strategy、Factory、Template Method、Adapter、Decorator、Chain of Responsibility、Specification、State、Observer/Event、Builder、Facade，但避免为模式而模式。
- **Alibaba Java 规约硬门槛**：覆盖命名、集合、异常、日志、并发、数据库、事务、DTO/Entity 分离、测试和安全风险。
- **生产可维护性检查**：关注测试、事务、幂等、缓存、消息、远程调用、日志、指标和上线风险。

## Repository Structure

```text
.
+-- LICENSE
+-- README.md
`-- java-architect-coder/
    +-- SKILL.md
    +-- agents/
    |   `-- openai.yaml
    `-- references/
        +-- alibaba-java-hard-gates.md
        +-- business-requirement-to-code-shape.md
        +-- design-patterns-long-term-code.md
        +-- graphify-architecture-integration.md
        `-- spring-boot-architecture-playbook.md
```

## Installation

Copy the `java-architect-coder` directory into your Codex skills directory.

Windows:

```powershell
New-Item -ItemType Directory -Force "$env:USERPROFILE\.codex\skills" | Out-Null
Copy-Item -Recurse -Force ".\java-architect-coder" "$env:USERPROFILE\.codex\skills\java-architect-coder"
```

macOS / Linux:

```bash
mkdir -p ~/.codex/skills
cp -R ./java-architect-coder ~/.codex/skills/java-architect-coder
```

Restart Codex after installation so the skill metadata is discovered.

## Usage

Invoke the skill explicitly:

```text
Use $java-architect-coder to understand this Spring Boot requirement, inspect existing code, and implement it under Alibaba Java conventions.
```

Example prompts:

```text
Use $java-architect-coder to design an order payment feature. Decide whether it should be a simple service method, a strategy, or a domain policy.
```

```text
Use $java-architect-coder to review this Spring Boot module for Alibaba convention violations, duplicated business logic, and long-term architecture risks.
```

```text
Use $java-architect-coder with graphify to trace where user status validation is implemented before adding a new rule.
```

## How It Works

The skill guides Codex through this workflow:

1. Inspect the repository structure, build files, package layout, tests, and existing conventions.
2. Use `$graphify` when a persistent code graph helps answer architecture, dependency, module relationship, or reuse questions.
3. Model the business requirement before writing code.
4. Search for existing components that can be reused, extended, or consolidated.
5. Choose the smallest sufficient code shape: service method, helper, domain behavior, policy/specification, interface/port, strategy, event, workflow, or facade.
6. Implement conservatively with tests where feasible.
7. Self-review against Alibaba Java conventions, Spring Boot boundaries, transaction correctness, logging, failure handling, and production risks.

## Graphify Integration

This skill does not bundle Graphify. It is designed to work with an installed `$graphify` skill.

When a target project already contains `graphify-out/graph.json`, `java-architect-coder` should query that graph before broad manual exploration. This helps Codex discover existing services, strategies, mappers, repositories, clients, validators, enums, constants, exceptions, configuration classes, and tests before creating new code.

## Validation

Validate the skill with the official skill creator validator:

Windows:

```powershell
python "$env:USERPROFILE\.codex\skills\.system\skill-creator\scripts\quick_validate.py" ".\java-architect-coder"
```

macOS / Linux:

```bash
python ~/.codex/skills/.system/skill-creator/scripts/quick_validate.py ./java-architect-coder
```

Expected output:

```text
Skill is valid!
```

## Design Principles

- Prefer reuse before creation.
- Prefer business clarity over framework ceremony.
- Prefer the simplest design that protects known change pressure.
- Do not create interfaces only because future changes might happen.
- Do not turn simple CRUD into a strategy hierarchy.
- Do not let graph analysis replace source inspection, tests, or compilation.
- Treat Alibaba Java conventions as hard gates unless legacy compatibility is explicitly required.

## License

This project is licensed under the MIT License. See [LICENSE](./LICENSE) for details.
