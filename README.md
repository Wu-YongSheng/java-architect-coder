# Java Architect Coder

一个面向 Java / Spring Boot 项目的 Codex skill，让智能体像架构师一样理解业务需求、阅读现有代码、复用已有能力，并在 Alibaba Java 规约约束下编写可长期维护的代码。

这个 skill 的重点不是“更快生成代码”，而是让 Codex 在写代码前先回答几个架构师会关心的问题：

- 这个需求真正对应什么业务能力？
- 系统里是否已经有类似 service、strategy、mapper、repository、client、validator 或测试夹具？
- 直接加一个 service 方法是否足够？
- 是否需要抽象 interface / port / strategy / event / facade？
- 事务、幂等、失败处理、观测性和测试边界应该放在哪里？
- 新代码是否符合 Alibaba Java 开发规约？

## 核心能力

- **业务感知的新功能设计**：先识别业务目标、参与者、状态变化、不变量、变化轴、事务边界、幂等和失败行为，再决定代码形态。
- **复用优先编码**：写新代码前优先查找和复用已有组件，避免重复造轮子。
- **Graphify 辅助架构理解**：当项目较大或模块关系复杂时，优先利用 `$graphify` 和已有 `graphify-out/graph.json` 查询代码图。
- **Spring Boot 架构分层**：关注 controller、DTO、application service、domain、repository、adapter、client、configuration 等边界。
- **设计模式决策**：支持 Strategy、Factory、Template Method、Adapter、Decorator、Chain of Responsibility、Specification、State、Observer/Event、Builder、Facade，但避免为模式而模式。
- **Alibaba Java 规约硬门槛**：覆盖命名、集合、异常、日志、并发、数据库、事务、DTO/Entity 分离、测试和安全风险。
- **生产可维护性检查**：关注测试、事务、幂等、缓存、消息、远程调用、日志、指标和上线风险。

## 目录结构

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

## 安装方式

把 `java-architect-coder` 目录复制到你的 Codex skills 目录。

Windows：

```powershell
New-Item -ItemType Directory -Force "$env:USERPROFILE\.codex\skills" | Out-Null
Copy-Item -Recurse -Force ".\java-architect-coder" "$env:USERPROFILE\.codex\skills\java-architect-coder"
```

macOS / Linux：

```bash
mkdir -p ~/.codex/skills
cp -R ./java-architect-coder ~/.codex/skills/java-architect-coder
```

安装后重启 Codex，让 Codex 重新发现 skill 元数据。

## 使用方式

显式调用这个 skill：

```text
使用 $java-architect-coder 理解这个 Spring Boot 需求，检查现有代码，并在 Alibaba Java 规约下实现它。
```

示例提示词：

```text
使用 $java-architect-coder 设计一个订单支付功能，判断它应该是简单 service 方法、strategy，还是 domain policy。
```

```text
使用 $java-architect-coder 审查这个 Spring Boot 模块，检查 Alibaba 规约问题、重复业务逻辑和长期架构风险。
```

```text
使用 $java-architect-coder 和 graphify 追踪用户状态校验在哪里实现，然后再添加一条新规则。
```

## 工作方式

这个 skill 会引导 Codex 按下面的流程工作：

1. 检查仓库结构、构建文件、包结构、测试和已有约定。
2. 当代码图能帮助理解架构、依赖、模块关系或复用点时，使用 `$graphify`。
3. 写代码前先对业务需求建模。
4. 查找可以复用、扩展或合并的现有组件。
5. 选择最小且足够的代码形态：service method、helper、domain behavior、policy/specification、interface/port、strategy、event、workflow 或 facade。
6. 在可行时先补测试，再保守实现。
7. 根据 Alibaba Java 规约、Spring Boot 边界、事务正确性、日志、失败处理和生产风险做自查。

## Graphify 集成

这个 skill 不内置 Graphify，而是设计为配合已安装的 `$graphify` skill 使用。

当目标项目已经存在 `graphify-out/graph.json` 时，`java-architect-coder` 应优先查询已有代码图，再进行大范围人工搜索。这样可以帮助 Codex 在创建新代码前发现已有的 service、strategy、mapper、repository、client、validator、enum、constant、exception、configuration class 和测试。

## 校验方式

使用官方 skill creator 校验器检查 skill 是否有效。

Windows：

```powershell
python "$env:USERPROFILE\.codex\skills\.system\skill-creator\scripts\quick_validate.py" ".\java-architect-coder"
```

macOS / Linux：

```bash
python ~/.codex/skills/.system/skill-creator/scripts/quick_validate.py ./java-architect-coder
```

预期输出：

```text
Skill is valid!
```

## 设计原则

- 先复用，再创建。
- 业务清晰优先于框架仪式感。
- 使用能保护已知变化压力的最简单设计。
- 不要仅仅因为“未来可能变化”就创建 interface。
- 不要把简单 CRUD 做成 strategy 体系。
- 不要让代码图分析替代源码检查、测试或编译。
- 除非明确要求兼容遗留代码，否则 Alibaba Java 规约是硬门槛。

## 开源协议

本项目使用 MIT License，详见 [LICENSE](./LICENSE)。
