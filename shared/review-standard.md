# Review Standard - 统一审查规范

## 概述

本文档定义了所有 AI Agent 在进行代码审查、文档审查和架构审查时必须遵守的统一审查规范。审查是质量保证体系的核心环节。

## 适用范围

- 本规范适用于所有类型的审查：代码审查、文档审查、架构审查、安全审查
- 所有 Code Reviewer 和其他执行审查的 Agent 必须遵守本规范

## 语言规范

本规范文档及所有相关文档、代码注释、提交信息均使用简体中文编写。审查报告和审查意见使用中文。技术术语（如 API、REST、SQL、JSON、HTTP、JWT、PR、MR、CI/CD 等）保留英文原文。

## 审查原则

| 原则 | 说明 |
|------|------|
| 建设性 | 审查的目的是改进，而非批评 |
| 客观性 | 基于规范和标准，而非个人偏好 |
| 及时性 | 审查应在 24 小时内完成 |
| 完整性 | 审查覆盖所有检查点 |
| 可追溯 | 所有审查意见有记录 |
| 分级管理 | 问题按严重程度分级处理 |

## 审查类型

### 按审查对象分类

| 类型 | 执行者 | 审查内容 |
|------|--------|----------|
| 代码审查 | Code Reviewer | 代码质量、规范合规、安全性 |
| 文档审查 | Code Reviewer / Tech Lead | 文档完整性、准确性、一致性 |
| 架构审查 | Tech Lead / Solution Architect | 架构合规、技术选型、设计模式 |
| 安全审查 | Security Engineer | 安全漏洞、数据保护、合规 |
| 测试审查 | QA Engineer | 测试覆盖率、测试质量 |

### 按审查时机分类

| 时机 | 说明 |
|------|------|
| 提交前审查 | Self Review，Agent 自我检查 |
| 合并前审查 | Peer Review，Code Reviewer 审查 |
| 发布前审查 | Release Review，Tech Lead 审查 |
| 定期审查 | Scheduled Review，定期整体审查 |

## 审查流程

### 标准审查流程

```
1. Author 提交审查请求
   ↓
2. Reviewer 接收审查任务
   ↓
3. Reviewer 审查
   ├── 代码审查
   ├── 文档审查
   ├── 规范检查
   └── 安全检查
   ↓
4. Reviewer 编写审查报告
   ↓
5. Reviewer 提交审查意见
   ↓
6. Author 处理审查意见
   ├── 修复 Critical 和 High 问题
   ├── 讨论 Medium 问题
   └── 记录 Low 问题（可延后处理）
   ↓
7. Reviewer 验证修复
   ↓
8. Reviewer 批准
```

### 审查时间规范

| 审查类型 | 响应时间 | 完成时间 |
|----------|----------|----------|
| 代码审查 | < 4 小时 | < 24 小时 |
| 文档审查 | < 4 小时 | < 24 小时 |
| 架构审查 | < 8 小时 | < 48 小时 |
| 安全审查 | < 8 小时 | < 48 小时 |

## 问题分级

### 严重程度

| 级别 | 标识 | 说明 | 处理要求 |
|------|------|------|----------|
| Critical | 🔴 | 阻塞性问题，必须立即修复 | 必须修复，阻止合并 |
| High | 🟠 | 重要问题，发布前必须修复 | 必须修复，阻止合并 |
| Medium | 🟡 | 一般问题，建议修复 | 建议修复，不阻止合并 |
| Low | 🟢 | 轻微问题，可延后处理 | 记录，可延后处理 |
| Info | 🔵 | 建议性意见，仅供参考 | 非必须 |

### 问题分类

| 分类 | 说明 | 示例 |
|------|------|------|
| 功能 | 功能逻辑错误 | 计算错误、条件判断错误 |
| 安全 | 安全漏洞 | SQL 注入、XSS、敏感信息泄露 |
| 性能 | 性能问题 | N+1 查询、内存泄漏 |
| 规范 | 不符合编码规范 | 命名错误、格式错误 |
| 架构 | 架构合规问题 | 违反分层原则、循环依赖 |
| 文档 | 文档问题 | 注释缺失、文档过期 |
| 测试 | 测试问题 | 测试缺失、覆盖率不足 |
| 可维护性 | 可维护性问题 | 代码重复、过度复杂 |

## 审查内容

### 代码审查检查点

#### 1. 功能正确性

- [ ] 代码是否实现了预期的功能
- [ ] 边界条件是否正确处理
- [ ] 错误情况是否正确处理
- [ ] 并发场景是否正确处理
- [ ] 数据类型是否正确

#### 2. 代码质量

- [ ] 代码是否易于理解
- [ ] 函数是否单一职责
- [ ] 是否有重复代码
- [ ] 是否有过度设计
- [ ] 是否有过度优化

#### 3. 命名规范

- [ ] 类名是否符合 PascalCase
- [ ] 函数名是否符合 camelCase
- [ ] 常量是否符合 UPPER_SNAKE_CASE
- [ ] 文件名是否符合 kebab-case
- [ ] 名称是否自解释

#### 4. 代码结构

- [ ] 文件结构是否合理
- [ ] 模块划分是否合理
- [ ] 依赖关系是否合理
- [ ] 是否有循环依赖

#### 5. 错误处理

- [ ] 所有错误是否被处理
- [ ] 错误信息是否清晰
- [ ] 错误类型是否正确
- [ ] 是否吞没了错误

#### 6. 安全性

- [ ] 是否使用参数化查询
- [ ] 用户输入是否验证
- [ ] 是否有权限检查
- [ ] 敏感信息是否安全

#### 7. 性能

- [ ] 是否有 N+1 查询
- [ ] 是否有不必要的循环
- [ ] 是否有内存泄漏
- [ ] 缓存是否合理

#### 8. 测试

- [ ] 是否有单元测试
- [ ] 测试覆盖率是否达标
- [ ] 测试是否覆盖边界条件
- [ ] 测试是否独立

#### 9. 文档

- [ ] 注释是否完整
- [ ] 注释是否准确
- [ ] API 文档是否更新
- [ ] README 是否更新

#### 10. 规范合规

- [ ] 是否符合 Coding Standard
- [ ] 是否符合 API Standard
- [ ] 是否符合 Database Standard
- [ ] 是否符合 Git Standard

### 文档审查检查点

- [ ] 文档结构是否完整
- [ ] 内容是否准确
- [ ] 是否与代码一致
- [ ] 是否有占位符
- [ ] 是否有过期内容
- [ ] 术语是否一致
- [ ] 格式是否统一
- [ ] 链接是否有效

### 架构审查检查点

- [ ] 是否符合架构设计
- [ ] 模块划分是否合理
- [ ] 接口定义是否清晰
- [ ] 依赖关系是否正确
- [ ] 技术选型是否合理
- [ ] 是否有过度设计
- [ ] 是否有扩展性
- [ ] 是否有性能瓶颈

## 审查报告

### 报告格式

```markdown
# Code Review Report

## 审查信息

| 项目 | 内容 |
|------|------|
| 审查编号 | CR-20260711-001 |
| 审查类型 | 代码审查 |
| 审查对象 | feature/user-auth |
| 审查分支 | feature/user-auth → develop |
| 审查者 | Code Reviewer |
| 审查日期 | 2026-07-11 |
| 审查状态 | Passed / Changes Requested |

## 审查摘要

| 指标 | 数值 |
|------|------|
| 变更文件数 | 12 |
| 总变更行数 | +456 / -123 |
| Critical 问题 | 0 |
| High 问题 | 1 |
| Medium 问题 | 3 |
| Low 问题 | 5 |
| Info 建议 | 2 |

## 问题列表

### Critical
无

### High
#### H-001: SQL 注入风险
- **文件**: `src/modules/user/user.repository.ts:45`
- **问题**: 使用字符串拼接构建 SQL 查询
- **建议**: 使用参数化查询
- **代码**:
  ```typescript
  // ❌ 当前代码
  const query = `SELECT * FROM users WHERE email = '${email}'`;
  
  // ✅ 建议修改
  const query = 'SELECT * FROM users WHERE email = $1';
  const result = await db.query(query, [email]);
  ```

### Medium
#### M-001: 缺少输入验证
- **文件**: `src/modules/user/user.controller.ts:23`
- **问题**: createUser 端点没有对请求体进行验证
- **建议**: 添加 DTO 验证

#### M-002: 缺少错误处理
- **文件**: `src/modules/user/user.service.ts:67`
- **问题**: 数据库查询没有错误处理
- **建议**: 添加 try-catch 和适当错误抛出

#### M-003: 日志级别不正确
- **文件**: `src/modules/user/user.service.ts:34`
- **问题**: 使用 console.log 而非 logger
- **建议**: 使用统一的 logger，设置合适的日志级别

### Low
#### L-001: 注释不完整
- **文件**: `src/modules/user/user.service.ts:12`
- **问题**: getUserById 方法缺少 JSDoc 注释
- **建议**: 添加完整的 JSDoc 注释

## 审查结论

- [ ] Approved（批准）
- [x] Changes Requested（需要修改）
- [ ] Rejected（拒绝）

**需要修改后再提交审查。必须修复 High 问题 H-001。建议修复 Medium 问题 M-001 至 M-003。**
```

### 完整审查报告示例

以下是一个更完整的代码审查报告，包含更多细节和真实场景：

```markdown
# Code Review Report: feature/order-management

## 审查信息

| 项目 | 内容 |
|------|------|
| 审查编号 | CR-20260711-002 |
| 审查类型 | 代码审查 + 安全审查 |
| 审查对象 | feature/order-management |
| 审查分支 | feature/order-management → develop |
| 审查者 | Code Reviewer |
| 审查日期 | 2026-07-11 |
| 审查状态 | Changes Requested |

## 审查摘要

| 指标 | 数值 |
|------|------|
| 变更文件数 | 18 |
| 总变更行数 | +892 / -234 |
| Critical 问题 | 1 |
| High 问题 | 2 |
| Medium 问题 | 4 |
| Low 问题 | 6 |
| Info 建议 | 3 |

## 问题列表

### Critical

#### C-001: 订单金额计算存在精度问题
- **文件**: `src/modules/order/order.service.ts:156`
- **严重程度**: Critical
- **分类**: 功能
- **问题**: 使用 JavaScript 浮点数（number 类型）进行金额计算，存在精度丢失风险
- **代码**:
  ```typescript
  // ❌ 当前代码
  const total = order.items.reduce((sum, item) => {
    return sum + item.price * item.quantity;
  }, 0);
  ```
- **建议**:
  ```typescript
  // ✅ 建议修改：使用 Decimal 库进行精确计算
  import Decimal from 'decimal.js';
  
  const total = order.items.reduce((sum, item) => {
    return sum.plus(new Decimal(item.price).times(item.quantity));
  }, new Decimal(0));
  ```
- **影响**: 在涉及大量订单时，累积的精度误差可能导致财务对账不通过

### High

#### H-001: 订单创建缺少事务保护
- **文件**: `src/modules/order/order.service.ts:89-145`
- **严重程度**: High
- **分类**: 功能
- **问题**: 创建订单涉及多个数据库操作（创建订单、扣减库存、创建订单项），但没有使用数据库事务保护，可能导致数据不一致
- **建议**: 使用数据库事务包裹所有操作，任何一步失败时回滚整个事务
- **代码**:
  ```typescript
  // ✅ 建议修改
  async createOrder(dto: CreateOrderDto): Promise<Order> {
    return this.dataSource.transaction(async (manager) => {
      const order = await manager.save(Order, orderData);
      await manager.decrement(Product, { id: dto.productId }, 'stock', dto.quantity);
      await manager.save(OrderItem, orderItems);
      return order;
    });
  }
  ```

#### H-002: 订单状态机缺少并发控制
- **文件**: `src/modules/order/order.service.ts:234-267`
- **严重程度**: High
- **分类**: 功能
- **问题**: 订单状态变更（如取消订单）没有使用乐观锁或悲观锁，在高并发场景下可能出现状态覆盖
- **建议**: 使用乐观锁（version 字段）或数据库行级锁防止并发更新
- **代码**:
  ```typescript
  // ✅ 建议修改：使用乐观锁
  async cancelOrder(orderId: string): Promise<void> {
    const result = await this.dataSource
      .createQueryBuilder()
      .update(Order)
      .set({ status: 'cancelled', version: () => 'version + 1' })
      .where('id = :id AND version = :version', { id: orderId, version: currentVersion })
      .execute();
    
    if (result.affected === 0) {
      throw new ConflictException('订单状态已被其他操作修改，请重试');
    }
  }
  ```

### Medium

#### M-001: 订单列表查询缺少索引支持
- **文件**: `src/modules/order/order.repository.ts:45`
- **严重程度**: Medium
- **分类**: 性能
- **问题**: 订单列表查询按 status + created_at 排序，但缺少对应的复合索引
- **建议**: 创建 `idx_orders_status_created_at` 复合索引
- **SQL**:
  ```sql
  CREATE INDEX idx_orders_status_created_at ON orders (status, created_at DESC);
  ```

#### M-002: 订单详情接口返回过多数据
- **文件**: `src/modules/order/order.controller.ts:67`
- **严重程度**: Medium
- **分类**: 性能
- **问题**: 订单详情接口返回了所有关联的完整用户信息（包括密码哈希），存在过度返回和安全隐患
- **建议**: 使用 DTO 选择需要的字段，排除敏感信息

#### M-003: 缺少幂等性处理
- **文件**: `src/modules/order/order.controller.ts:34`
- **严重程度**: Medium
- **分类**: 功能
- **问题**: 创建订单接口没有幂等键（idempotency key），网络重试可能导致重复创建订单
- **建议**: 在请求头中接收 Idempotency-Key，使用 Redis 缓存请求结果防止重复处理

#### M-004: 错误消息暴露内部实现细节
- **文件**: `src/modules/order/order.service.ts:312`
- **严重程度**: Medium
- **分类**: 安全
- **问题**: 数据库错误消息直接返回给客户端，暴露了表名和字段信息
- **建议**: 捕获数据库错误，返回通用的错误消息，将详细错误记录到日志

### Low

#### L-001: 函数过长
- **文件**: `src/modules/order/order.service.ts:89`
- **严重程度**: Low
- **分类**: 可维护性
- **问题**: createOrder 方法有 180 行，超过了 50 行的建议上限
- **建议**: 拆分为 createOrder、validateOrderItems、calculateTotal、reserveStock 等独立方法

#### L-002: 魔法数字
- **文件**: `src/modules/order/order.service.ts:198`
- **严重程度**: Low
- **分类**: 可维护性
- **问题**: 订单过期时间硬编码为 1800000（30 分钟）
- **建议**: 将魔法数字提取为命名常量：`const ORDER_EXPIRATION_MS = 30 * 60 * 1000;`

#### L-003: 缺少日志记录
- **文件**: `src/modules/order/order.service.ts:89`
- **严重程度**: Low
- **分类**: 可维护性
- **问题**: 订单创建、取消等关键操作缺少结构化日志
- **建议**: 在关键操作点添加日志记录，包含 orderId、userId、timestamp 等上下文信息

### Info

#### I-001: 建议使用 DTO 映射库
- **文件**: `src/modules/order/order.controller.ts`
- **建议**: 考虑使用 class-transformer 或 automapper 减少手动 DTO 映射代码

#### I-002: 建议添加 API 文档注解
- **文件**: `src/modules/order/order.controller.ts`
- **建议**: 使用 Swagger/OpenAPI 装饰器注解接口，自动生成 API 文档

#### I-003: 建议添加性能监控
- **文件**: `src/modules/order/order.service.ts`
- **建议**: 对订单创建、查询等关键操作添加性能埋点，记录执行时间

## 审查结论

- [ ] Approved（批准）
- [x] Changes Requested（需要修改）
- [ ] Rejected（拒绝）

**必须修复**: C-001（金额精度）、H-001（事务保护）、H-002（并发控制）
**强烈建议修复**: M-001 至 M-004
**可选修复**: L-001 至 L-003
**建议采纳**: I-001 至 I-003

请修复后重新提交审查。
```

## 不同严重程度问题的详细示例

### Critical 问题示例

| 场景 | 问题描述 | 代码示例 | 修复方案 |
|------|----------|----------|----------|
| SQL 注入 | 用户输入直接拼接到 SQL 语句 | `db.query("SELECT * FROM users WHERE id = " + userId)` | 使用参数化查询 `db.query("SELECT * FROM users WHERE id = $1", [userId])` |
| XSS 攻击 | 用户输入直接渲染到 HTML | `element.innerHTML = userInput` | 使用 `textContent` 或 DOMPurify 转义 |
| 密码明文存储 | 数据库中存储明文密码 | `user.password = password` | 使用 bcrypt 哈希 `user.password = await bcrypt.hash(password, 12)` |
| 硬编码密钥 | 密钥和 Token 硬编码在代码中 | `const API_KEY = "sk-xxxxx"` | 使用环境变量 `const API_KEY = process.env.API_KEY` |
| 金额精度丢失 | 浮点数计算金额 | `total += price * quantity` | 使用 Decimal 库或整数（分）计算 |
| 缺少事务保护 | 多步操作无事务 | 分别执行多个 save 操作 | 使用数据库事务包裹所有操作 |

### High 问题示例

| 场景 | 问题描述 | 代码示例 | 修复方案 |
|------|----------|----------|----------|
| 并发控制缺失 | 更新操作无乐观/悲观锁 | `await repo.save(entity)` | 添加 version 字段或行级锁 |
| 敏感信息泄露 | 错误消息返回数据库细节 | `res.status(500).send(err.message)` | 返回通用错误消息 |
| 缺少权限检查 | 接口未验证用户权限 | 直接处理请求 | 添加权限中间件 |
| 缺少速率限制 | 接口无调用频率限制 | 无限制 | 添加 rate limiter |
| 日志泄露敏感信息 | 日志中记录密码或 Token | `logger.info({ user })` | 过滤敏感字段 |
| N+1 查询 | 循环中执行数据库查询 | `for (item of items) { await db.query() }` | 使用 JOIN 或批量查询 |

### Medium 问题示例

| 场景 | 问题描述 | 修复方案 |
|------|----------|----------|
| 缺少索引 | 高频查询字段无索引 | 添加对应索引 |
| 函数过长 | 200 行单一函数 | 拆分为多个小函数 |
| 循环依赖 | 模块间循环引用 | 重构模块依赖关系 |
| 缺少输入验证 | 未验证请求参数类型 | 添加 DTO 验证 |
| 错误处理缺失 | 未处理异常情况 | 添加 try-catch |
| 代码重复 | 相同逻辑复制多份 | 提取公共函数 |

### Low 问题示例

| 场景 | 问题描述 | 修复方案 |
|------|----------|----------|
| 命名不规范 | 变量名不符合约定 | 重命名为符合规范的名称 |
| 注释缺失 | 公共方法缺少 JSDoc | 添加 JSDoc 注释 |
| 魔法数字 | 硬编码数值 | 提取为命名常量 |
| 未使用的导入 | import 了未使用的模块 | 删除未使用的导入 |
| 格式不一致 | 缩进或空格不统一 | 运行 Prettier 格式化 |
| 过时的注释 | 注释与代码不一致 | 更新或删除注释 |

## 审查报告模板（通用版）

以下模板适用于所有审查类型（代码、文档、架构、安全）：

```markdown
# {审查类型} Review Report

## 审查信息

| 项目 | 内容 |
|------|------|
| 审查编号 | {CR/RR/AR/SR}-{YYYYMMDD}-{NNN} |
| 审查类型 | 代码审查 / 文档审查 / 架构审查 / 安全审查 |
| 审查对象 | {对象名称或路径} |
| 审查者 | {审查者角色} |
| 审查日期 | {YYYY-MM-DD} |
| 审查状态 | Passed / Changes Requested / Rejected |

## 审查摘要

| 指标 | 数值 |
|------|------|
| 检查项总数 | {数量} |
| 通过数 | {数量} |
| 不通过数 | {数量} |
| 通过率 | {百分比} |
| Critical 问题 | {数量} |
| High 问题 | {数量} |
| Medium 问题 | {数量} |
| Low 问题 | {数量} |

## 审查结论

- [ ] Approved（批准）
- [ ] Changes Requested（需要修改）
- [ ] Rejected（拒绝）

## 审查建议

{对审查对象的整体评价和改进建议}

## 附录

### 审查耗时
- 审查时长: {X} 小时
- 审查项数: {数量}
```

## 审查统计和度量方法

### 审查度量指标

| 指标 | 计算方式 | 目标值 |
|------|----------|--------|
| 审查覆盖率 | 已审查的 PR 数 / 总 PR 数 | 100% |
| 审查响应时间 | 从提交审查到完成审查的时间 | < 24 小时 |
| 问题发现率 | 平均每个 PR 发现的问题数 | 3-5 个 |
| 返工率 | 需要 REWORK 的 PR 数 / 总 PR 数 | < 30% |
| 缺陷逃逸率 | 审查后发现的缺陷数 / 总缺陷数 | < 5% |
| 审查效率 | 审查的代码行数 / 审查时间 | > 200 行/小时 |

### 审查统计面板

```markdown
## 审查统计: 2026 年 7 月

### 总体统计

| 指标 | 数值 |
|------|------|
| 总审查次数 | 45 |
| 总审查行数 | 12,340 |
| 平均审查时间 | 1.5 小时 |
| 问题总数 | 187 |
| 平均问题数/PR | 4.2 |

### 问题分布

| 严重程度 | 数量 | 占比 |
|----------|------|------|
| Critical | 3 | 1.6% |
| High | 24 | 12.8% |
| Medium | 67 | 35.8% |
| Low | 72 | 38.5% |
| Info | 21 | 11.2% |

### 问题分类分布

| 分类 | 数量 | 占比 |
|------|------|------|
| 功能 | 42 | 22.5% |
| 安全 | 18 | 9.6% |
| 性能 | 23 | 12.3% |
| 规范 | 45 | 24.1% |
| 可维护性 | 38 | 20.3% |
| 文档 | 12 | 6.4% |
| 测试 | 9 | 4.8% |

### 趋势分析

| 月份 | 审查次数 | 平均问题数 | Critical 数 | 返工率 |
|------|----------|-----------|-------------|--------|
| 4 月 | 38 | 5.8 | 5 | 35% |
| 5 月 | 42 | 4.9 | 4 | 30% |
| 6 月 | 45 | 4.2 | 3 | 28% |
| 7 月 | 45 | 3.8 | 2 | 25% |

趋势分析：代码质量持续改善，平均问题数从 5.8 下降到 3.8，返工率从 35% 下降到 25%。
```

## 审查自动化

### 自动化检查

| 检查项 | 工具 | 触发时机 |
|--------|------|----------|
| 代码格式 | Prettier | pre-commit |
| 代码规范 | ESLint | pre-commit |
| 类型检查 | TypeScript/mypy | pre-commit |
| 单元测试 | Jest/pytest | pre-push |
| 测试覆盖率 | Jest/pytest | CI |
| 安全扫描 | Snyk/Trivy | CI |
| 依赖审计 | npm audit/pip audit | CI |

### 自动化规则

- 所有自动化检查必须通过后才能进行人工审查
- 自动化检查失败时，自动阻止审查流程
- 自动化检查结果作为审查报告的附件

## 审查最佳实践

### 对 Reviewer

1. **审查代码，而非作者** — 关注代码质量问题，不针对个人
2. **提供具体建议** — 不只说"有问题"，要给出具体修改方案
3. **解释原因** — 说明为什么需要修改，帮助作者理解
4. **区分优先级** — 明确标注问题的严重程度
5. **及时审查** — 避免审查积压，影响开发进度
6. **关注高优先级问题** — 优先处理 Critical 和 High 问题
7. **正面反馈** — 对好的代码给出肯定

### 对 Author

1. **小步提交** — 每次提交的变更量适中，便于审查
2. **自检先行** — 提交前先自我审查
3. **描述清晰** — PR/MR 描述清楚变更内容和原因
4. **积极响应** — 及时处理审查意见
5. **主动沟通** — 对不确定的问题主动讨论
6. **不抵触** — 审查意见是改进机会，不是批评

## 反模式

| 反模式 | 说明 | 正确做法 |
|--------|------|----------|
| 橡皮图章 | 审查走过场，不仔细检查 | 认真审查每个检查点 |
| 完美主义 | 要求代码完美无缺 | 区分严重程度，关注关键问题 |
| 主观偏好 | 基于个人偏好提意见 | 基于规范和标准提意见 |
| 审查延迟 | 审查积压，不及时处理 | 24 小时内完成审查 |
| 巨型 PR | 一次提交大量变更 | 拆分为多个小 PR |
| 审查缺失 | 跳过审查直接合并 | 所有代码必须经过审查 |
| 无建设性批评 | 只说"不好"不给建议 | 提供具体的修改建议 |

## 检查清单

### Reviewer 检查清单

- [ ] 已理解变更内容和目的
- [ ] 已检查所有审查点
- [ ] 已标注问题严重程度
- [ ] 已提供具体修改建议
- [ ] 已编写审查报告
- [ ] 已提交审查结论

### Author 检查清单

- [ ] 已完成自检
- [ ] 已通过自动化检查
- [ ] PR/MR 描述清晰
- [ ] 变更量适中
- [ ] 已处理所有审查意见
- [ ] 已请求重新审查

---

**本规范是所有 AI Agent 执行审查的基础标准。每个 Code Reviewer 和 Tech Lead 在审查时必须严格遵守本规范。**