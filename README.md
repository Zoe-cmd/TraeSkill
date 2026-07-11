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

它让你可以用 **一个 Prompt**，驱动多个 AI Agent（Claude Code、Cursor Agent、Codex、Gemini CLI、OpenHands 等），**接力式**地完成整个软件研发流程：

```
产品经理 → UI设计师 → 架构师 → 数据库工程师 → 后端 → 前端 → AI工程师 → 测试 → 安全 → 代码评审 → 运维 → 上线
```

**你只需要做决策，AI 负责执行。**

---

## 🚀 为什么适合中国宝宝体质？

| 痛点 | 传统方式 | 用这个仓库 |
|------|----------|-----------|
| 招聘 | 招人难、养人贵、留人更难 | 13 个 AI Agent 角色，零人力成本 |
| 沟通 | 等消息、开会、扯皮 | 文档驱动，AI 之间自动交接，无需等待 |
| 时间 | 项目周期动辄数月 | 并行 + 接力，**效率提升 3-5 倍** |
| 质量 | 代码风格全靠自觉 | 13 本共享规范，代码像一个人写的 |
| 文档 | 永远在"补文档"的路上 | 文档即源码，写完代码文档自动完整 |
| 成本 | 烧钱如流水 | 你只需要 **API Token 费用** |

---

## 📦 仓库里有什么？

```text
TraeSkill/
├── README.md                     ★ 就这个文件
├── workflow.md                   ★ 全流程工作流定义
├── skills/                       ★ 13 个 AI Agent 技能手册
│   ├── product-manager.md        产品经理 - 需求分析、PRD 撰写
│   ├── ui-ux-designer.md         UI/UX设计师 - 交互设计、设计系统
│   ├── solution-architect.md     系统架构师 - 架构设计、技术选型
│   ├── database-engineer.md      数据库工程师 - 数据建模、SQL 设计
│   ├── backend-engineer.md       后端工程师 - API 开发、业务逻辑
│   ├── frontend-engineer.md      前端工程师 - 组件开发、页面实现
│   ├── ai-engineer.md            AI工程师 - Prompt 工程、RAG 管线
│   ├── qa-engineer.md            测试工程师 - 测试策略、缺陷管理
│   ├── security-engineer.md      安全工程师 - 安全审计、漏洞修复
│   ├── code-reviewer.md          代码评审工程师 - 代码审查、重构建议
│   ├── devops-engineer.md        运维工程师 - CI/CD、部署监控
│   ├── project-manager.md        项目经理 - 进度跟踪、风险管理
│   └── tech-lead.md              技术负责人 - 技术决策、架构评审
├── shared/                       ★ 13 本共享规范
│   ├── coding-standard.md        编码规范（TypeScript/Python/Java/Go）
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
├── templates/                    ★ 8 个文档模板
│   ├── prd-template.md           产品需求文档模板
│   ├── architecture-template.md  架构设计文档模板
│   ├── api-template.md           API 设计文档模板
│   ├── database-template.md      数据库设计文档模板
│   ├── test-template.md          测试计划文档模板
│   ├── deployment-template.md    部署计划文档模板
│   ├── decision-log-template.md  决策记录文档模板
│   └── todo-template.md          任务清单模板
└── examples/                     ★ 完整示例项目
    └── sample-project/           TaskFlow 任务管理系统
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

- **45 个 Markdown 文件**
- **17,000+ 行企业级文档**
- **13 个 AI Agent 角色，每个 ≥ 300 行岗位手册**
- **13 本共享规范，统一代码质量**
- **1 个完整示例项目（TaskFlow），可直接参考**

---

## 🎬 快速开始

### 第 1 步：在 TRAE 中导入 Skill

```bash
# 在 TRAE 中找到 Skill 导入功能，选择 skill.md 文件
# 或直接将整个仓库放入 TRAE 的 skills 目录
```

### 第 2 步：启动你的第一个 AI Agent

打开 TRAE，输入：

```
你是一名 AI 产品经理，请阅读 product-manager.md 了解你的职责。
然后阅读 workflow.md 了解整体流程。
现在，我有一个项目想法：[你的项目想法]，请开始工作。
```

### 第 3 步：按流程接力

每个 Agent 完成工作后，会自动生成交接文档。下一个 Agent 读交接文档，继续工作。

**你只需要在关键决策点说"通过"或"改这里"。**

---

## 🧩 设计哲学

| 理念 | 说明 |
|------|------|
| **📄 文档驱动开发 (DDD)** | Markdown 是唯一真实来源。Agent 不依赖聊天上下文，一切从文档出发 |
| **🤖 AI Agent First** | 所有流程、规范、模板均面向 AI Agent 设计，而非人类 |
| **👤 人在回路 (HITL)** | 关键决策必须由人类确认，AI 提供建议但不越权 |
| **🤝 多 Agent 协作** | 每个 Agent 单一职责，通过文档交接，松耦合高内聚 |
| **🧹 KISS + DRY + YAGNI** | 不炫技、不重复、不过度设计 |

**核心规则就一条：**

> 读取 Markdown → 分析 → 设计 → 实现 → 更新 Markdown → 交接

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

## 🔧 技术栈（示例项目 TaskFlow）

| 层级 | 技术选型 | 说明 |
|------|----------|------|
| 前端 | React + TypeScript + Zustand + Tailwind CSS | 状态管理轻量、样式原子化 |
| 后端 | NestJS + TypeScript + Prisma | 企业级架构、类型安全 ORM |
| 数据库 | PostgreSQL | 成熟稳定，支持 JSON、全文搜索 |
| 缓存 | Redis | 高性能缓存、会话管理 |
| AI | OpenAI API + LangChain | RAG 管线、智能排期 |
| CI/CD | GitHub Actions + Docker + K8s | 自动化部署、容器编排 |
| 监控 | Prometheus + Grafana | 指标采集、可视化告警 |

---

## 📊 仓库数据

| 指标 | 数值 |
|------|------|
| 总文件数 | 45 个 Markdown |
| 总行数 | 17,000+ |
| Skill 角色数 | 13 个 |
| 共享规范数 | 13 本 |
| 文档模板数 | 8 个 |
| 示例项目 | 1 个完整项目 |
| 最大单文件 | 800+ 行 |

---

## ⚠️ 重要说明

1. **这不是"一键生成"工具** —— 你仍然需要做决策、Review 代码、调试问题。这个仓库帮你省掉的是"重复劳动"和"上下文切换"，不是"思考"。

2. **AI 会犯错** —— 永远要 Review AI 生成的代码。每本 Skill 手册都包含"常见错误"章节，正是因为我们知道 AI 容易在哪里翻车。

3. **Human In The Loop** —— 关键决策点（PRD 审批、架构评审、安全审计、上线确认）必须由人类确认。

4. **持续迭代** —— 这个仓库本身也在不断进化。欢迎提 Issue 和 PR。

---

## 🙋 常见问题

**Q: 我用 Claude Code，能用这个仓库吗？**

A: 当然！所有 Skill 都是纯 Markdown，任何支持文件读取的 AI 工具都能用。你只需要把对应 Skill 文件的内容喂给 AI 即可。

**Q: 我一定要按 13 个角色的顺序走吗？**

A: 不需要。小项目可以跳过 UI 设计师、AI 工程师等非必需角色。workflow.md 中定义了哪些角色可以并行、哪些可以跳过。

**Q: 这个仓库和 Cursor Rules / .cursorrules 有什么区别？**

A: Cursor Rules 是"给一个 AI 看的提示"。这个仓库是"给 13 个 AI 看的 SOP"。一个是便签，一个是一整套岗位手册。

**Q: 我能自定义角色吗？**

A: 可以。每个 Skill 文件都是独立的，你可以修改、删除、添加角色。遵循 `shared/` 中的规范，保证新角色能无缝接入。

**Q: 需要什么水平的开发者才能用？**

A: 至少需要能独立完成一个完整模块的开发。你需要能看懂 AI 生成的代码、判断对错、做出技术决策。如果你还不会写代码，先用 AI 学会写代码再来。

---

## 🌟 开始使用

```bash
# 克隆仓库
git clone https://github.com/你的用户名/TraeSkill.git

# 在 TRAE 中导入 Skill
# 选择 skill.md 作为入口文件

# 启动你的第一个 AI 团队
# 输入: "你是一名产品经理，请阅读 product-manager.md"
```

---

## 📝 License

MIT

---

> **"一个人就是一支军队。"**
>
> 不再等招人、不再等排期、不再等沟通。
> 打开 TRAE，导入 Skill，启动你的 AI 研发团队。
>
> **现在就试试。**