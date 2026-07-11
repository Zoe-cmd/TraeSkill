# Documentation Standard - 统一文档规范

## 概述

本文档定义了所有 AI Agent 在创建和更新文档时必须遵守的统一文档规范。在 Documentation Driven Development 模式下，文档是唯一真实来源（Source of Truth），因此文档质量至关重要。

## 适用范围

- 本规范适用于所有 AI Agent 产生的所有文档
- 包括但不限于：PRD、架构文档、API 文档、数据库文档、测试文档、部署文档、决策日志等
- 所有 Agent 在编写文档时必须遵守本规范

## 语言规范

本规范文档及所有相关文档均使用简体中文编写。技术术语（如 API、REST、SQL、JSON、HTTP、Git、CI/CD、PRD、MVP 等）保留英文原文。代码块中的代码和注释遵循对应语言的规范。文档中的代码标识符（变量名、函数名、类名等）使用英文。

## 文档原则

| 原则 | 说明 |
|------|------|
| 完整 | 文档必须完整，不得使用占位符、省略号、TODO 标记 |
| 准确 | 文档内容必须与代码实现一致 |
| 及时 | 文档必须随代码变更同步更新 |
| 清晰 | 文档结构清晰，易于阅读和理解 |
| 可操作 | 文档提供可执行的步骤，而非模糊的描述 |
| 版本化 | 文档有版本号，记录变更历史 |
| 单一来源 | 每个信息只有一个权威来源，避免重复和不一致 |

## 文档格式

### 文件格式

所有文档统一使用 Markdown 格式（`.md`）。

### 文件编码

所有文档使用 UTF-8 编码，无 BOM。

### 文件命名

```
{type}-{description}.md

type: prd, architecture, api, database, test, deployment, decision-log, todo
description: 简短描述，使用 kebab-case
```

示例：
```
prd.md
architecture.md
api-spec.md
database-schema.md
test-plan.md
deployment-plan.md
decision-log.md
todo.md
```

## 文档结构

### 通用文档结构

所有文档应遵循以下结构：

```markdown
# 文档标题

## 概述
（1-3 段说明文档的目的和范围）

## 核心内容
（根据文档类型组织章节）

## 附录
（可选：参考链接、术语表、变更历史等）
```

### 文档元信息

每个文档必须在开头包含元信息（使用 HTML 注释，不影响渲染）：

```markdown
<!--
Document: PRD - Product Requirements Document
Version: 1.0.0
Author: Product Manager
Created: 2026-07-11
Updated: 2026-07-11
Status: Draft | Review | Approved | Final
-->
```

### 标题层级

| 层级 | 用途 | 示例 |
|------|------|------|
| H1 (#) | 文档标题 | `# PRD - Product Requirements Document` |
| H2 (##) | 主要章节 | `## 功能需求` |
| H3 (###) | 子章节 | `### 用户注册` |
| H4 (####) | 子子章节 | `#### 邮箱验证` |
| H5 (#####) | 细节 | 不推荐使用 |
| H6 (######) | 细节 | 不推荐使用 |

### 目录

文档超过 500 行时，必须在 H1 标题后添加目录：

```markdown
# 文档标题

## 目录

- [概述](#概述)
- [功能需求](#功能需求)
  - [用户注册](#用户注册)
  - [用户登录](#用户登录)
- [非功能需求](#非功能需求)
- [附录](#附录)
```

## 内容规范

### 表格规范

所有表格必须有表头，表头与内容用 `|------|` 分隔：

```markdown
| 列名 1 | 列名 2 | 列名 3 |
|--------|--------|--------|
| 内容 1 | 内容 2 | 内容 3 |
| 内容 4 | 内容 5 | 内容 6 |
```

### 代码块规范

代码块必须指定语言：

```markdown
\`\`\`typescript
const user = await userService.getUserById('user-123');
\`\`\`
```

支持的语言标记：
- `typescript`, `javascript`, `python`, `java`, `go`, `rust`
- `sql`, `yaml`, `json`, `xml`, `html`, `css`
- `bash`, `shell`, `powershell`
- `markdown`, `text`
- `mermaid`（图表）

### 列表规范

- 无序列表使用 `-`
- 有序列表使用 `1.`
- 嵌套列表缩进 2 空格
- 列表项之间不空行（除非列表项包含多段落）

### 链接规范

```markdown
[链接文本](相对路径或 URL)

[PRD 文档](./prd.md)
[API 规范](../shared/api-standard.md)
[外部文档](https://example.com/docs)
```

### 图片规范

```markdown
![替代文本](./images/architecture.png)

*图 1: 系统架构图*
```

### 引用规范

```markdown
> 这是一个引用块。
> 可以有多行。
```

### 强调规范

```markdown
**粗体** — 用于重要概念
*斜体* — 用于术语、文件名
`代码` — 用于代码、命令、路径
```

### Mermaid 图表

```markdown
\`\`\`mermaid
graph TD
    A[开始] --> B[处理]
    B --> C[结束]
\`\`\`
```

## 文档质量规范

### 完整性检查

- 无占位符（如 "TODO"、"待补充"、"略"）
- 无省略号（如 "..."、"等等"）
- 无模糊描述（如 "可能"、"大概"、"基本上"）
- 所有引用链接有效
- 所有代码示例可运行

### 准确性检查

- 文档内容与代码实现一致
- API 文档与实际 API 一致
- 数据库文档与实际 Schema 一致
- 版本号与标签一致

### 一致性检查

- 术语使用一致（同一概念使用相同术语）
- 格式一致（所有表格、代码块、列表格式统一）
- 命名一致（文件、变量、函数命名统一）

### 可读性检查

- 句子简洁明了
- 段落不宜过长（不超过 5 行）
- 使用主动语态
- 使用肯定句式
- 结构清晰，层次分明

## 文档生命周期

### 状态流转

```
Draft → Review → Approved → Final → Archived
  ↑        ↓         ↓
  └── Rework ────────┘
```

### 状态定义

| 状态 | 说明 | 可修改者 |
|------|------|----------|
| Draft | 初稿，正在编写 | Author |
| Review | 审查中，等待反馈 | Reviewer |
| Rework | 需要修改 | Author |
| Approved | 已批准 | 不可修改 |
| Final | 最终版本 | 不可修改 |
| Archived | 已归档 | 不可修改 |

### 版本管理

| 版本号变更 | 触发条件 |
|------------|----------|
| Major | 文档结构重大变更 |
| Minor | 新增章节或重大内容变更 |
| Patch | 修正错误、补充细节 |

## 文档类型规范

### PRD 文档

参见 `templates/prd-template.md`

### 架构文档

参见 `templates/architecture-template.md`

### API 文档

参见 `templates/api-template.md`

### 数据库文档

参见 `templates/database-template.md`

### 测试文档

参见 `templates/test-template.md`

### 部署文档

参见 `templates/deployment-template.md`

### 决策日志

参见 `templates/decision-log-template.md`

### 任务清单

参见 `templates/todo-template.md`

## 文档组织

### 目录结构

```
docs/
├── project-plan.md
├── prd.md
├── design-system.md
├── user-flows.md
├── wireframes.md
├── architecture.md
├── api-spec.md
├── database-schema.md
├── database-migration-plan.md
├── test-plan.md
├── test-report.md
├── bug-report.md
├── security-audit-report.md
├── security-recommendations.md
├── code-review-report.md
├── refactoring-suggestions.md
├── deployment-plan.md
├── project-summary.md
├── lessons-learned.md
├── maintenance-plan.md
├── decision-log.md
└── todo.md
```

### 文档索引

每个文档应清楚地列出：
- 依赖的文档（Required Documents）
- 引用的文档（Referenced Documents）
- 产生的文档（Outputs）

## 文档审查

### 审查检查点

| 检查点 | 说明 |
|--------|------|
| 完整性 | 所有必填章节是否完整 |
| 准确性 | 内容是否与实际一致 |
| 一致性 | 术语、格式是否一致 |
| 可读性 | 是否易于理解 |
| 可操作性 | 是否提供可执行的步骤 |
| 格式合规 | 是否符合 Markdown 规范 |

### 审查流程

1. Author 完成文档
2. Author 自检（Self Review）
3. Author 提交 Review
4. Reviewer 审查
5. Reviewer 提供反馈
6. Author 修改（如需）
7. Reviewer 批准
8. 文档状态更新为 Approved

## 反模式

| 反模式 | 说明 | 正确做法 |
|--------|------|----------|
| 占位符文档 | 文档包含 TODO、待补充 | 完成所有内容后再提交 |
| 过期文档 | 文档与代码不一致 | 代码变更时同步更新文档 |
| 重复文档 | 同一信息在多个文档中 | 使用引用，保持单一来源 |
| 冗长文档 | 文档过长，难以阅读 | 拆分为多个文档或使用目录 |
| 模糊文档 | 使用模糊的描述 | 使用具体、可操作的描述 |
| 无结构文档 | 文档没有清晰的结构 | 遵循标准文档结构 |
| 无版本文档 | 文档没有版本号 | 添加版本号和变更历史 |

## 文档版本管理策略

### 版本号规范

所有文档使用语义化版本号（Semantic Versioning）：

```
MAJOR.MINOR.PATCH

示例: 2.1.3
```

| 版本号变更 | 触发条件 | 示例 |
|------------|----------|------|
| Major (X.0.0) | 文档结构重大变更、章节重新组织 | v1.0.0 → v2.0.0：将"功能需求"拆分为"核心功能"和"辅助功能"两个独立章节 |
| Minor (x.Y.0) | 新增章节、重大内容变更 | v1.2.0 → v1.3.0：新增"国际化需求"章节 |
| Patch (x.y.Z) | 修正错误、补充细节、格式调整 | v1.2.0 → v1.2.1：修正 API 端点 URL 中的拼写错误 |

### 版本历史记录

每个文档必须在末尾维护版本历史：

```markdown
## 版本历史

| 版本 | 日期 | 变更说明 | 作者 |
|------|------|----------|------|
| 1.3.0 | 2026-07-15 | 新增"国际化需求"章节，补充多语言支持需求 | Product Manager |
| 1.2.1 | 2026-07-13 | 修正 API 端点 URL 拼写错误（/usr → /users） | Backend Engineer |
| 1.2.0 | 2026-07-12 | 新增"支付功能"需求详情，补充 3 个用户故事 | Product Manager |
| 1.1.0 | 2026-07-11 | 更新非功能需求，新增性能指标和兼容性要求 | Solution Architect |
| 1.0.0 | 2026-07-10 | 初始版本，完成 MVP 范围定义 | Product Manager |
```

### 文档分支管理

对于大型文档，推荐使用 Git 分支管理文档变更：

```
main（当前生效版本）
  │
  ├── feature/prd-v2.0（重大改版分支）
  │     ├── 新增章节
  │     ├── 重组结构
  │     └── 合并到 main 后发布 v2.0.0
  │
  ├── fix/prd-typo（修正分支）
  │     ├── 修正拼写错误
  │     └── 合并到 main 后发布 v1.2.1
  │
  └── docs/prd-api-update（文档更新分支）
        ├── 更新 API 相关章节
        └── 合并到 main 后发布 v1.3.0
```

### 文档引用版本锁定

当文档 A 引用文档 B 时，应锁定文档 B 的版本号，避免文档 B 变更后文档 A 出现不一致：

```markdown
## 引用文档

| 文档 | 版本 | 状态 |
|------|------|------|
| PRD 文档 | v1.3.0 | Approved |
| 架构设计文档 | v2.1.0 | Approved |
| API 规范文档 | v1.0.0 | Draft |
```

## 文档评审流程

### 评审角色和职责

| 角色 | 职责 | 评审重点 |
|------|------|----------|
| Author | 文档编写者 | 确保内容完整、准确 |
| Peer Reviewer | 同级别审查者 | 内容正确性、逻辑一致性 |
| Tech Lead | 技术负责人 | 技术决策合理性、架构合规 |
| Product Manager | 产品经理 | 业务需求对齐、用户体验 |
| QA Engineer | 质量工程师 | 可测试性、验收标准清晰度 |

### 评审流程

```
Author 完成文档初稿
    ↓
Author 自检（Self Review）
    │  - 检查完整性（无占位符）
    │  - 检查准确性（与代码一致）
    │  - 检查格式（Markdown 规范）
    ↓
Author 提交评审请求
    │  - 标注文档版本和状态为 Review
    │  - 通知相关评审者
    ↓
Peer Reviewer 执行评审
    │  - 检查内容正确性
    │  - 检查逻辑一致性
    │  - 提供修改建议
    ↓
Peer Reviewer 提交评审意见
    │  - Approved（批准）
    │  - Changes Requested（需要修改）
    │  - Rejected（拒绝）
    ↓
Author 处理评审意见
    │  - 修改文档
    │  - 回应每一条评审意见（同意/不同意/已修改）
    ↓
Tech Lead 最终审批（仅关键文档）
    │  - PRD、架构文档、安全文档
    ↓
文档状态更新为 Approved
    ↓
通知相关方文档已生效
```

### 评审意见模板

```markdown
## 文档评审意见

### 评审信息
| 项目 | 内容 |
|------|------|
| 评审编号 | DR-20260711-001 |
| 文档名称 | PRD 文档 v1.2.0 |
| 评审者 | Tech Lead |
| 评审日期 | 2026-07-11 |
| 评审结论 | Changes Requested |

### 评审意见

#### 意见 1: 缺少并发用户场景描述
- **位置**: 第 3.2 节"订单模块"
- **严重程度**: Medium
- **问题**: 没有描述高并发场景下的行为（如秒杀活动期间 1000 人同时下单）
- **建议**: 补充并发场景的业务规则，明确超卖处理策略

#### 意见 2: 性能指标需要更具体
- **位置**: 第 4.1 节"性能需求"
- **严重程度**: Low
- **问题**: "API 响应时间 < 200ms"没有说明是平均值还是 P95
- **建议**: 明确为 "API 响应时间 P95 < 200ms，P99 < 500ms"

#### 意见 3: 数据模型缺少索引说明
- **位置**: 第 5.1 节"数据实体"
- **严重程度**: Medium
- **问题**: 只列出了字段，没有说明哪些字段需要索引
- **建议**: 补充每个实体的索引策略

### 评审结论
- [ ] Approved
- [x] Changes Requested
- [ ] Rejected

请修改后重新提交评审，重点关注 Medium 级别的意见 1 和 3。
```

## 文档废弃和归档规则

### 废弃规则

当文档不再适用时，不应直接删除，而应遵循以下规则：

| 状态 | 说明 | 操作 |
|------|------|------|
| Deprecated | 文档已过时，但仍有参考价值 | 在文档头部添加废弃标记，保留文件 |
| Archived | 文档已归档，不再使用 | 移动到 `docs/archive/` 目录 |
| Superseded | 被新文档取代 | 在文档头部添加取代说明，指向新文档 |

### 废弃标记格式

```markdown
<!--
Document: PRD - Product Requirements Document
Version: 1.0.0
Status: Deprecated
Deprecated Date: 2026-07-11
Superseded By: docs/prd-v2.md
Reason: 产品方向调整，MVP 范围重新定义
-->
```

### 归档规则

| 规则 | 说明 |
|------|------|
| 归档目录 | `docs/archive/` |
| 归档命名 | `{原文件名}-{归档日期}.md` |
| 归档索引 | 维护 `docs/archive/README.md` 记录所有归档文档 |
| 保留期限 | 归档文档至少保留 2 年 |
| 定期清理 | 每季度清理一次超过保留期限的归档文档 |

### 归档索引

```markdown
# 文档归档索引

| 原文件名 | 归档日期 | 归档原因 | 保留至 |
|----------|----------|----------|--------|
| prd-v1.0.0.md | 2026-07-11 | 被 prd-v2.0.0.md 取代 | 2028-07-11 |
| architecture-v1.0.0.md | 2026-06-15 | 架构重构，不再适用 | 2028-06-15 |
| api-spec-v0.9.0.md | 2026-05-20 | 发布 v1.0.0 正式版 | 2028-05-20 |
```

## 文档质量检查清单

### 内容质量检查

- [ ] 所有章节标题与实际内容匹配
- [ ] 无占位符（如 TODO、待补充、TBD、N/A）
- [ ] 无省略号（如 "..."、"等等"、"之类的"）
- [ ] 无模糊描述（如 "可能"、"大概"、"基本上"、"差不多"）
- [ ] 所有表格完整填写，无空单元格
- [ ] 所有代码示例可运行，语法正确
- [ ] 所有 Mermaid 图表可正确渲染
- [ ] 所有外部链接有效
- [ ] 所有内部引用路径正确
- [ ] 技术术语使用一致

### 格式质量检查

- [ ] 文件编码为 UTF-8 无 BOM
- [ ] 标题层级正确（H1 → H2 → H3 递进）
- [ ] 表格对齐正确，表头与内容用 `|------|` 分隔
- [ ] 代码块指定了语言标记
- [ ] 列表缩进一致（2 空格）
- [ ] 无多余的空格和空行
- [ ] 链接格式正确（`[文本](路径)`）
- [ ] 图片有替代文本

### 结构质量检查

- [ ] 文档元信息完整（版本、作者、日期、状态）
- [ ] 超过 500 行的文档包含目录
- [ ] 章节结构合理，逻辑清晰
- [ ] 引用文档已列出，版本号正确
- [ ] 版本历史完整

### 可读性检查

- [ ] 段落不超过 5 行
- [ ] 句子简洁明了，无过长句子
- [ ] 使用主动语态
- [ ] 使用肯定句式
- [ ] 首次出现的缩写有全称说明
- [ ] 复杂概念有图表辅助说明

### 一致性检查

- [ ] 同一概念使用相同的术语
- [ ] 同一表头使用相同的列名
- [ ] 同一格式使用相同的样式
- [ ] 文档之间的引用关系一致
- [ ] 文档与代码实现一致

### 专业性检查

- [ ] 中文内容语法正确，无错别字
- [ ] 技术术语使用正确
- [ ] 数据准确，有来源说明
- [ ] 图表清晰，有编号和标题
- [ ] 结论有依据，不主观臆断

---

**本规范是所有 AI Agent 创建和更新文档的基础标准。Documentation is the Source of Truth. 每个 Agent 必须严格遵守本规范。**