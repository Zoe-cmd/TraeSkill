# Decision Log: TaskFlow 技术决策记录

<!--
Document: Decision Log
Version: 1.0.0
Author: Tech Lead
Created: 2026-07-11
Updated: 2026-07-11
Status: Active
-->

## 决策索引

| 编号 | 决策 | 日期 | 状态 |
|------|------|------|------|
| DEC-001 | 选择模块化单体架构而非微服务 | 2026-07-01 | Accepted |
| DEC-002 | 选择 PostgreSQL 而非 MySQL | 2026-07-01 | Accepted |
| DEC-003 | 选择 Zustand 而非 Redux | 2026-07-02 | Accepted |
| DEC-004 | 选择 NestJS 而非 Express | 2026-07-02 | Accepted |
| DEC-005 | AI 功能使用 OpenAI API 而非开源模型 | 2026-07-05 | Accepted |
| DEC-006 | 采用 BullMQ 而非 RabbitMQ | 2026-07-05 | Accepted |
| DEC-007 | 前端使用 Tailwind CSS + Radix UI 组合 | 2026-07-06 | Accepted |
| DEC-008 | 采用 JWT RS256 算法而非 HMAC | 2026-07-08 | Accepted |

---

## DEC-001: 选择模块化单体架构而非微服务

### 元信息
| 字段 | 内容 |
|------|------|
| 决策编号 | DEC-001 |
| 决策日期 | 2026-07-01 |
| 决策者 | Solution Architect |
| 决策状态 | Accepted |
| 影响范围 | 整个系统架构 |
| 关联决策 | DEC-004, DEC-006 |

### 背景

TaskFlow 初期目标用户量在 500 DAU 以内，团队规模 5-8 人。需要快速开发、易于维护的架构。同时需要为未来可能的规模增长预留扩展空间。

### 决策

采用**模块化单体架构**，按业务领域（用户、项目、任务、通知、AI 等）划分为独立模块，每个模块内部高内聚，模块间通过接口（Interface）通信，预留微服务拆分接口。

### 理由

1. **开发效率优先**：单体架构在开发、调试、部署上比微服务简单得多，适合 5-8 人团队
2. **运维成本低**：只需要部署一个应用，而非数十个微服务
3. **模块化设计保证可拆分**：每个模块有独立的 Service、Controller、Repository，未来拆分时只需将模块独立部署并添加 RPC 通信层
4. **初期用户量小**：500 DAU 的流量，单体架构完全够用，无需微服务的复杂基础设施
5. **避免过早优化**：遵循 "Monolith First" 原则，在验证产品市场匹配后再考虑拆分

### 替代方案

| 方案 | 优点 | 缺点 | 放弃原因 |
|------|------|------|----------|
| 纯微服务 | 独立部署、独立扩展、技术栈灵活 | 运维复杂、网络延迟、调试困难、数据一致性难保证 | 5-8 人团队维护 10+ 微服务不现实，运维成本远超收益 |
| 模块化单体（已选） | 开发效率高、运维简单、模块化可拆分 | 单体部署（单点故障风险）、无法独立扩展单模块 | 风险可控，已通过 K8s 多副本 + 健康检查缓解单点故障 |
| 无架构模式 | 开发最快 | 无约束、代码混乱、难以维护 | 项目规模必然增长，无架构会导致后期重构成本极高 |

### 拆分时机

当满足以下任一条件时，考虑将高频模块拆分为独立微服务：

- 某个模块的日均请求量超过其他模块 10 倍以上
- 某个模块需要独立的技术栈（如 AI 推理需要 Python）
- 团队规模超过 15 人，需要按模块划分团队

---

## DEC-002: 选择 PostgreSQL 而非 MySQL

### 元信息
| 字段 | 内容 |
|------|------|
| 决策编号 | DEC-002 |
| 决策日期 | 2026-07-01 |
| 决策者 | Tech Lead / Database Engineer |
| 决策状态 | Accepted |
| 影响范围 | 数据层 |

### 背景

需要选择主数据库。TaskFlow 的数据模型中包含灵活的 JSON 结构（如排期方案数据、通知元数据），同时需要全文搜索能力和复杂的数据分析查询。

### 决策

选择 **PostgreSQL 16** 作为主数据库。

### 理由

对比 MySQL 8.0 和 PostgreSQL 16 的关键差异：

| 评估维度 | PostgreSQL 16 | MySQL 8.0 | 选择 |
|----------|--------------|-----------|------|
| JSON 支持 | JSONB 原生索引、GIN 索引、JSONPath 查询 | JSON 类型但无二进制存储，索引支持弱 | PostgreSQL 胜 |
| 全文搜索 | 内置 tsvector + GIN 索引 | 需要 InnoDB FULLTEXT 索引，功能有限 | PostgreSQL 胜 |
| 高级 SQL | 窗口函数、CTE、LATERAL JOIN、物化视图 | 窗口函数支持，但物化视图等不如 PG | PostgreSQL 胜 |
| 并发控制 | MVCC 实现更好，无读写锁冲突 | MVCC 但存在 gap lock 问题 | PostgreSQL 胜 |
| 扩展性 | PostGIS, pg_cron, pg_partman 等丰富扩展 | 扩展生态较弱 | PostgreSQL 胜 |
| 社区活跃度 | 非常活跃，Stack Overflow 排名前 2 | 活跃 | 平手 |
| ORM 支持 | TypeORM 原生支持，完美配合 | TypeORM 支持 | 平手 |

**核心原因：**

1. **JSONB 是关键需求**：`schedule_plans.plan_data` 和 `notifications.metadata` 需要灵活存储 JSON 数据，PostgreSQL 的 JSONB 支持 GIN 索引，查询性能远超 MySQL 的 JSON 类型
2. **全文搜索无需额外中间件**：MVP 阶段不需要 Elasticsearch，PostgreSQL 内置的全文搜索（tsvector + GIN 索引）足以满足需求
3. **团队经验**：两位后端工程师有 PostgreSQL 生产经验，MySQL 经验较少

### 替代方案

| 方案 | 优点 | 缺点 | 放弃原因 |
|------|------|------|----------|
| MySQL 8.0 | 简单、广泛使用 | JSON 支持弱、无内置全文搜索、高级 SQL 功能少 | 核心需求（JSONB、全文搜索）MySQL 不能满足 |
| MongoDB | 灵活 Schema、JSON 原生 | 无事务支持（早期版本）、缺少关系型查询能力、团队无经验 | 数据模型中有大量关系型数据（用户-项目-任务），不适合文档数据库 |
| PostgreSQL（已选） | 功能全面、性能优秀 | 学习曲线略高 | 最符合需求 |

---

## DEC-003: 选择 Zustand 而非 Redux

### 元信息
| 字段 | 内容 |
|------|------|
| 决策编号 | DEC-003 |
| 决策日期 | 2026-07-02 |
| 决策者 | Frontend Lead |
| 决策状态 | Accepted |
| 影响范围 | 前端状态管理 |

### 背景

前端需要管理客户端状态（用户认证状态、UI 状态、表单状态）。需要选择一个轻量但功能足够的状态管理方案。

### 决策

选择 **Zustand 4.x** 作为前端状态管理库。

### 理由

1. **极简 API**：创建一个 store 只需几行代码，无需 Provider、Reducer、Action Creator 等模板代码
2. **包体积仅 1KB**：对比 Redux Toolkit 的 11KB+，Zustand 对首屏加载影响极小
3. **TypeScript 原生支持**：类型推演完美，无需额外类型声明
4. **无需 Provider 包裹**：直接在组件中 `useStore()` 即可，减少组件树层级
5. **支持中间件**：内置 `persist`（localStorage 持久化）、`devtools`（Redux DevTools 调试）、`immer`（不可变更新）

### 实际代码对比

**Redux Toolkit:**
```typescript
// store.ts
const taskSlice = createSlice({
  name: 'tasks',
  initialState: { items: [], loading: false },
  reducers: {
    setTasks: (state, action) => { state.items = action.payload; },
    addTask: (state, action) => { state.items.push(action.payload); },
  },
});
// 需要 Provider + useDispatch + useSelector
```

**Zustand:**
```typescript
// store.ts
const useTaskStore = create((set) => ({
  items: [],
  setTasks: (items) => set({ items }),
  addTask: (task) => set((s) => ({ items: [...s.items, task] })),
}));
// 直接 useTaskStore((s) => s.items)
```

对于 TaskFlow 的客户端状态需求（用户认证、UI 状态、少量全局状态），Zustand 完全够用，且代码量减少约 60%。

### 替代方案

| 方案 | 优点 | 缺点 | 放弃原因 |
|------|------|------|----------|
| Redux Toolkit | 生态丰富、中间件多、DevTools 强大 | 模板代码多、包体积大、学习曲线陡 | 对 TaskFlow 这种中等规模应用过于繁重 |
| Zustand（已选） | 极简、轻量、TS 友好 | 生态不如 Redux 丰富 | 功能足够，简单就是美 |
| Jotai | 原子化状态、细粒度更新 | 对简单状态过于复杂 | Zustand 更直观 |
| Context API | 无额外依赖 | 性能问题（不必要重渲染）、不适合频繁更新 | 不适合复杂状态管理 |

---

## DEC-004: 选择 NestJS 而非 Express

### 元信息
| 字段 | 内容 |
|------|------|
| 决策编号 | DEC-004 |
| 决策日期 | 2026-07-02 |
| 决策者 | Tech Lead |
| 决策状态 | Accepted |
| 影响范围 | 后端框架 |

### 背景

需要选择后端框架。团队有 Node.js 经验，但之前主要使用 Express。项目规模预计中等（10 个模块，50+ API 端点），需要良好的架构约束。

### 决策

选择 **NestJS 10.x** 作为后端框架。

### 理由

1. **企业级架构约束**：Module/Controller/Service/Provider 的分层设计天然形成良好的代码组织，避免 Express "自由发挥"导致的项目混乱
2. **依赖注入 (DI)**：内置 IoC 容器，方便模块间解耦和单元测试
3. **装饰器丰富**：`@Get()`, `@Post()`, `@Body()`, `@Query()`, `@UseGuards()` 等声明式 API 极大提升开发效率
4. **守卫/拦截器/管道/过滤器**：AOP 切面编程，认证、日志、校验、异常处理等横切关注点统一管理
5. **全栈 TypeScript**：前后端共享类型定义（`packages/shared/types`），减少接口对接错误
6. **OpenAPI (Swagger) 自动生成**：`@nestjs/swagger` 装饰器自动生成 API 文档，无需额外维护

### 实际场景对比

**Express 实现认证 + 校验：**
```typescript
// 需要手动在每个路由中编写认证和校验逻辑
app.post('/tasks', authenticate, validate(createTaskSchema), async (req, res) => {
  // 逻辑分散，容易遗漏
});
```

**NestJS 实现：**
```typescript
@Post()
@UseGuards(JwtAuthGuard, ProjectMemberGuard)
async create(@Body() dto: CreateTaskDto, @CurrentUser() user: User) {
  return this.tasksService.create(dto, user);
}
// 校验由 ValidationPipe 自动处理，认证由 Guard 统一管理
```

### 替代方案

| 方案 | 优点 | 缺点 | 放弃原因 |
|------|------|------|----------|
| Express | 简单、灵活、生态最大 | 缺少架构约束、无内置 DI、需手动管理中间件 | 10 个模块的项目使用 Express 会很快变得混乱 |
| NestJS（已选） | 企业级架构、DI、装饰器、全栈 TS | 学习曲线、一定的抽象开销 | 架构优势远大于学习成本 |
| Fastify | 高性能、插件系统 | 生态不如 Express/NestJS，缺少架构约束 | 性能优势不明显（NestJS 也可用 Fastify 适配器） |

---

## DEC-005: AI 功能使用 OpenAI API 而非开源模型

### 元信息
| 字段 | 内容 |
|------|------|
| 决策编号 | DEC-005 |
| 决策日期 | 2026-07-05 |
| 决策者 | Tech Lead / Product Manager |
| 决策状态 | Accepted |
| 影响范围 | AI 智能排期、任务摘要功能 |

### 背景

智能排期建议功能需要 AI 模型分析任务上下文后生成排期方案。需要选择 AI 服务提供商。同时需要 AI 生成任务摘要。预计每周调用次数：MVP 阶段约 500 次。

### 决策

使用 **OpenAI GPT-4o API** 作为 AI 服务提供商。

### 理由

1. **推理质量业界领先**：GPT-4o 在结构化输出任务上的表现优于开源模型，能准确理解任务依赖关系并生成合理的 JSON 排期方案
2. **API 稳定可靠**：99.5% 可用性 SLA，响应时间 P95 < 2 秒，无需自建维护
3. **成本可控**：GPT-4o 输入 $5/1M tokens，输出 $15/1M tokens。每次排期请求约 2000 tokens，成本约 $0.02，每周 500 次约 $10
4. **结构化输出**：支持 JSON Mode，确保返回格式符合预期，减少解析错误
5. **快速上线**：无需 GPU 服务器采购和部署，集成 SDK 即可使用

### 降级策略

当 OpenAI API 不可用时，自动降级为基于规则的排期算法（按优先级排序 + 简单负载均衡），保证功能可用性。

### 成本分析

| 场景 | 月调用量 | 单次成本 | 月成本 |
|------|----------|----------|--------|
| MVP 阶段 | 2000 次 | $0.02 | $40 |
| 增长期 (100 团队) | 10000 次 | $0.02 | $200 |
| 成熟期 (500 团队) | 50000 次 | $0.02 | $1000 |

后期成本可通过以下方式优化：
- 使用 GPT-4o-mini 处理简单摘要任务（成本降低 90%）
- 缓存常见排期模式（减少 API 调用）

### 替代方案

| 方案 | 优点 | 缺点 | 放弃原因 |
|------|------|------|----------|
| 开源模型 (Llama 3) | 无 API 费用、数据隐私 | 需自建 GPU 集群（至少 $5000/月）、推理速度慢、维护成本高 | MVP 阶段投入产出比极低 |
| OpenAI API（已选） | 推理质量高、稳定、成本低 | 数据传输隐私风险、依赖第三方 | 通过数据脱敏缓解隐私风险 |
| Claude API | 推理质量高、长上下文 | 成本略高于 GPT-4o | 团队已有 OpenAI 使用经验 |
| 不使用 AI | 无成本、无依赖 | 无法实现智能排期核心功能 | 智能排期是核心差异化功能 |

---

## DEC-006: 采用 BullMQ 而非 RabbitMQ

### 元信息
| 字段 | 内容 |
|------|------|
| 决策编号 | DEC-006 |
| 决策日期 | 2026-07-05 |
| 决策者 | Tech Lead |
| 决策状态 | Accepted |
| 影响范围 | 异步任务处理 |

### 背景

系统需要异步处理以下任务：邮件发送、AI API 调用（避免阻塞 HTTP 请求）、通知推送。需要选择消息队列中间件。

### 决策

选择 **BullMQ 5.x**（基于 Redis）作为消息队列。

### 理由

1. **零额外运维**：BullMQ 基于 Redis，而 Redis 已在技术栈中，无需额外部署 RabbitMQ 实例
2. **功能完整**：支持延迟任务、重试（指数退避）、优先级、任务进度、速率限制
3. **NestJS 原生集成**：`@nestjs/bullmq` 模块提供 `@Processor()`, `@Process()` 装饰器，与 NestJS 无缝集成
4. **足够可靠**：Redis 持久化（AOF + RDB）保证任务不丢失，满足 TaskFlow 的可靠性需求
5. **开发体验好**：Bull Board 提供可视化任务管理界面，方便调试

### 实际使用场景

```typescript
// 发送邮件 - 延迟任务，失败重试 3 次
@Processor('email')
export class EmailProcessor {
  @Process('send-welcome')
  async handleWelcomeEmail(job: Job<WelcomeEmailData>) {
    await this.emailService.send(job.data);
  }
}

// 在 Service 中添加任务
await this.emailQueue.add('send-welcome', { email: 'user@example.com' }, {
  attempts: 3,
  backoff: { type: 'exponential', delay: 2000 },
});
```

### 替代方案

| 方案 | 优点 | 缺点 | 放弃原因 |
|------|------|------|----------|
| RabbitMQ | 功能丰富、企业级可靠性、多协议支持 | 需额外部署、运维成本高、学习曲线陡 | MVP 阶段不必要，Redis 已能满足需求 |
| BullMQ（已选） | 基于 Redis、零额外运维、功能完整 | 依赖 Redis 可用性、大量任务时性能不如 RabbitMQ | 初期任务量不大，Redis 够用 |
| AWS SQS | 全托管、高可用 | 功能有限（无延迟任务、优先级）、AWS 锁定 | 不如 BullMQ 灵活 |

---

## DEC-007: 前端使用 Tailwind CSS + Radix UI 组合

### 元信息
| 字段 | 内容 |
|------|------|
| 决策编号 | DEC-007 |
| 决策日期 | 2026-07-06 |
| 决策者 | Frontend Lead |
| 决策状态 | Accepted |
| 影响范围 | 前端 UI 开发 |

### 背景

前端需要构建一个专业、一致、可访问的 UI。需要选择 CSS 框架和 UI 组件库。

### 决策

选择 **Tailwind CSS 3.x + Radix UI 1.x** 组合。

### 理由

1. **Tailwind CSS - 设计系统一致性**：原子化 CSS 类天然保证设计一致性，自定义 Design Tokens（颜色、间距、字体）统一管理
2. **Radix UI - 无样式 + 无障碍优先**：提供完全可访问的 UI 原语（Dialog、Dropdown、Tabs 等），但无默认样式，可自由用 Tailwind 定制
3. **完美配合**：Radix 提供行为和无障碍，Tailwind 提供样式，各司其职
4. **包体积可控**：按需引入，不会被没用到的组件拖累
5. **定制自由度高**：不会出现"一看就是 MUI 做的"这种同质化问题

### 替代方案

| 方案 | 优点 | 缺点 | 放弃原因 |
|------|------|------|----------|
| MUI (Material UI) | 组件丰富、开箱即用 | 样式厚重、定制困难、Material Design 风格明显、包体积大 | 产品需要自己的设计语言，而非 Material Design |
| CSS Modules | 原生的 CSS 隔离 | 需要写大量自定义 CSS，开发效率低 | 缺少原子化 CSS 的开发效率 |
| Tailwind + Radix（已选） | 设计一致性、无障碍、包体积小 | 需要学习 Tailwind 类名 | 最佳平衡 |
| Ant Design | 组件极其丰富，适合后台 | 包体积巨大（~500KB）、风格固定 | 产品是 SaaS 而非企业内部后台 |

---

## DEC-008: 采用 JWT RS256 算法而非 HMAC

### 元信息
| 字段 | 内容 |
|------|------|
| 决策编号 | DEC-008 |
| 决策日期 | 2026-07-08 |
| 决策者 | Tech Lead |
| 决策状态 | Accepted |
| 影响范围 | 认证系统 |

### 背景

需要选择 JWT 签名算法。考虑未来可能需要微服务拆分，各服务需要独立验证 Token。

### 决策

选择 **RS256 (RSA 非对称加密)** 作为 JWT 签名算法。

### 理由

1. **非对称加密**：私钥签名（Auth Service），公钥验证（其他 Service），公钥可安全分发
2. **微服务友好**：未来拆分微服务时，各服务只需持有公钥即可独立验证 Token，无需共享密钥
3. **安全性**：私钥泄露不意味着攻击者可以伪造 Token（除非同时获取公钥）
4. **密钥轮换**：支持密钥轮换（Key Rotation），通过 JWKS 端点分发公钥

### 替代方案

| 方案 | 优点 | 缺点 | 放弃原因 |
|------|------|------|----------|
| HS256 (HMAC) | 简单、性能好 | 对称密钥，所有服务共享密钥，密钥泄露风险高 | 不适合微服务架构 |
| RS256（已选） | 非对称、微服务友好、密钥轮换 | 性能略低（但可忽略） | 最适合当前和未来需求 |
| EdDSA | 性能更好、密钥更短 | 生态支持不如 RSA 成熟 | 等生态成熟后可迁移 |

---

**版本: 1.0.0 | 作者: Tech Lead | 日期: 2026-07-11**