# 🧠 AI 软件工程技能仓库

> **一个人，就是一个团队。**
> 
> 让 AI Agent 模拟完整软件研发团队，从需求分析到部署上线，一条龙搞定。

---

## 🤔 你是不是也遇到过这些痛？

- 💸 **预算有限，招不起人** —— 全栈开发已经够累了，还要兼职产品、测试、运维？
- 🕐 **项目太赶，沟通成本爆炸** —— 跟真人协作，等回复、等评审、等排期，时间都花在"等"上了
- 🤯 **上下文丢失，反复解释** —— 每次换 AI 工具对话，都要从头说一遍需求，烦不烦？
- 📝 **文档形同虚设** —— 写了一堆文档没人看，代码才是"唯一真实来源"（然后半年后自己都看不懂）

**如果你点了头，那这个仓库就是为你准备的。**

---

## 🎯 这是什么？

这是一套 **企业级 AI Agent 技能仓库**，专为 **中文开发者** 打造。

它让你可以用 **一个 Prompt**，驱动多个 AI Agent（TRAE、Claude Code、Cursor Agent、Codex、Gemini CLI、OpenHands 等），**接力式**地完成整个软件研发流程：

```
产品经理 → UI设计师 → 架构师 → 数据库工程师 → 后端 → 前端 → AI工程师 → 测试 → 安全 → 代码评审 → 运维 → 上线
```

**你只需要做决策，AI 负责执行。**

---

## 🚀 为什么适合中国宝宝体质？

| 痛点  | 传统方式         | 用这个仓库                  |
| --- | ------------ | ---------------------- |
| 招聘  | 招人难、养人贵、留人更难 | 13 个 AI Agent 角色，零人力成本 |
| 沟通  | 等消息、开会、扯皮    | 文档驱动，AI 之间自动交接，无需等待    |
| 时间  | 项目周期动辄数月     | 并行 + 接力，**效率提升 3-5 倍** |
| 质量  | 代码风格全靠自觉     | 13 本共享规范，代码像一个人写的      |
| 文档  | 永远在"补文档"的路上  | 文档即源码，写完代码文档自动完整       |
| 成本  | 烧钱如流水        | 你只需要 **API Token 费用**  |

---

## 📦 仓库里有什么？

```text
TraeSkill/
├── SKILL.md                      ★ TRAE Skill 入口（YAML 元数据 + 协调指令）
├── README.md                     ★ 你正在看的这个文件 — 仓库总览
├── workflow.md                   ★ 全流程工作流定义
│
├── skills/                       ★ 13 个 AI Agent 技能手册（每个 ≥ 300 行）
│   ├── product-manager.md        产品经理 — 需求分析、PRD 撰写、优先级排序
│   ├── ui-ux-designer.md         UI/UX 设计师 — 交互设计、设计系统、原型输出
│   ├── solution-architect.md     系统架构师 — 架构设计、技术选型、架构评审
│   ├── database-engineer.md      数据库工程师 — 数据建模、Schema 设计、性能优化
│   ├── backend-engineer.md       后端工程师 — API 开发、业务逻辑、服务端实现
│   ├── frontend-engineer.md      前端工程师 — 组件开发、页面实现、状态管理
│   ├── ai-engineer.md            AI 工程师 — Prompt 工程、RAG 管线、模型集成
│   ├── qa-engineer.md            测试工程师 — 测试策略、自动化测试、缺陷管理
│   ├── security-engineer.md      安全工程师 — 安全审计、漏洞扫描、安全加固
│   ├── code-reviewer.md          代码评审工程师 — 代码审查、规范检查、重构建议
│   ├── devops-engineer.md        运维工程师 — CI/CD、容器化、部署监控
│   ├── project-manager.md        项目经理 — 进度跟踪、风险管理、资源协调
│   └── tech-lead.md              技术负责人 — 技术决策、架构评审、技术债务管理
│
├── shared/                       ★ 13 本共享规范 — 所有 Agent 共同遵守
│   ├── coding-standard.md        编码规范（TypeScript / Python / Java / Go）
│   ├── api-standard.md           REST API 设计规范
│   ├── database-standard.md      数据库设计规范
│   ├── git-standard.md           Git 分支与提交规范
│   ├── testing-standard.md       测试规范
│   ├── deployment-standard.md    部署规范
│   ├── review-standard.md        代码评审规范
│   ├── documentation-standard.md 文档编写规范
│   ├── decision-log-standard.md  技术决策记录规范
│   ├── task-standard.md          任务管理规范
│   ├── context-standard.md       上下文管理规范
│   ├── memory-standard.md        记忆管理规范
│   └── handoff-standard.md       Agent 交接规范
│
├── templates/                    ★ 8 个文档模板 — 拿来即用
│   ├── prd-template.md           产品需求文档模板
│   ├── architecture-template.md  架构设计文档模板
│   ├── api-template.md           API 设计文档模板
│   ├── database-template.md      数据库设计文档模板
│   ├── test-template.md          测试计划文档模板
│   ├── deployment-template.md    部署计划文档模板
│   ├── decision-log-template.md  决策记录文档模板
│   └── todo-template.md          任务清单模板
│
└── examples/                     ★ 完整示例项目 — 可直接参考
    └── sample-project/           TaskFlow 任务管理系统
        ├── README.md             项目说明
        ├── prd.md                产品需求文档
        ├── architecture.md       架构设计文档
        ├── api-spec.md           API 规范文档
        ├── database-schema.md    数据库 Schema 文档
        ├── test-plan.md          测试计划文档
        ├── deployment-plan.md    部署计划文档
        ├── decision-log.md       决策记录文档
        └── todo.md               任务清单
```

### 各目录作用说明

| 目录            | 作用                                     | 谁在用               |
| ------------- | -------------------------------------- | ----------------- |
| `skills/`     | 每个角色的"岗位说明书"，定义角色的使命、职责、输入输出、工作流程、常见错误 | 每个 AI Agent 激活时读取 |
| `shared/`     | 所有角色共享的编码规范和流程标准，保证 13 个角色写出来的代码像一个人写的 | 所有 AI Agent 开发时参考 |
| `templates/`  | 标准化文档模板，保证输出格式统一、内容完整                  | 各角色生成交付物时使用       |
| `examples/`   | 一个完整的示例项目，展示从 PRD 到上线的完整产出             | 新人学习、AI Agent 参考  |
| `workflow.md` | 整体工作流定义，包括 Phase 划分、角色启动顺序、并行策略        | 项目经理 / 技术负责人调度时使用 |

**数据概览：**

- **45 个 Markdown 文件**
- **17,000+ 行企业级文档**
- **13 个 AI Agent 角色，每个 ≥ 300 行岗位手册**
- **13 本共享规范，统一代码质量**
- **8 个文档模板，开箱即用**
- **1 个完整示例项目（TaskFlow），可直接参考**

---

## 🧩 设计哲学

### 核心理念

| 理念                    | 说明                                                            |
| --------------------- | ------------------------------------------------------------- |
| **📄 文档驱动开发 (DDD)**   | Markdown 是唯一真实来源（Source of Truth）。所有 Agent 必须从文档读取、分析、更新文档、交接 |
| **🤖 AI Agent First** | 所有流程、规范、模板均面向 AI Agent 设计，而非人类开发者                             |
| **👤 人在回路 (HITL)**    | 关键决策点必须由人类确认，AI Agent 提供建议但不可越权                               |
| **🤝 多 Agent 协作**     | 每个 Agent 担任单一角色，遵循 SRP（单一职责原则），通过文档交接协作                       |
| **🔗 高内聚低耦合**         | 每个 Skill 高度内聚，Skill 之间通过共享文档松耦合                               |
| **🧱 SOLID**          | 遵循 SOLID 原则，确保 Repository 可扩展、可维护                             |
| **🧹 KISS**           | 避免过度设计，每个 Skill 聚焦核心职责                                        |
| **📋 DRY**            | 共享内容放入 `shared/` 目录，避免重复定义                                    |
| **✂️ YAGNI**          | 只包含经过验证的实践，不添加推测性内容                                           |

### 核心规则就一条

> 读取 Markdown → 分析 → 设计 → 实现 → 更新 Markdown → 交接

### 每个 Agent 的标准工作流

```
1. 读取输入文档（Inputs）
2. 读取参考文档（Referenced Documents）
3. 读取共享规范（Shared Standards）
4. 分析任务（Analysis）
5. 设计方案（Design）
6. 执行实现（Implementation）
7. 自检 Review（Self Review）
8. 更新文档（Update Documents）
9. 生成交付物（Deliverables）
10. 执行交接（Handoff）
```

### 所有 Agent 必须遵守的核心约束

1. **不得依赖聊天上下文** — 所有信息必须从 Markdown 文档读取
2. **不得修改其他 Agent 的交付物** — 每个 Agent 只修改自己的 Deliverables
3. **必须在交接前完成自检** — 通过 Quality Gates 后方可交接
4. **必须更新 Decision Log** — 所有关键决策必须记录
5. **必须更新 Todo 状态** — 完成后更新任务状态
6. **必须遵循命名规范** — 所有文件、变量、函数遵循统一命名规范
7. **必须编写完整的交付物** — 不得使用占位符、省略号、TODO 标记
8. **必须通过 Review Checklist** — 提交前逐项检查

### Human In The Loop 触发条件

以下情况必须暂停并等待人类确认：

| 触发条件          | 确认方式                          |
| ------------- | ----------------------------- |
| 架构决策影响多个模块    | 提交 Decision Log，等待 Approval   |
| API 设计存在多种方案  | 提交方案对比，等待选择                   |
| 安全风险高于 Medium | 提交风险报告，等待确认                   |
| 数据库 Schema 变更 | 提交 Migration Plan，等待审批        |
| 生产环境部署        | 提交 Deployment Plan，等待审批       |
| 第三方服务选型       | 提交 Vendor Comparison，等待选择     |
| 预算 / 资源超支     | 提交 Resource Alert，等待决策        |
| 需求模糊或矛盾       | 提交 Clarification Request，等待澄清 |

---

## 👥 13 个角色一览

### 按角色查找

| 角色         | 文件                             | 核心职责                   |
| ---------- | ------------------------------ | ---------------------- |
| 产品经理       | `skills/product-manager.md`    | 需求分析、PRD 编写、优先级排序      |
| UI/UX 设计师  | `skills/ui-ux-designer.md`     | 界面设计、交互设计、设计系统         |
| 解决方案架构师    | `skills/solution-architect.md` | 系统架构、技术选型、架构评审         |
| 数据库工程师     | `skills/database-engineer.md`  | 数据库设计、Schema 管理、性能优化   |
| 后端工程师      | `skills/backend-engineer.md`   | API 开发、业务逻辑、服务端实现      |
| 前端工程师      | `skills/frontend-engineer.md`  | 前端开发、组件实现、状态管理         |
| AI 工程师     | `skills/ai-engineer.md`        | AI 功能开发、模型集成、Prompt 工程 |
| QA 工程师     | `skills/qa-engineer.md`        | 测试策略、自动化测试、质量报告        |
| 安全工程师      | `skills/security-engineer.md`  | 安全审计、漏洞扫描、安全加固         |
| 代码评审工程师    | `skills/code-reviewer.md`      | 代码审查、规范检查、技术债务识别       |
| DevOps 工程师 | `skills/devops-engineer.md`    | CI/CD、容器化、监控运维         |
| 项目经理       | `skills/project-manager.md`    | 项目规划、进度管理、风险控制         |
| 技术负责人      | `skills/tech-lead.md`          | 技术决策、架构评审、技术债务管理       |

### 按阶段查找

| 阶段    | 相关文档                                                                                             |
| ----- | ------------------------------------------------------------------------------------------------ |
| 需求分析  | `skills/product-manager.md`, `templates/prd-template.md`                                         |
| 设计    | `skills/ui-ux-designer.md`, `skills/solution-architect.md`, `templates/architecture-template.md` |
| 数据层   | `skills/database-engineer.md`, `shared/database-standard.md`, `templates/database-template.md`   |
| 后端开发  | `skills/backend-engineer.md`, `shared/api-standard.md`, `templates/api-template.md`              |
| 前端开发  | `skills/frontend-engineer.md`, `shared/coding-standard.md`                                       |
| AI 开发 | `skills/ai-engineer.md`                                                                          |
| 测试    | `skills/qa-engineer.md`, `shared/testing-standard.md`, `templates/test-template.md`              |
| 安全    | `skills/security-engineer.md`                                                                    |
| 审查    | `skills/code-reviewer.md`, `shared/review-standard.md`                                           |
| 部署    | `skills/devops-engineer.md`, `shared/deployment-standard.md`, `templates/deployment-template.md` |
| 管理    | `skills/project-manager.md`, `skills/tech-lead.md`, `shared/decision-log-standard.md`            |

### 按规范查找

| 规范类型   | 文件                                 |
| ------ | ---------------------------------- |
| 编码规范   | `shared/coding-standard.md`        |
| API 规范 | `shared/api-standard.md`           |
| 数据库规范  | `shared/database-standard.md`      |
| Git 规范 | `shared/git-standard.md`           |
| 文档规范   | `shared/documentation-standard.md` |
| 审查规范   | `shared/review-standard.md`        |
| 测试规范   | `shared/testing-standard.md`       |
| 部署规范   | `shared/deployment-standard.md`    |
| 决策日志规范 | `shared/decision-log-standard.md`  |
| 任务规范   | `shared/task-standard.md`          |
| 上下文规范  | `shared/context-standard.md`       |
| 记忆规范   | `shared/memory-standard.md`        |
| 交接规范   | `shared/handoff-standard.md`       |

---

## 🔧 技术栈

本仓库设计为技术栈无关，但提供以下技术栈的参考实现：

| 技术栈           | 后端                         | 前端                    | 数据库                  | AI/ML              |
| ------------- | -------------------------- | --------------------- | -------------------- | ------------------ |
| 全栈 JavaScript | Node.js / Express / NestJS | React / Next.js / Vue | PostgreSQL / MongoDB | LangChain / OpenAI |
| Python 生态     | FastAPI / Django           | -                     | PostgreSQL           | LangChain / LLaMA  |
| Java 企业级      | Spring Boot                | React / Angular       | MySQL / PostgreSQL   | Spring AI          |
| Go 高性能        | Go / Gin / Fiber           | -                     | PostgreSQL           | -                  |
| Rust 系统级      | Rust / Actix / Axum        | -                     | PostgreSQL           | -                  |

### 示例项目（TaskFlow）技术栈

| 层级    | 技术选型                                        | 说明                |
| ----- | ------------------------------------------- | ----------------- |
| 前端    | React + TypeScript + Zustand + Tailwind CSS | 状态管理轻量、样式原子化      |
| 后端    | NestJS + TypeScript + Prisma                | 企业级架构、类型安全 ORM    |
| 数据库   | PostgreSQL                                  | 成熟稳定，支持 JSON、全文搜索 |
| 缓存    | Redis                                       | 高性能缓存、会话管理        |
| AI    | OpenAI API + LangChain                      | RAG 管线、智能排期       |
| CI/CD | GitHub Actions + Docker + K8s               | 自动化部署、容器编排        |
| 监控    | Prometheus + Grafana                        | 指标采集、可视化告警        |

---

## 🌟 开始使用

```bash
# 克隆仓库
git clone https://github.com/Zoe-cmd/TraeSkill.git

# 选择你的 AI 工具，参考「各 AI 工具导入指南」章节
# 每个工具都有详细的操作步骤和代码示例

# 启动你的第一个 AI 团队
# 输入: "你是一名产品经理，请阅读 skills/product-manager.md"
```

## ★ 各 AI 工具导入指南

本仓库的设计理念是**纯 Markdown 驱动**，任何支持文件读取的 AI 工具都可以使用。以下是 7 个主流 AI 工具的详细导入和使用指南。

---

### 在 TRAE 中使用

#### 导入方法

TRAE 原生支持 Skill 系统，你可以通过以下方式导入：

**方式一：通过界面导入（推荐）**

1. 打开 TRAE IDE
2. 前往 **设置** → **技能与命令**
3. 在 **技能** 部分，点击 **创建** 按钮
4. 选择 **项目技能**（推荐）或 **全局技能**
5. 上传本仓库的 `SKILL.md` 文件
6. TRAE 会自动分析 Skill 并填充名称、描述和指令
7. 点击 **确认** 完成导入

**方式二：手动复制到项目目录**

```bash
# 将整个仓库复制到项目 .trae/skills/ 目录下
# 例如：
cp -r TraeSkill/ .trae/skills/trae-skill/
```

**方式三：使用 .agents 技能目录**

```bash
# 将 SKILL.md 放入 .agents/skills/trae-skill/ 目录
mkdir -p .agents/skills/trae-skill/
cp SKILL.md .agents/skills/trae-skill/
cp -r skills/ shared/ templates/ workflow.md .agents/skills/trae-skill/
```

然后在 TRAE 设置中启用「.agents 技能目录」开关。

#### 启动你的 AI 团队

导入后，在 TRAE 对话中输入：

```
我有一个项目想法：[描述你的项目]
请按照 SKILL.md 中的工作流程，从 Phase 0 开始推进。
```

或者直接指定角色：

```
你现在是产品经理，请阅读 skills/product-manager.md 
了解你的职责，然后开始分析我的需求。
```

#### 角色切换

当需要切换到下一个角色时，输入：

```
Phase 0 已完成，请通知 项目经理 开始 Phase 1 工作。
请阅读 workflow.md 了解 Phase 1 的详细流程。
```

#### 使用技巧

- TRAE 会根据 SKILL.md 中的 `description` 字段自动判断是否触发本技能
- 你也可以手动调用：`用 software-team-simulator 技能帮我启动一个新项目`
- 每个角色的 Skill 文件包含完整的 Prompt Template，Agent 会自动遵循
- 关键决策点 TRAE 会暂停等待你的确认，确保 HITL 流程
- 如果只想用某个特定角色，直接说「帮我用产品经理角色分析需求」即可

---

### 在 Claude Code 中使用

#### 导入方法

1. 将本仓库克隆到本地：

```bash
git clone https://github.com/Zoe-cmd/TraeSkill.git
```

2. 在 Claude Code 对话中，将 Skill 文件作为上下文引用：

```
# 启动产品经理
@skills/product-manager.md
@shared/documentation-standard.md
@templates/prd-template.md

请阅读以上文件，了解你的角色职责。
现在，我需要你以产品经理的身份分析以下需求：[描述你的需求]
```

#### 角色切换

每个角色完成工作后，手动切换到下一个角色：

```
# 产品经理工作已完成，启动架构师
@skills/solution-architect.md
@shared/api-standard.md
@shared/coding-standard.md
@templates/architecture-template.md

请阅读以上文件，以及产品经理交付的 PRD 文档。
现在，请以系统架构师的身份设计系统架构。
```

#### 使用技巧

- Claude Code 支持 `@文件路径` 引用多个文件，可以一次引用角色的 Skill 文件 + 相关规范文件 + 模板文件
- 每完成一个 Phase，让 Claude Code 更新对应的文档，然后手动切换到下一个角色
- 建议使用 Claude Code 的 Projects 功能，将整个仓库作为项目上下文，避免每次重复引用
- Claude Code 的 `/init` 命令可以初始化项目记忆，将核心设计哲学写入 MEMORY.md

---

### 在 Cursor 中使用

#### 导入方法

1. 将本仓库克隆到本地：

```bash
git clone https://github.com/Zoe-cmd/TraeSkill.git
```

2. 在 Cursor 中打开你的项目目录（将 TraeSkill 放在项目目录下或作为子模块）

3. 在 Cursor 的 Agent 模式下，使用 `@file` 引用 Skill 文件：

```
@skills/product-manager.md
请阅读这个文件，你现在是产品经理角色。
请分析我的项目需求并编写 PRD。
```

#### 快捷方式

- 可以创建 `.cursorrules` 文件，将核心规则写入其中：

```markdown
# .cursorrules
你是一个 AI 软件工程团队的成员。
请遵循 TraeSkill/skills/ 目录下的角色定义。
所有代码必须符合 TraeSkill/shared/ 目录下的编码规范。
文档格式请参考 TraeSkill/templates/ 目录下的模板。
关键决策必须等待人工确认（HITL）。
```

- 在 Cursor Chat 中使用 `@folder:skills` 引用整个 skills 目录
- 使用 Cursor 的 Composer 功能进行多文件协同编辑
- Cursor 支持 Agent 模式自动执行多步骤任务，非常适合按角色顺序推进

#### 角色切换

```
# 在 Cursor Chat 中切换角色
@skills/solution-architect.md
@skills/database-engineer.md
请先阅读架构师角色，完成系统架构设计。
然后切换到数据库工程师角色，进行数据建模。
```

---

### 在 GitHub Copilot / Codex 中使用

#### 导入方法

1. 将本仓库克隆到你的项目目录：

```bash
git clone https://github.com/Zoe-cmd/TraeSkill.git
```

2. 在 VS Code 中打开项目

3. 使用 Copilot Chat 或 Codex CLI，引用 Skill 文件：

```
# 在 Copilot Chat 中（VS Code）
请阅读 @skills/product-manager.md 文件。
你现在是一名产品经理，请按照文件中定义的工作流程分析我的需求。

# 使用 Codex CLI（终端）
cat skills/product-manager.md | codex -

# 或者直接在 Codex 对话中
codex
> 请阅读 skills/product-manager.md
> 你是产品经理，分析一下这个需求：[描述需求]
```

#### 使用技巧

- Copilot Chat 支持 `#file:` 引用文件：`#file:skills/product-manager.md`
- 可以将 Skill 文件内容复制到 Chat 中作为上下文（适合较短的 Skill 文件）
- 建议创建一个 `.github/copilot-instructions.md` 文件，包含核心规则：

```markdown
# copilot-instructions.md
本项目使用 TraeSkill AI 软件工程技能仓库。
当前角色定义请参考 skills/ 目录下的对应文件。
编码规范请参考 shared/coding-standard.md。
所有关键决策必须等待人工确认。
```

- Codex CLI 支持 `--instructions` 参数加载自定义指令文件

#### 角色切换

```
# 在 Copilot Chat 中
#file:skills/solution-architect.md
#file:shared/api-standard.md
现在你是系统架构师，上一个角色（产品经理）已经完成了 PRD，请开始架构设计。
```

---

### 在 Gemini CLI 中使用

#### 导入方法

1. 将本仓库克隆到本地：

```bash
git clone https://github.com/Zoe-cmd/TraeSkill.git
```

2. 使用 Gemini CLI 的 `--system-instruction` 或文件引用：

```bash
# 方式一：将 Skill 文件作为系统指令（Windows PowerShell）
$content = Get-Content -Path skills/product-manager.md -Raw
gemini --system-instruction "$content"

# 方式二：直接启动 Gemini，在对话中引用
gemini
> 请阅读 skills/product-manager.md 文件
> 你现在是产品经理，请分析我的需求：[描述需求]
```

#### 使用技巧

- Gemini 支持超长上下文（百万 token 级别），可以一次传入多个 Skill 文件
- 建议按 Phase 分批使用，每个 Phase 切换一次角色，保持上下文聚焦
- 使用 Gemini 的上下文缓存功能提高效率，避免重复上传相同的 Skill 文件
- 可以创建一个启动脚本 `start-agent.sh`（或 `start-agent.ps1`），自动拼接角色 Skill + 相关规范

#### 角色切换

```bash
# 在 Gemini CLI 中
# 上一个角色完成后，启动新角色
gemini
> 请阅读 skills/solution-architect.md 和 shared/api-standard.md
> 产品经理的 PRD 已经完成（见 prd.md），请开始系统架构设计。
```

---

### 在 OpenHands 中使用

#### 导入方法

1. 将本仓库克隆到本地：

```bash
git clone https://github.com/Zoe-cmd/TraeSkill.git
```

2. 在 OpenHands 中创建新任务

3. 在任务描述中引用 Skill 文件：

```
请阅读 /path/to/TraeSkill/skills/product-manager.md
了解你的角色定义。然后阅读 /path/to/TraeSkill/workflow.md
了解整体工作流程。现在开始分析以下需求：[描述你的需求]
```

#### 使用技巧

- OpenHands 支持文件系统操作，可以自动读取和创建文件，天然适合文档驱动开发
- 为每个角色创建单独的 OpenHands 任务，通过文件系统传递上下文
- 使用 OpenHands 的 Agent 模式，让 AI 自动执行多步骤任务
- 每个角色任务完成后，OpenHands 会自动将交付物写入文件系统，下一个角色任务可以直接读取

#### 角色切换

```
# 创建新的 OpenHands 任务
请阅读 /path/to/TraeSkill/skills/solution-architect.md
以及 /path/to/TraeSkill/shared/api-standard.md
和 /path/to/TraeSkill/shared/coding-standard.md

上一个角色（产品经理）已经完成了 PRD，文件在 /path/to/project/prd.md
请以系统架构师的身份，设计系统架构并输出 architecture.md。
```

---

### 在其他 AI 工具中使用（通用方法）

本仓库的设计理念是**纯 Markdown 驱动**，任何支持文件读取的 AI 工具都可以使用。

#### 通用三步法

1. **选择角色**：根据当前阶段，选择对应的 Skill 文件（如 `skills/product-manager.md`）
2. **喂给 AI**：将 Skill 文件内容 + 相关规范文件 + 输入文档（如上一阶段的交付物）一起作为上下文
3. **按流程执行**：让 AI 按照 Skill 文件中的 Prompt Template 工作，生成交付物并更新文档

#### 快速参考：什么时候读什么

| 你想做什么      | 读取这些文件                        |
| ---------- | ----------------------------- |
| 启动某个角色     | `skills/角色名.md`               |
| 了解编码规范     | `shared/coding-standard.md`   |
| 参考文档模板     | `templates/xxx-template.md`   |
| 看完整示例      | `examples/sample-project/` 目录 |
| 了解整体流程     | `workflow.md`                 |
| 解决角色间的协作问题 | `shared/handoff-standard.md`  |

#### 目录速查

```
想启动某个角色？      → 读取 skills/角色名.md
想了解编码规范？      → 读取 shared/coding-standard.md
想参考文档模板？      → 读取 templates/xxx-template.md
想看完整示例？        → 阅读 examples/sample-project/ 目录
想了解整体流程？      → 阅读 workflow.md
想了解如何交接？      → 读取 shared/handoff-standard.md
```

---

## 🎬 核心工作流

### 角色启动顺序

```
Phase 0: 项目初始化
├── 项目经理 (project-manager.md) — 项目规划和资源分配
└── 技术负责人 (tech-lead.md) — 技术选型和架构评审

Phase 1: 需求与设计
├── 产品经理 (product-manager.md) — 需求分析和 PRD 编写
├── UI/UX 设计师 (ui-ux-designer.md) — 界面和交互设计
└── 系统架构师 (solution-architect.md) — 系统架构设计

Phase 2: 数据与后端
├── 数据库工程师 (database-engineer.md) — 数据库设计
└── 后端工程师 (backend-engineer.md) — 后端开发

Phase 3: 前端与 AI
├── 前端工程师 (frontend-engineer.md) — 前端开发
└── AI 工程师 (ai-engineer.md) — AI 功能开发

Phase 4: 质量保证
├── 测试工程师 (qa-engineer.md) — 测试
├── 安全工程师 (security-engineer.md) — 安全审计
└── 代码评审工程师 (code-reviewer.md) — 代码审查

Phase 5: 部署与运维
├── 运维工程师 (devops-engineer.md) — 部署和运维
└── 项目经理 (project-manager.md) — 项目验收

Phase 6: 持续维护
└── 技术负责人 (tech-lead.md) — 技术债务管理
```

### 快速开始（三步走）

**第 1 步：选择你的 AI 工具并导入**

参考上方「各 AI 工具导入指南」章节，选择你使用的 AI 工具，按照对应指南导入本仓库。

**第 2 步：启动你的第一个 AI Agent**

根据你选择的工具，使用对应的方式启动角色：

```
你是一名 AI 产品经理，请阅读 skills/product-manager.md 了解你的职责。
然后阅读 workflow.md 了解整体流程。
现在，我有一个项目想法：[你的项目想法]，请开始工作。
```

**第 3 步：按流程接力**

每个 Agent 完成工作后，会自动生成交接文档。下一个 Agent 读取交接文档，继续工作。

**你只需要在关键决策点说"通过"或"改这里"。**

### 文档驱动开发流程

```
┌──────────────────────────────────────────────────────────────────┐
│                  Documentation Driven Development                 │
├──────────────────────────────────────────────────────────────────┤
│                                                                  │
│   ┌─────────┐     ┌─────────┐     ┌─────────┐     ┌─────────┐  │
│   │  READ   │────▶│ ANALYZE │────▶│  DESIGN │────▶│IMPLEMENT│  │
│   │  Docs   │     │  Docs   │     │  Docs   │     │  Code   │  │
│   └─────────┘     └─────────┘     └─────────┘     └─────────┘  │
│        ▲                                               │        │
│        │              ┌─────────┐                      │        │
│        └──────────────│ HANDOFF │◀─────────────────────│        │
│                       │  Docs   │                      │        │
│                       └─────────┘                      │        │
│                            │                           │        │
│                            ▼                           │        │
│                       ┌─────────┐                      │        │
│                       │  NEXT   │                      │        │
│                       │  Agent  │                      │        │
│                       └─────────┘                      │        │
│                                                                  │
└──────────────────────────────────────────────────────────────────┘
```

---

## 💡 使用场景

### 场景 1：个人开发者做 Side Project

> "我想做一个 AI 记账小程序，但我是前端，后端不熟，数据库也不会设计。"

👉 启动产品经理 → 写 PRD → 架构师设计 → 数据库工程师建模 → 后端 + 前端并行开发 → 测试 → 上线

**你一个人，花 2-3 天，搞定一个全栈项目。**

### 场景 2：创业团队快速验证 MVP

> "我们只有 3 个人，但要在一个月内出 MVP 去融资。"

👉 3 个人分别负责：产品方向决策 + AI 执行 + 代码 Review

**人力不变，产出翻 3 倍。**

### 场景 3：企业内部工具开发

> "运维部需要一个小工具，开发部排期到 3 个月后了。"

👉 运维同事自己用 AI Agent 开发，开发部只做最终 Review

**需求提出者 = 需求实现者。**

---

## 🙋 常见问题

**Q: 我用 Claude Code / Cursor / Copilot，能用这个仓库吗？**

A: 当然！所有 Skill 都是纯 Markdown，任何支持文件读取的 AI 工具都能用。详见上方「各 AI 工具导入指南」章节，每个工具都有详细的操作步骤。

**Q: 我一定要按 13 个角色的顺序走吗？**

A: 不需要。小项目可以跳过 UI 设计师、AI 工程师等非必需角色。workflow.md 中定义了哪些角色可以并行、哪些可以跳过。你可以只用 5-6 个核心角色完成一个中小型项目。

**Q: 这个仓库和 Cursor Rules / .cursorrules 有什么区别？**

A: Cursor Rules 是"给一个 AI 看的提示"。这个仓库是"给 13 个 AI 看的 SOP"。一个是便签，一个是一整套岗位手册。前者告诉 AI"怎么做"，后者告诉 AI"你是谁、你的职责是什么、你要交付什么、你要和谁协作"。

**Q: 我可以用部分角色而不是全部吗？**

A: 完全可以。每个角色是独立的。你可以只用一个产品经理帮你写 PRD，也可以只用架构师 + 后端工程师帮你设计系统。仓库的模块化设计让你按需取用。

**Q: 需要什么水平的开发者才能用？**

A: 至少需要能独立完成一个完整模块的开发。你需要能看懂 AI 生成的代码、判断对错、做出技术决策。如果你还不会写代码，先用 AI 学会写代码再来。

**Q: 怎么自定义角色？**

A: 可以。每个 Skill 文件都是独立的，你可以修改、删除、添加角色。参考 `templates/` 中的模板和现有角色的结构，遵循 `shared/` 中的规范，保证新角色能无缝接入。记得在 `workflow.md` 中定义新角色的位置。

**Q: AI 生成的代码质量如何保证？**

A: 四层保障机制：

1. 每个角色的 Skill 手册包含「常见错误」章节，预先警示 AI 容易出错的地方
2. 13 本共享规范保证所有角色输出的代码风格统一
3. 代码评审工程师（code-reviewer）会专门审查代码质量
4. 你（人类开发者）是最终审查者，所有关键决策由你确认

**Q: 这个仓库多久更新一次？**

A: 仓库本身也在持续进化。共享规范、Skill 手册、模板都会根据实际使用反馈不断优化。欢迎提 Issue 和 PR。

**Q: 可以用于商业项目吗？**

A: 可以。MIT 许可证，你可以自由使用、修改、分发。但请注意：AI 生成的代码你需要自己审查质量，仓库不提供任何质量保证。

---

## 📊 仓库数据

| 指标        | 数值                |
| --------- | ----------------- |
| 总文件数      | 45 个 Markdown     |
| 总行数       | 17,000+           |
| Skill 角色数 | 13 个              |
| 共享规范数     | 13 本              |
| 文档模板数     | 8 个               |
| 示例项目      | 1 个完整项目（TaskFlow） |
| 最大单文件     | 800+ 行            |
| AI 工具支持   | 7 个主流工具（含通用方法）    |

---

## ⚠️ 重要说明

1. **这不是"一键生成"工具** —— 你仍然需要做决策、Review 代码、调试问题。这个仓库帮你省掉的是"重复劳动"和"上下文切换"，不是"思考"。

2. **AI 会犯错** —— 永远要 Review AI 生成的代码。每本 Skill 手册都包含「常见错误」章节，正是因为我们知道 AI 容易在哪里翻车。

3. **Human In The Loop** —— 关键决策点（PRD 审批、架构评审、安全审计、上线确认）必须由人类确认。

4. **持续迭代** —— 这个仓库本身也在不断进化。欢迎提 Issue 和 PR。

5. **上下文管理** —— 不同 AI 工具的上下文窗口大小不同。如果一次性加载太多 Skill 文件导致上下文溢出，建议按角色分批使用，每次只加载当前角色需要的文件。

---

## 📝 License

MIT 

---

> **"一个人就是一支军队。"**
> 
> 不再等招人、不再等排期、不再等沟通。
> 打开你喜欢的 AI 工具，导入 Skill，启动你的 AI 研发团队。
> 
> **现在就试试。**