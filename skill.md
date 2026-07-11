# AI 软件工程技能仓库

## 概述

本 Repository 是一套企业级 AI Software Engineering Skill Repository，旨在让一个开发者通过多个 AI Agent（Codex、Claude Code、Cursor Agent、Gemini CLI、OpenHands 等）模拟一个完整的软件研发团队，从需求分析到部署上线，完成企业级复杂项目开发。

## 设计哲学

### 核心理念

| 理念 | 说明 |
|------|------|
| **Documentation Driven Development (DDD)** | Markdown 是唯一真实来源（Source of Truth），所有 Agent 必须从文档读取、分析、更新文档、交接 |
| **AI Agent First** | 所有流程、规范、模板均面向 AI Agent 设计，而非人类开发者 |
| **Human In The Loop (HITL)** | 关键决策点必须由人类确认，AI Agent 提供建议但不可越权 |
| **Multi-Agent Collaboration** | 每个 Agent 担任单一角色，遵循 SRP（Single Responsibility Principle），通过文档交接协作 |
| **High Cohesion, Low Coupling** | 每个 Skill 高度内聚，Skill 之间通过共享文档松耦合 |
| **SOLID** | 遵循 SOLID 原则，确保 Repository 可扩展、可维护 |
| **KISS (Keep It Simple, Stupid)** | 避免过度设计，每个 Skill 聚焦核心职责 |
| **DRY (Don't Repeat Yourself)** | 共享内容放入 shared/ 目录，避免重复定义 |
| **YAGNI (You Aren't Gonna Need It)** | 只包含经过验证的实践，不添加推测性内容 |

### 架构原则

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                        AI Agent Team Architecture                            │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  ┌──────────┐    ┌──────────┐    ┌──────────┐    ┌──────────┐              │
│  │ Product  │───▶│  UI/UX   │───▶│ Solution │───▶│ Database │              │
│  │ Manager  │    │ Designer │    │ Architect│    │ Engineer │              │
│  └──────────┘    └──────────┘    └──────────┘    └──────────┘              │
│       │                                               │                     │
│       │              ┌──────────┐                     │                     │
│       │              │  Tech    │◀────────────────────│                     │
│       │              │  Lead    │                     │                     │
│       │              └──────────┘                     │                     │
│       │                   │                           │                     │
│       ▼                   ▼                           ▼                     │
│  ┌──────────┐    ┌──────────┐    ┌──────────┐    ┌──────────┐              │
│  │ Backend  │    │ Frontend │    │    AI    │    │   QA     │              │
│  │ Engineer │    │ Engineer │    │ Engineer │    │ Engineer │              │
│  └──────────┘    └──────────┘    └──────────┘    └──────────┘              │
│       │                │               │               │                    │
│       ▼                ▼               ▼               ▼                    │
│  ┌──────────┐    ┌──────────┐    ┌──────────┐    ┌──────────┐              │
│  │ Security │    │   Code   │    │  DevOps  │    │ Project  │              │
│  │ Engineer │    │ Reviewer │    │ Engineer │    │ Manager  │              │
│  └──────────┘    └──────────┘    └──────────┘    └──────────┘              │
│                                                                             │
│                         Human In The Loop                                   │
│                               │                                             │
│                    ┌──────────┴──────────┐                                  │
│                    │   Human Developer   │                                  │
│                    │  (Final Authority)  │                                  │
│                    └─────────────────────┘                                  │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

## Repository 结构

```
skills/
├── README.md                      # 本文件 - Repository 总览
├── workflow.md                    # 整体工作流定义
├── product-manager.md             # 产品经理 Skill
├── ui-ux-designer.md              # UI/UX 设计师 Skill
├── solution-architect.md          # 解决方案架构师 Skill
├── database-engineer.md           # 数据库工程师 Skill
├── backend-engineer.md            # 后端工程师 Skill
├── frontend-engineer.md           # 前端工程师 Skill
├── ai-engineer.md                 # AI 工程师 Skill
├── qa-engineer.md                 # QA 工程师 Skill
├── security-engineer.md           # 安全工程师 Skill
├── code-reviewer.md               # 代码审查员 Skill
├── devops-engineer.md             # DevOps 工程师 Skill
├── project-manager.md             # 项目经理 Skill
├── tech-lead.md                   # 技术负责人 Skill

shared/
├── coding-standard.md             # 编码规范
├── api-standard.md                # API 规范
├── database-standard.md           # 数据库规范
├── git-standard.md                # Git 规范
├── documentation-standard.md      # 文档规范
├── review-standard.md             # 审查规范
├── testing-standard.md            # 测试规范
├── deployment-standard.md         # 部署规范
├── decision-log-standard.md       # 决策日志规范
├── task-standard.md               # 任务规范
├── context-standard.md            # 上下文规范
├── memory-standard.md             # 记忆规范
├── handoff-standard.md            # 交接规范

templates/
├── prd-template.md                # PRD 模板
├── architecture-template.md       # 架构文档模板
├── api-template.md                # API 文档模板
├── database-template.md           # 数据库文档模板
├── test-template.md               # 测试文档模板
├── deployment-template.md         # 部署文档模板
├── decision-log-template.md       # 决策日志模板
├── todo-template.md               # 任务清单模板

examples/
└── sample-project/                # 完整示例项目
    ├── README.md
    ├── prd.md
    ├── architecture.md
    ├── api-spec.md
    ├── database-schema.md
    ├── test-plan.md
    ├── deployment-plan.md
    ├── decision-log.md
    └── todo.md
```

## 使用指南

### 快速开始

1. **克隆或复制本 Repository** 到你的项目目录
2. **阅读 `workflow.md`** 了解整体工作流
3. **按照角色顺序** 依次调用 AI Agent
4. **每个 Agent 启动时**，使用对应的 Prompt Template
5. **严格遵循 Handoff 流程**，确保文档交接完整

### 角色启动顺序

```
Phase 0: 项目初始化
├── 项目经理 (project-manager.md) - 项目规划和资源分配
└── 技术负责人 (tech-lead.md) - 技术选型和架构评审

Phase 1: 需求与设计
├── 产品经理 (product-manager.md) - 需求分析和 PRD 编写
├── UI/UX设计师 (ui-ux-designer.md) - 界面和交互设计
└── 系统架构师 (solution-architect.md) - 系统架构设计

Phase 2: 数据与后端
├── 数据库工程师 (database-engineer.md) - 数据库设计
└── 后端工程师 (backend-engineer.md) - 后端开发

Phase 3: 前端与 AI
├── 前端工程师 (frontend-engineer.md) - 前端开发
└── AI工程师 (ai-engineer.md) - AI 功能开发

Phase 4: 质量保证
├── 测试工程师 (qa-engineer.md) - 测试
├── 安全工程师 (security-engineer.md) - 安全审计
└── 代码评审工程师 (code-reviewer.md) - 代码审查

Phase 5: 部署与运维
├── 运维工程师 (devops-engineer.md) - 部署和运维
└── 项目经理 (project-manager.md) - 项目验收

Phase 6: 持续维护
└── 技术负责人 (tech-lead.md) - 技术债务管理
```

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

## 核心约束

### 所有 Agent 必须遵守

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

| 触发条件 | 确认方式 |
|----------|----------|
| 架构决策影响多个模块 | 提交 Decision Log，等待 Approval |
| API 设计存在多种方案 | 提交方案对比，等待选择 |
| 安全风险高于 Medium | 提交风险报告，等待确认 |
| 数据库 Schema 变更 | 提交 Migration Plan，等待审批 |
| 生产环境部署 | 提交 Deployment Plan，等待审批 |
| 第三方服务选型 | 提交 Vendor Comparison，等待选择 |
| 预算/资源超支 | 提交 Resource Alert，等待决策 |
| 需求模糊或矛盾 | 提交 Clarification Request，等待澄清 |

## 质量保证

### Quality Gates

每个 Agent 在交接前必须通过以下 Quality Gates：

| Gate | 检查内容 | 通过标准 |
|------|----------|----------|
| Gate 1: 文档完整性 | 所有必填文档是否完整 | 100% 完成 |
| Gate 2: 规范合规性 | 是否符合 Shared Standards | 0 违规 |
| Gate 3: 交付物质量 | 交付物是否满足 Acceptance Criteria | 100% 满足 |
| Gate 4: 自检通过 | Self Review 是否通过 | 100% 通过 |
| Gate 5: 决策记录 | Decision Log 是否更新 | 所有决策已记录 |
| Gate 6: 交接准备 | Handoff 文档是否完整 | 100% 完成 |

### 审查层级

```
Level 1: Self Review（自检）
    └── Agent 完成工作后自我检查

Level 2: Peer Review（同行评审）
    └── Code Reviewer 审查代码质量

Level 3: Security Review（安全审查）
    └── Security Engineer 审查安全漏洞

Level 4: Architecture Review（架构审查）
    └── Tech Lead / Architect 审查架构合规

Level 5: Human Review（人工审查）
    └── Human Developer 最终审查关键决策
```

## 版本管理

### Repository 版本策略

- **主版本号（Major）**: 架构级变更，不兼容的修改
- **次版本号（Minor）**: 新增 Skill 或 Shared Standard
- **修订号（Patch）**: 文档修正、示例更新

### 变更流程

1. 提出变更 Proposal（Decision Log 记录）
2. 技术负责人评审
3. Human Developer 批准
4. 更新相关文档
5. 更新版本号
6. 通知所有 Agent

## 贡献指南

### 新增 Skill

1. 使用 `templates/` 中的模板
2. 确保包含所有必填章节（不少于 300 行）
3. 在 `workflow.md` 中定义其位置
4. 更新本 README 的目录结构
5. 通过技术负责人评审

### 修改 Shared Standard

1. 在 Decision Log 中记录变更原因
2. 评估对所有 Skill 的影响
3. 更新所有受影响的 Skill 文档
4. 通过 Human Developer 审批

## 快速索引

### 按角色查找

| 角色 | 文件 | 核心职责 |
|------|------|----------|
| 产品经理 | `product-manager.md` | 需求分析、PRD 编写、优先级排序 |
| UI/UX 设计师 | `ui-ux-designer.md` | 界面设计、交互设计、设计系统 |
| 解决方案架构师 | `solution-architect.md` | 系统架构、技术选型、架构评审 |
| 数据库工程师 | `database-engineer.md` | 数据库设计、Schema 管理、性能优化 |
| 后端工程师 | `backend-engineer.md` | API 开发、业务逻辑、服务端实现 |
| 前端工程师 | `frontend-engineer.md` | 前端开发、组件实现、状态管理 |
| AI 工程师 | `ai-engineer.md` | AI 功能开发、模型集成、Prompt 工程 |
| QA 工程师 | `qa-engineer.md` | 测试策略、自动化测试、质量报告 |
| 安全工程师 | `security-engineer.md` | 安全审计、漏洞扫描、安全加固 |
| 代码审查员 | `code-reviewer.md` | 代码审查、规范检查、技术债务识别 |
| DevOps 工程师 | `devops-engineer.md` | CI/CD、容器化、监控运维 |
| 项目经理 | `project-manager.md` | 项目规划、进度管理、风险控制 |
| 技术负责人 | `tech-lead.md` | 技术决策、架构评审、技术债务管理 |

### 按阶段查找

| 阶段 | 相关文档 |
|------|----------|
| 需求分析 | `product-manager.md`, `templates/prd-template.md` |
| 设计 | `ui-ux-designer.md`, `solution-architect.md`, `templates/architecture-template.md` |
| 数据层 | `database-engineer.md`, `shared/database-standard.md`, `templates/database-template.md` |
| 后端开发 | `backend-engineer.md`, `shared/api-standard.md`, `templates/api-template.md` |
| 前端开发 | `frontend-engineer.md`, `shared/coding-standard.md` |
| AI 开发 | `ai-engineer.md` |
| 测试 | `qa-engineer.md`, `shared/testing-standard.md`, `templates/test-template.md` |
| 安全 | `security-engineer.md` |
| 审查 | `code-reviewer.md`, `shared/review-standard.md` |
| 部署 | `devops-engineer.md`, `shared/deployment-standard.md`, `templates/deployment-template.md` |
| 管理 | `project-manager.md`, `tech-lead.md`, `shared/decision-log-standard.md` |

### 按规范查找

| 规范类型 | 文件 |
|----------|------|
| 编码规范 | `shared/coding-standard.md` |
| API 规范 | `shared/api-standard.md` |
| 数据库规范 | `shared/database-standard.md` |
| Git 规范 | `shared/git-standard.md` |
| 文档规范 | `shared/documentation-standard.md` |
| 审查规范 | `shared/review-standard.md` |
| 测试规范 | `shared/testing-standard.md` |
| 部署规范 | `shared/deployment-standard.md` |
| 决策日志规范 | `shared/decision-log-standard.md` |
| 任务规范 | `shared/task-standard.md` |
| 上下文规范 | `shared/context-standard.md` |
| 记忆规范 | `shared/memory-standard.md` |
| 交接规范 | `shared/handoff-standard.md` |

## 技术栈支持

本 Repository 设计为技术栈无关，但提供以下技术栈的参考实现：

| 技术栈 | 后端 | 前端 | 数据库 | AI/ML |
|--------|------|------|--------|-------|
| 全栈 JavaScript | Node.js/Express/NestJS | React/Next.js/Vue | PostgreSQL/MongoDB | LangChain/OpenAI |
| Python 生态 | FastAPI/Django | - | PostgreSQL | LangChain/LLaMA |
| Java 企业级 | Spring Boot | React/Angular | MySQL/PostgreSQL | Spring AI |
| Go 高性能 | Go/Gin/Fiber | - | PostgreSQL | - |
| Rust 系统级 | Rust/Actix/Axum | - | PostgreSQL | - |

## 许可证

本 Repository 为内部使用，版权归创建者所有。

## 版本历史

| 版本 | 日期 | 变更说明 |
|------|------|----------|
| 1.0.0 | 2026-07-11 | 初始版本，完整 Repository 创建 |

---

## 给 AI Agent 的第一条指令

**当你作为 AI Agent 被激活时，请首先阅读本 README.md，然后阅读 `workflow.md`，再根据你的角色阅读对应的 Skill 文件。在开始任何工作前，请确保你理解了自己的角色身份、角色使命、权限范围和工作范围。**

**记住：文档是唯一真实来源（Source of Truth）。所有决策均被记录。所有交接均为显式交接。**