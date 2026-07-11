# Deployment Template - 部署文档模板

## 使用说明

本模板用于 DevOps Engineer 编写部署计划。请完整填写所有章节。

## 文档元信息

<!--
Document: Deployment Plan
Version: 1.0.0
Author: DevOps Engineer
Created: {YYYY-MM-DD}
Updated: {YYYY-MM-DD}
Status: Draft
-->

---

# Deployment Plan: {项目名称}

## 1. 概述

### 1.1 部署目标

| 属性 | 值 |
|------|------|
| 部署版本 | v{版本号} |
| 部署环境 | {staging/production} |
| 部署日期 | {YYYY-MM-DD} |
| 预计停机时间 | 0（零停机部署） |
| 回滚时间 | < 5 分钟 |

### 1.2 变更内容

| 变更 | 类型 | 影响 |
|------|------|------|
| {变更 1} | 功能/Bug修复/优化 | {影响} |
| {变更 2} | 功能/Bug修复/优化 | {影响} |

## 2. 环境信息

### 2.1 基础设施

| 资源 | 规格 | 数量 | 备注 |
|------|------|------|------|
| 应用服务器 | 2 CPU / 4 GB | 3 | 自动扩缩容 |
| 数据库 | 4 CPU / 16 GB / 100 GB SSD | 1 | 主从复制 |
| 缓存 | 2 GB | 1 | 哨兵模式 |
| 消息队列 | 2 CPU / 4 GB | 1 | 集群模式 |

### 2.2 环境变量

```bash
# 需要新增的环境变量
NEW_FEATURE_ENABLED=true

# 需要修改的环境变量
LOG_LEVEL=info  # 从 debug 改为 info

# 需要删除的环境变量
# OLD_CONFIG_KEY（已废弃）
```

## 3. 部署步骤

### 3.1 部署前检查

- [ ] 所有测试通过（CI）
- [ ] 安全扫描通过
- [ ] 代码审查通过
- [ ] 数据库迁移已准备
- [ ] 回滚方案已准备
- [ ] 监控告警已配置
- [ ] 通知已发送

### 3.2 数据库迁移

```sql
-- 迁移 1: 添加新列
ALTER TABLE users ADD COLUMN IF NOT EXISTS phone_verified BOOLEAN DEFAULT false;

-- 迁移 2: 创建新索引
CREATE INDEX CONCURRENTLY IF NOT EXISTS idx_users_phone ON users (phone);
```

### 3.3 部署执行

```
1. 部署到预发布环境
   ├── kubectl apply -f deployment-staging.yaml
   ├── kubectl rollout status deployment/app -n staging
   └── 运行冒烟测试

2. 人工审批

3. 部署到生产环境（滚动更新）
   ├── kubectl set image deployment/app app=app:v{version} -n production
   ├── kubectl rollout status deployment/app -n production
   └── 监控部署状态

4. 部署后验证
   ├── 健康检查
   ├── 冒烟测试
   ├── 监控指标检查
   └── 日志检查
```

### 3.4 部署后验证

| 验证项 | 方法 | 预期结果 |
|--------|------|----------|
| 健康检查 | GET /health | 200 OK |
| 功能验证 | 冒烟测试 | 全部通过 |
| 性能验证 | 监控面板 | 指标正常 |
| 日志验证 | 日志系统 | 无异常错误 |

## 4. 回滚方案

### 4.1 回滚触发条件

- 错误率 > 1%
- 响应时间 P95 > 1s
- 冒烟测试失败
- 数据库迁移失败
- 关键功能异常

### 4.2 回滚步骤

```
1. 执行回滚
   kubectl rollout undo deployment/app -n production

2. 回滚数据库迁移
   {回滚 SQL 脚本}

3. 验证回滚
   ├── 健康检查
   ├── 功能验证
   └── 监控指标检查

4. 通知团队
```

## 5. 监控与告警

### 5.1 部署期间监控

| 指标 | 监控面板 | 告警阈值 |
|------|----------|----------|
| 错误率 | Dashboard | > 1% |
| 响应时间 | Dashboard | P95 > 1s |
| CPU 使用率 | Dashboard | > 80% |
| 内存使用率 | Dashboard | > 80% |
| 部署状态 | kubectl | 非 Running |

### 5.2 告警规则

| 告警名称 | 条件 | 持续时间 | 通知 |
|----------|------|----------|------|
| 部署失败 | deployment status != success | 1m | 电话 + 消息 |
| 错误率过高 | error_rate > 5% | 5m | 电话 + 消息 |
| 响应时间过长 | p95 > 2s | 5m | 消息 |

## 6. 通知计划

### 6.1 通知对象

| 对象 | 通知时机 | 通知方式 |
|------|----------|----------|
| 开发团队 | 部署前 30 分钟 | 消息 |
| QA 团队 | 部署完成后 | 消息 |
| 项目经理 | 部署完成后 | 消息 |
| 全体用户 | 如有停机 | 邮件/公告 |

## 7. 风险评估

| 风险 | 可能性 | 影响 | 缓解措施 |
|------|--------|------|----------|
| 数据库迁移失败 | 低 | 高 | 已测试迁移脚本，有回滚方案 |
| 性能下降 | 中 | 中 | 灰度发布，监控指标 |
| 第三方服务故障 | 低 | 中 | 降级策略 |
| 配置错误 | 中 | 高 | 配置审查，环境变量管理 |

## 8. 附录

### 8.1 部署检查清单

- [ ] 部署前检查完成
- [ ] 数据库迁移已执行
- [ ] 部署已执行
- [ ] 部署后验证通过
- [ ] 监控指标正常
- [ ] 通知已发送
- [ ] 回滚方案就绪

### 8.2 变更历史

| 版本 | 日期 | 变更说明 | 作者 |
|------|------|----------|------|
| 1.0.0 | {YYYY-MM-DD} | 初始版本 | DevOps Engineer |

---

## 部署策略模板

### 滚动更新（Rolling Update）

```yaml
# Kubernetes 滚动更新配置
apiVersion: apps/v1
kind: Deployment
metadata:
  name: app-deployment
spec:
  replicas: 3
  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxSurge: 1        # 最多超过期望副本数 1 个
      maxUnavailable: 0  # 确保始终有 3 个 Pod 在运行
  template:
    spec:
      containers:
      - name: app
        image: app:v2.0.0
        readinessProbe:
          httpGet:
            path: /health
            port: 3000
          initialDelaySeconds: 10
          periodSeconds: 5
```

**滚动更新步骤**:
```
1. 创建 1 个新版 Pod（maxSurge=1）
   ↓
2. 等待新版 Pod 通过健康检查（readinessProbe）
   ↓
3. 删除 1 个旧版 Pod（maxUnavailable=0，确保始终有 3 个运行）
   ↓
4. 重复步骤 1-3，直到所有 Pod 更新完成
   ↓
5. 验证所有 Pod 运行新版
```

**适用场景**:
- 零停机部署
- 逐步替换旧版本，风险可控
- 适用于无状态服务

**回滚方式**:
```bash
kubectl rollout undo deployment/app-deployment -n production
```

### 蓝绿部署（Blue-Green Deployment）

```yaml
# 蓝绿部署配置
# 当前生产环境（Blue）
apiVersion: v1
kind: Service
metadata:
  name: app-service
spec:
  selector:
    app: app-blue
  ports:
  - port: 80
    targetPort: 3000
```

**蓝绿部署步骤**:
```
当前状态: Blue（v1.0.0）在生产环境运行
    ↓
1. 部署 Green 环境（v2.0.0）
   ├── 创建与 Blue 相同的副本数
   ├── 执行健康检查
   └── 运行冒烟测试
    ↓
2. 切换流量
   ├── 更新 Service selector 从 app: app-blue 改为 app: app-green
   └── 流量瞬间切换到 Green
    ↓
3. 验证 Green 环境
   ├── 监控错误率
   ├── 监控响应时间
   └── 监控业务指标
    ↓
4. 保留 Blue 环境作为回滚备用
   ├── 观察 30 分钟
   └── 确认无问题后，可下线 Blue 环境
```

**适用场景**:
- 需要快速回滚的场景
- 不接受渐进式切换的场景
- 对瞬时流量切换不敏感的服务

**回滚方式**:
```bash
# 快速回滚：切换 Service selector 回 Blue
kubectl patch service app-service -p '{"spec":{"selector":{"app":"app-blue"}}}'
```

### 金丝雀部署（Canary Deployment）

```yaml
# 金丝雀部署配置
# 主部署（95% 流量）
apiVersion: apps/v1
kind: Deployment
metadata:
  name: app-stable
spec:
  replicas: 19  # 95% 流量
  template:
    metadata:
      labels:
        app: app
        version: stable
    spec:
      containers:
      - name: app
        image: app:v1.0.0

---
# 金丝雀部署（5% 流量）
apiVersion: apps/v1
kind: Deployment
metadata:
  name: app-canary
spec:
  replicas: 1   # 5% 流量
  template:
    metadata:
      labels:
        app: app
        version: canary
    spec:
      containers:
      - name: app
        image: app:v2.0.0
```

**金丝雀部署步骤**:
```
当前状态: v1.0.0 处理 100% 流量
    ↓
1. 部署金丝雀版本（v2.0.0），分配 5% 流量
   ├── 创建 1 个金丝雀 Pod
   ├── 配置 Istio/Linkerd 流量路由
   └── 5% 流量 → v2.0.0，95% 流量 → v1.0.0
    ↓
2. 监控金丝雀版本（15 分钟）
   ├── 错误率对比（v2.0.0 vs v1.0.0）
   ├── 响应时间对比
   └── 业务指标对比（订单量、支付成功率）
    ↓
3. 逐步增加金丝雀流量
   ├── 5% → 25% → 50% → 75% → 100%
   └── 每个阶段观察 15 分钟
    ↓
4. 全量切换
   ├── 100% 流量 → v2.0.0
   └── 下线 v1.0.0
```

**适用场景**:
- 需要验证新版本在生产环境的表现
- 需要逐步验证新功能
- 对流量变化敏感的服务

**回滚方式**:
```bash
# 将金丝雀流量降为 0，所有流量回到稳定版
kubectl scale deployment app-canary --replicas=0
```

## 回滚模板

### 完整回滚方案

```markdown
# Rollback Plan: {项目名称} v{版本号}

## 回滚触发条件

| 条件 | 阈值 | 检查方式 |
|------|------|----------|
| 错误率 | > 5% | 监控面板 - 错误率指标 |
| 响应时间 P95 | > 2s | 监控面板 - 响应时间指标 |
| 核心功能不可用 | 无法登录/下单 | 冒烟测试失败 |
| 数据库迁移失败 | 迁移脚本报错 | 部署日志检查 |
| 用户投诉 | 10 分钟内 > 5 条 | 客服系统告警 |

## 回滚决策流程

```
检测到回滚条件触发
    ↓
值班工程师确认（2 分钟内）
    ↓
通知 Tech Lead 和 DevOps Engineer
    ↓
Tech Lead 决策：回滚 or 前向修复
    ├── 回滚（预计 5 分钟）
    └── 前向修复（预计 X 分钟）
    ↓
执行回滚
    ↓
验证回滚结果
    ↓
通知所有相关方
    ↓
记录事后分析（Post-mortem）
```

## 回滚步骤

### 步骤 1：应用回滚

```bash
# Kubernetes 回滚到上一个版本
kubectl rollout undo deployment/app -n production

# 回滚到指定版本
kubectl rollout undo deployment/app --to-revision=3 -n production

# 查看回滚状态
kubectl rollout status deployment/app -n production
```

### 步骤 2：数据库回滚（如需要）

```sql
-- 执行数据库 DOWN 迁移
BEGIN;
ALTER TABLE users DROP COLUMN IF EXISTS phone_verified;
DROP INDEX IF EXISTS idx_users_phone;
COMMIT;
```

### 步骤 3：配置回滚（如需要）

```bash
# 回滚环境变量
kubectl set env deployment/app LOG_LEVEL=debug -n production
```

## 回滚后验证

| 验证项 | 方法 | 预期结果 |
|--------|------|----------|
| 应用健康检查 | GET /health | 200 OK |
| 核心功能验证 | 冒烟测试 | 全部通过 |
| 错误率 | 监控面板 | < 1% |
| 响应时间 | 监控面板 | P95 < 1s |
| 数据库连接 | 数据库监控 | 连接正常 |

## 回滚后通知

| 通知对象 | 通知内容 | 通知方式 |
|----------|----------|----------|
| 开发团队 | 回滚原因、回滚版本、影响范围 | 即时消息 |
| QA 团队 | 回滚后需要重新验证的功能 | 即时消息 |
| 项目经理 | 回滚原因、影响时间、后续计划 | 即时消息 |
| 用户（如有影响） | 功能恢复通知 | 公告/邮件 |

## 事后分析模板

```markdown
# Post-mortem: v{版本号} 部署回滚

## 事件时间线
| 时间 | 事件 |
|------|------|
| 14:00 | 开始部署 v2.0.0 |
| 14:10 | 部署完成，冒烟测试通过 |
| 14:20 | 监控告警：错误率上升至 8% |
| 14:22 | 值班工程师确认告警 |
| 14:25 | Tech Lead 决策：立即回滚 |
| 14:30 | 回滚完成，错误率恢复正常 |

## 根因分析
- 直接原因: {描述}
- 根本原因: {描述}

## 影响评估
- 影响时间: 10 分钟
- 影响用户: 约 200 个活跃用户
- 影响功能: 订单创建失败

## 改进措施
| 措施 | 负责人 | 截止日期 |
|------|--------|----------|
| {措施 1} | {负责人} | {日期} |
| {措施 2} | {负责人} | {日期} |
```

## 部署验证清单

### 部署前验证

- [ ] 所有测试通过（CI 流水线绿色）
- [ ] 安全扫描通过（无 Critical/High 漏洞）
- [ ] 代码审查通过（所有 PR 已批准）
- [ ] 数据库迁移脚本已测试（在 staging 环境验证）
- [ ] 回滚方案已准备（已确认回滚命令和步骤）
- [ ] 监控告警已配置（部署期间的专项监控）
- [ ] 通知已发送（部署前 30 分钟通知相关方）
- [ ] 部署窗口已确认（是否有部署冻结期）
- [ ] 依赖服务已确认可用（第三方 API、数据库、缓存）
- [ ] 环境变量已审查（新增、修改、删除的变量）

### 部署中验证

- [ ] 部署状态正常（kubectl rollout status）
- [ ] 新 Pod 健康检查通过（readiness probe）
- [ ] 旧 Pod 正常终止（graceful shutdown）
- [ ] 数据库迁移执行成功
- [ ] 无异常日志（日志系统检查）

### 部署后验证

- [ ] 健康检查端点返回 200
- [ ] 核心功能冒烟测试通过
- [ ] 错误率 < 1%
- [ ] 响应时间 P95 < 目标值
- [ ] 数据库连接池正常
- [ ] 缓存命中率正常
- [ ] 消息队列消费正常
- [ ] 第三方服务连接正常
- [ ] 通知已发送（部署完成通知）

**本模板必须完整填写。不要使用占位符、省略号或 TODO 标记。**