# Deployment Standard - 统一部署规范

## 概述

本文档定义了所有 AI Agent 在进行部署和运维时必须遵守的统一部署规范。涵盖 CI/CD、容器化、环境管理、监控告警、日志管理等。

## 适用范围

- 本规范适用于所有部署相关的操作
- 所有 DevOps Engineer 和涉及部署的 Agent 必须遵守本规范

## 语言规范

本规范文档及所有相关文档、代码注释、提交信息均使用简体中文编写。技术术语（如 CI/CD、Docker、Kubernetes、GitHub Actions、YAML、JSON、HTTPS、SSH、TLS、DNS 等）保留英文原文。

## 部署原则

| 原则 | 说明 |
|------|------|
| 自动化 | 部署流程全部自动化，减少人工操作 |
| 可重复 | 部署结果可重复，环境一致 |
| 可回滚 | 任何部署都可以快速回滚 |
| 不可变 | 基础设施不可变，变更通过替换实现 |
| 环境隔离 | 开发、测试、预发布、生产环境严格隔离 |
| 安全第一 | 部署过程中安全措施不可妥协 |
| 监控先行 | 部署前确保监控告警已配置 |

## 环境管理

### 环境定义

| 环境 | 用途 | 数据 | 访问 |
|------|------|------|------|
| dev | 本地开发 | 模拟数据 | 开发者 |
| test | 自动化测试 | 测试数据 | CI/CD |
| staging | 预发布验证 | 脱敏生产数据 | 内部团队 |
| production | 生产环境 | 生产数据 | 终端用户 |

### 环境差异

| 配置项 | dev | test | staging | production |
|--------|-----|------|---------|------------|
| 日志级别 | DEBUG | DEBUG | INFO | WARN |
| 数据库 | 本地 | 测试库 | 预发布库 | 生产库 |
| 缓存 | 可选 | 可选 | 启用 | 启用 |
| 监控 | 关闭 | 关闭 | 开启 | 开启 |
| 告警 | 关闭 | 关闭 | 开启 | 开启 |
| 限流 | 关闭 | 关闭 | 模拟 | 开启 |
| 副本数 | 1 | 1 | 2 | 3+ |

### 环境变量

```bash
# 环境标识
NODE_ENV=production
APP_ENV=production

# 服务配置
PORT=3000
HOST=0.0.0.0

# 数据库
DATABASE_URL=postgresql://user:password@host:5432/database
DATABASE_POOL_MIN=2
DATABASE_POOL_MAX=20

# Redis
REDIS_URL=redis://host:6379

# JWT
JWT_SECRET=your-secret-key
JWT_EXPIRY=24h

# 日志
LOG_LEVEL=info
LOG_FORMAT=json

# 监控
SENTRY_DSN=https://xxx@sentry.io/xxx

# 第三方服务
EMAIL_SERVICE_URL=https://api.email.com
PAYMENT_SERVICE_URL=https://api.payment.com
```

### 敏感信息管理

- 所有密钥、密码、Token 使用 Secret Manager 管理
- 禁止在代码中硬编码敏感信息
- 禁止在日志中打印敏感信息
- 禁止将 .env 文件提交到 Git
- 使用不同环境的不同密钥

## CI/CD

### CI/CD 流水线

```
Push/PR
  │
  ▼
┌──────────────────────────────────────────────────────┐
│ Stage 1: Build                                        │
│ ├── Install dependencies                              │
│ ├── Lint check                                        │
│ ├── Type check                                        │
│ └── Build project                                     │
└──────────────────────────────────────────────────────┘
  │
  ▼
┌──────────────────────────────────────────────────────┐
│ Stage 2: Test                                         │
│ ├── Unit tests                                        │
│ ├── Integration tests                                 │
│ └── Coverage report                                   │
└──────────────────────────────────────────────────────┘
  │
  ▼
┌──────────────────────────────────────────────────────┐
│ Stage 3: Security Scan                                │
│ ├── Dependency audit                                  │
│ ├── SAST (Static Application Security Testing)        │
│ └── Container scan                                    │
└──────────────────────────────────────────────────────┘
  │
  ▼
┌──────────────────────────────────────────────────────┐
│ Stage 4: Package                                      │
│ ├── Build Docker image                                │
│ ├── Tag image                                         │
│ └── Push to registry                                  │
└──────────────────────────────────────────────────────┘
  │
  ▼
┌──────────────────────────────────────────────────────┐
│ Stage 5: Deploy to Staging                            │
│ ├── Deploy to staging                                 │
│ ├── Run smoke tests                                   │
│ └── Health check                                      │
└──────────────────────────────────────────────────────┘
  │
  ▼
┌──────────────────────────────────────────────────────┐
│ Stage 6: Deploy to Production                         │
│ ├── Manual approval (Human In The Loop)               │
│ ├── Deploy to production (rolling update)             │
│ ├── Health check                                      │
│ └── Post-deploy verification                          │
└──────────────────────────────────────────────────────┘
```

### GitHub Actions 示例

```yaml
name: CI/CD Pipeline

on:
  push:
    branches: [develop]
  pull_request:
    branches: [develop, main]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: '20'
          cache: 'npm'
      - run: npm ci
      - run: npm run lint
      - run: npm run type-check
      - run: npm run build

  test:
    needs: build
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: '20'
          cache: 'npm'
      - run: npm ci
      - run: npm test -- --coverage
      - uses: actions/upload-artifact@v4
        with:
          name: coverage
          path: coverage/

  security:
    needs: build
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - run: npm audit --audit-level=high
      - uses: snyk/actions/node@master
        env:
          SNYK_TOKEN: ${{ secrets.SNYK_TOKEN }}

  deploy-staging:
    needs: [test, security]
    if: github.ref == 'refs/heads/develop'
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Build and push Docker image
        run: |
          docker build -t app:${GITHUB_SHA} .
          docker push app:${GITHUB_SHA}
      - name: Deploy to staging
        run: |
          kubectl set image deployment/app app=app:${GITHUB_SHA} -n staging
          kubectl rollout status deployment/app -n staging

  deploy-production:
    needs: deploy-staging
    if: github.ref == 'refs/heads/main'
    runs-on: ubuntu-latest
    environment: production
    steps:
      - name: Deploy to production
        run: |
          kubectl set image deployment/app app=app:${GITHUB_SHA} -n production
          kubectl rollout status deployment/app -n production
```

## 容器化

### Dockerfile 规范

```dockerfile
# 多阶段构建
FROM node:20-alpine AS builder
WORKDIR /app
COPY package*.json ./
RUN npm ci --only=production
COPY . .
RUN npm run build

FROM node:20-alpine AS runner
WORKDIR /app
RUN addgroup --system --gid 1001 nodejs
RUN adduser --system --uid 1001 appuser
COPY --from=builder /app/dist ./dist
COPY --from=builder /app/node_modules ./node_modules
COPY --from=builder /app/package.json ./
USER appuser
EXPOSE 3000
HEALTHCHECK --interval=30s --timeout=3s --start-period=5s --retries=3 \
  CMD wget --no-verbose --tries=1 --spider http://localhost:3000/health || exit 1
CMD ["node", "dist/main.js"]
```

### Docker Compose 示例

```yaml
version: '3.8'
services:
  app:
    build: .
    ports:
      - "3000:3000"
    environment:
      - NODE_ENV=production
      - DATABASE_URL=postgresql://user:password@db:5432/app
      - REDIS_URL=redis://redis:6379
    depends_on:
      db:
        condition: service_healthy
      redis:
        condition: service_healthy
    healthcheck:
      test: ["CMD", "wget", "--spider", "http://localhost:3000/health"]
      interval: 30s
      timeout: 3s
      retries: 3

  db:
    image: postgres:16-alpine
    volumes:
      - postgres_data:/var/lib/postgresql/data
    environment:
      - POSTGRES_USER=user
      - POSTGRES_PASSWORD=password
      - POSTGRES_DB=app
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U user -d app"]
      interval: 10s
      timeout: 5s
      retries: 5

  redis:
    image: redis:7-alpine
    volumes:
      - redis_data:/data
    healthcheck:
      test: ["CMD", "redis-cli", "ping"]
      interval: 10s
      timeout: 5s
      retries: 5

volumes:
  postgres_data:
  redis_data:
```

## 部署策略

### 策略对比

| 策略 | 说明 | 优点 | 缺点 |
|------|------|------|------|
| Rolling Update | 逐步替换旧实例 | 零停机、简单 | 回滚较慢 |
| Blue-Green | 新旧两套环境切换 | 快速回滚 | 资源翻倍 |
| Canary | 小比例流量测试新版本 | 风险最小 | 实现复杂 |
| Recreate | 停旧启新 | 简单 | 有停机 |

### 推荐策略

- 开发环境：Rolling Update
- 预发布环境：Recreate
- 生产环境：Rolling Update（普通更新）或 Blue-Green（重大更新）

## 监控与告警

### 四大黄金信号

| 信号 | 指标 | 告警阈值 |
|------|------|----------|
| 延迟 | P95 响应时间 | > 500ms |
| 流量 | 请求速率 | 异常波动 > 50% |
| 错误 | 错误率 | > 1% |
| 饱和度 | 资源使用率 | CPU > 80%, Memory > 80% |

### 监控指标

```yaml
# 基础设施指标
- cpu_usage_percent
- memory_usage_percent
- disk_usage_percent
- network_bytes_in
- network_bytes_out

# 应用指标
- http_requests_total
- http_request_duration_seconds
- http_errors_total
- active_connections
- database_connections

# 业务指标
- user_signups_total
- order_creations_total
- payment_success_rate
- login_success_rate
```

### 告警规则

| 告警 | 条件 | 级别 | 通知方式 |
|------|------|------|----------|
| 服务不可用 | Health check 失败 | Critical | 电话 + 消息 |
| 错误率过高 | 错误率 > 5% | Critical | 电话 + 消息 |
| 响应时间过长 | P95 > 1s | High | 消息 |
| CPU 使用率过高 | CPU > 90% | High | 消息 |
| 内存使用率过高 | Memory > 90% | High | 消息 |
| 磁盘使用率过高 | Disk > 85% | Medium | 消息 |
| 证书即将过期 | < 30 天 | Medium | 消息 |

## 日志管理

### 日志收集

```
Application → stdout/stderr → Log Collector → Log Storage → Log Viewer
```

### 日志格式

```json
{
  "timestamp": "2026-07-11T12:00:00.000Z",
  "level": "INFO",
  "service": "user-service",
  "traceId": "abc-123",
  "spanId": "def-456",
  "message": "User logged in successfully",
  "context": {
    "userId": "user-123",
    "ip": "192.168.1.1"
  }
}
```

## 备份与恢复

### 备份策略

| 资源 | 频率 | 保留 |
|------|------|------|
| 数据库 | 每日全量 + 每小时增量 | 30 天 |
| 文件存储 | 每日 | 30 天 |
| 配置 | 每次变更 | 永久 |
| Secret | 每次变更 | 永久 |

### 恢复流程

```
1. 确认恢复需求
2. 选择恢复点
3. 创建恢复环境
4. 执行恢复
5. 验证数据完整性
6. 切换流量
```

## 安全规范

### 部署安全

- 使用非 root 用户运行容器
- 使用只读文件系统（如可能）
- 限制容器资源（CPU、Memory）
- 使用私有镜像仓库
- 扫描镜像漏洞
- 使用 TLS 加密通信
- 定期更新基础镜像

### Secret 管理

- 使用 Vault/Secret Manager
- 不在镜像中嵌入 Secret
- 不在环境变量中暴露 Secret
- 定期轮换密钥
- 审计 Secret 访问

## 反模式

| 反模式 | 说明 | 正确做法 |
|--------|------|----------|
| 手动部署 | 手动执行部署命令 | 使用 CI/CD 自动化 |
| 无回滚方案 | 部署失败无法回滚 | 必须有回滚方案 |
| 跳过测试 | 部署前不运行测试 | CI 中必须通过所有测试 |
| 环境不一致 | 各环境配置不同 | 使用配置管理保持一致性 |
| 无监控部署 | 部署后不监控 | 部署前确保监控已配置 |
| 硬编码配置 | 配置文件硬编码 | 使用环境变量和配置管理 |

## 检查清单

### 部署前检查

- [ ] 所有测试通过
- [ ] 安全扫描通过
- [ ] 代码审查通过
- [ ] 部署文档已更新
- [ ] 回滚方案已准备
- [ ] 监控告警已配置
- [ ] 数据库迁移已准备
- [ ] 环境变量已配置
- [ ] Secret 已配置

### 部署中检查

- [ ] 部署状态正常
- [ ] 健康检查通过
- [ ] 日志无异常
- [ ] 监控指标正常

### 部署后检查

- [ ] 冒烟测试通过
- [ ] 功能验证通过
- [ ] 性能指标正常
- [ ] 错误率正常
- [ ] 日志正常

---

**本规范是所有 AI Agent 进行部署的基础标准。每个 DevOps Engineer 在部署时必须严格遵守本规范。**