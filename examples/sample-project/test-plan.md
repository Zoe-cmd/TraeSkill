# Test Plan: TaskFlow 任务管理模块

<!--
Document: Test Plan
Version: 1.0.0
Author: QA Engineer
Created: 2026-07-11
Status: Approved
-->

## 1. 测试策略概述

### 1.1 测试金字塔

| 类型 | 范围 | 工具 | 目标覆盖率 | 负责人 |
|------|------|------|-----------|--------|
| 单元测试 | 所有 Service / Util / Hook | Vitest (前端) + Jest (后端) | >= 80% | 开发者 |
| 集成测试 | 核心 API 模块 | Jest + Supertest | 关键路径 100% | Backend Engineer |
| E2E 测试 | 核心用户流程 | Playwright | 核心流程 100% | QA Engineer |
| 性能测试 | 关键 API 端点 | k6 | P95 < 200ms | DevOps Engineer |
| 安全测试 | 认证/授权/注入 | OWASP ZAP | 无高危漏洞 | Security Engineer |

### 1.2 测试范围

| 模块 | 单元测试 | 集成测试 | E2E 测试 | 性能测试 |
|------|----------|----------|----------|----------|
| 用户认证 | 是 | 是 | 是 | 是 |
| 任务管理 | 是 | 是 | 是 | 是 |
| 项目管理 | 是 | 是 | 否 | 否 |
| 评论/附件 | 是 | 是 | 否 | 否 |
| 通知 | 是 | 是 | 否 | 否 |
| AI 排期 | 是 | 是 | 是 | 是 |
| 数据看板 | 是 | 是 | 否 | 是 |

---

## 2. 单元测试用例

### 2.1 任务创建服务 (TasksService)

| 编号 | 测试用例 | 输入 | 预期输出 | 优先级 |
|------|----------|------|----------|--------|
| UT-001 | 正常创建任务 | 有效 title + projectId + userId | 返回 Task 对象，status='todo' | P0 |
| UT-002 | 标题为空 | title="" | 抛出 ValidationError | P0 |
| UT-003 | 标题超过 500 字符 | title="A".repeat(501) | 抛出 ValidationError | P1 |
| UT-004 | 优先级非法 | priority="critical" | 抛出 ValidationError | P1 |
| UT-005 | 指派人非项目成员 | assigneeId=非成员 | 抛出 ForbiddenError | P0 |
| UT-006 | 创建子任务 | parentTaskId=有效 | 返回 Task，parentTaskId 正确关联 | P1 |
| UT-007 | 父任务不存在 | parentTaskId=无效 | 抛出 NotFoundError | P1 |
| UT-008 | 创建任务后发布事件 | 有效输入 | 验证 TaskCreatedEvent 被发布 | P1 |

### 2.2 任务状态流转 (TaskStatusService)

| 编号 | 测试用例 | 输入 | 预期输出 | 优先级 |
|------|----------|------|----------|--------|
| UT-009 | todo → in_progress | status="in_progress" | 状态更新成功，started_at 自动设置 | P0 |
| UT-010 | in_progress → review | status="review" | 状态更新成功 | P0 |
| UT-011 | review → done | status="done" | 状态更新成功，completed_at 自动设置 | P0 |
| UT-012 | todo → done (跳过 review) | status="done" | 状态更新成功 | P0 |
| UT-013 | done → in_progress (非法) | status="in_progress" | 抛出 InvalidStatusTransitionError | P0 |
| UT-014 | todo → review (非法) | status="review" | 抛出 InvalidStatusTransitionError | P1 |
| UT-015 | 非指派人更新状态 | 非指派人请求 | 状态更新成功（允许非指派人） | P1 |

### 2.3 AI 排期服务 (AISchedulingService)

| 编号 | 测试用例 | 输入 | 预期输出 | 优先级 |
|------|----------|------|----------|--------|
| UT-016 | 正常 AI 排期 | 有效 projectId + 5 个未完成任务 | 返回排期方案，所有任务已分配 | P0 |
| UT-017 | 空任务列表 | projectId (无未完成任务) | 返回空方案，message 提示无任务 | P1 |
| UT-018 | AI 返回格式错误 | Mock OpenAI 返回非法 JSON | 降级为规则排期，generated_by='rule_based' | P0 |
| UT-019 | AI 超时 | Mock OpenAI 超时 10s | 降级为规则排期 | P0 |
| UT-020 | 非项目管理员请求 | 普通 member 角色 | 抛出 ForbiddenError | P0 |

---

## 3. 集成测试用例

### 3.1 任务 CRUD 流程

| 编号 | 测试用例 | 测试步骤 | 预期结果 | 优先级 |
|------|----------|----------|----------|--------|
| IT-001 | 创建任务完整流程 | 1. POST /auth/login 获取 token；2. POST /projects/{id}/tasks 创建任务；3. GET /tasks/{id} 验证 | 任务创建成功，返回 201，详情可查询 | P0 |
| IT-002 | 获取任务列表（分页） | GET /projects/{id}/tasks?page=1&limit=10 | 返回正确分页数据，total 和 totalPages 正确 | P0 |
| IT-003 | 获取任务列表（筛选） | GET ...?status=todo&priority=high&assigneeId=xxx | 仅返回符合条件的任务 | P0 |
| IT-004 | 更新任务状态 | PATCH /tasks/{id} {status:"in_progress"} | 状态更新，started_at 被设置 | P0 |
| IT-005 | 删除任务 | DELETE /tasks/{id} | 返回 204，任务软删除 | P0 |
| IT-006 | 非项目成员访问 | 使用非项目成员 token 请求任务 | 返回 403 | P0 |
| IT-007 | 添加评论 | POST /tasks/{id}/comments {content:"test"} | 返回 201，评论包含 mentions 解析 | P0 |
| IT-008 | 批量操作 | POST /tasks/batch {operation:"update_status", taskIds:[...], payload:{status:"done"}} | 批量更新成功，返回成功/失败计数 | P1 |

### 3.2 AI 排期集成

| 编号 | 测试用例 | 测试步骤 | 预期结果 | 优先级 |
|------|----------|----------|----------|--------|
| IT-009 | AI 排期生成 + 应用 | 1. POST /scheduling/suggest 生成方案；2. 手动调整；3. PATCH /scheduling/{id}/apply 应用 | 方案应用后所有任务更新，通知发送 | P0 |
| IT-010 | 排期方案过期 | 生成方案后 7 天未应用 | 方案状态变为 expired | P1 |

---

## 4. E2E 测试用例

| 编号 | 测试用例 | 测试步骤 | 预期结果 | 优先级 |
|------|----------|----------|----------|--------|
| E2E-001 | 用户注册登录流程 | 1. 访问注册页；2. 填写表单提交；3. 验证邮箱（模拟）；4. 登录；5. 进入仪表盘 | 成功登录，仪表盘显示用户信息 | P0 |
| E2E-002 | 创建并分配任务 | 1. 登录；2. 进入项目；3. 点击"创建任务"；4. 填写标题/优先级/指派人；5. 提交；6. 在任务列表验证 | 任务出现在列表中，正确显示优先级和指派人 | P0 |
| E2E-003 | 任务状态拖拽流转 | 1. 进入看板视图；2. 拖拽任务从 TODO 到 IN_PROGRESS；3. 拖拽到 REVIEW；4. 拖拽到 DONE | 状态正确更新，视觉反馈即时 | P0 |
| E2E-004 | 智能排期建议 | 1. 进入项目；2. 点击"智能排期"；3. 等待方案生成；4. 查看甘特图；5. 拖拽调整日期；6. 点击"应用方案" | 甘特图正确渲染，方案应用后任务更新 | P0 |
| E2E-005 | 评论与通知 | 1. 创建评论 @提及另一用户；2. 切换到被提及用户；3. 查看通知 | 通知正确显示，点击跳转到对应任务 | P1 |

---

## 5. 性能测试

### 5.1 测试场景

| 场景 | 并发用户 | 持续时间 | 目标指标 |
|------|----------|----------|----------|
| 任务列表查询 | 100 | 5 分钟 | P95 < 200ms, 错误率 < 0.1% |
| 任务创建 | 50 | 5 分钟 | P95 < 500ms, 错误率 < 0.1% |
| 任务状态更新 | 50 | 5 分钟 | P95 < 300ms, 错误率 < 0.1% |
| AI 排期生成 | 10 | 5 分钟 | P95 < 5s, 错误率 < 1% |
| 项目统计查询 | 100 | 5 分钟 | P95 < 300ms, 错误率 < 0.1% |

### 5.2 k6 性能测试脚本示例

```javascript
// k6/task-list-load-test.js
import http from 'k6/http';
import { check, sleep } from 'k6';

export const options = {
    stages: [
        { duration: '1m', target: 50 },   // 1 分钟内升至 50 并发
        { duration: '3m', target: 100 },  // 3 分钟内升至 100 并发
        { duration: '1m', target: 0 },    // 1 分钟内降至 0
    ],
    thresholds: {
        http_req_duration: ['p(95)<200'], // P95 < 200ms
        http_req_failed: ['rate<0.001'],   // 错误率 < 0.1%
    },
};

const BASE_URL = 'https://staging-api.taskflow.com/v1';
const TOKEN = '{{TEST_TOKEN}}';

export default function () {
    const params = {
        headers: {
            'Authorization': `Bearer ${TOKEN}`,
            'Content-Type': 'application/json',
        },
    };

    // 获取任务列表
    const res = http.get(
        `${BASE_URL}/projects/10000000-0000-0000-0000-000000000001/tasks?page=1&limit=20`,
        params
    );

    check(res, {
        'status is 200': (r) => r.status === 200,
        'response time < 200ms': (r) => r.timings.duration < 200,
    });

    sleep(1);
}
```

---

## 6. 测试环境

| 环境 | 地址 | 数据库 | 用途 |
|------|------|--------|------|
| 本地开发 | localhost:3000/4000 | Docker PostgreSQL | 开发自测 |
| CI 测试 | GitHub Actions Runner | 临时 PostgreSQL 容器 | 自动化测试 |
| 预发布 | staging.taskflow.com | 独立 PostgreSQL 实例 | 手动验证 + 性能测试 |
| 生产 | taskflow.com | 生产数据库（只读副本） | 冒烟测试 |

### 6.1 测试数据管理

- 使用种子数据脚本初始化测试数据库
- 集成测试使用事务回滚保证隔离性
- E2E 测试使用独立的测试项目，避免污染
- 敏感数据使用假数据（Faker.js）

---

## 7. 缺陷管理流程

### 7.1 缺陷严重级别

| 级别 | 定义 | 响应时间 | 修复时间 |
|------|------|----------|----------|
| Blocker | 系统不可用，核心功能完全无法使用 | 1 小时 | 4 小时 |
| Critical | 核心功能部分不可用，影响大量用户 | 2 小时 | 24 小时 |
| Major | 功能可用但有明显缺陷，存在绕过方案 | 1 天 | 3 天 |
| Minor | 非核心功能缺陷，不影响主要流程 | 3 天 | 下一迭代 |
| Trivial | UI 文案、样式等小问题 | 无需立即响应 | 按需修复 |

### 7.2 缺陷生命周期

```
发现 → 提交(Issue) → 确认(Confirmed) → 分配(Assigned) → 修复中(Fixing)
    → 修复完成(Fixed) → 验证(Verified) → 关闭(Closed)
    → 无法复现 → 关闭(Closed/Not Reproducible)
    → 重复 → 关闭(Closed/Duplicate)
```

---

**版本: 1.0.0 | 作者: QA Engineer | 日期: 2026-07-11**