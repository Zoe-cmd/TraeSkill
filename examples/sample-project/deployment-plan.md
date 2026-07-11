# Deployment Plan: TaskFlow

<!--
Document: Deployment Plan
Version: 1.0.0
Author: DevOps Engineer
Created: 2026-07-11
Status: Approved
-->

## 1. 部署概述

| 属性 | 值 |
|------|------|
| 部署版本 | v1.0.0 |
| 部署策略 | 零停机滚动更新 (Rolling Update) |
| 部署日期 | 2026-07-11 |
| 预计停机时间 | 0 分钟 |
| 回滚时间 | < 5 分钟 |
| 部署工具 | ArgoCD + Kubernetes |

### 1.1 部署架构

```
┌──────────────────────────────────────────────────────────────────┐
│                         GitHub                                    │
│  ┌──────────────────────────────────────────────────────────┐    │
│  │  main branch (GitOps Source of Truth)                     │    │
│  │  ├── apps/api/Dockerfile                                  │    │
│  │  ├── apps/web/Dockerfile                                  │    │
│  │  └── k8s/                                                 │    │
│  │      ├── base/         (通用 K8s 资源)                    │    │
│  │      ├── staging/      (staging 环境 overlay)             │    │
│  │      └── production/   (production 环境 overlay)          │    │
│  └──────────────────────────────────────────────────────────┘    │
│                          │                                       │
│          ┌───────────────┼───────────────┐                       │
│          ▼               ▼               ▼                       │
│   ┌──────────┐   ┌──────────┐   ┌──────────┐                    │
│   │  CI      │   │  Build   │   │  Push    │                    │
│   │  Lint    │──▶│  Test    │──▶│  Image   │──▶ Docker Hub      │
│   │          │   │          │   │          │                    │
│   └──────────┘   └──────────┘   └──────────┘                    │
│                                                                  │
└──────────────────────────────────────────────────────────────────┘

┌──────────────────────────────────────────────────────────────────┐
│                    Kubernetes Cluster                             │
│  ┌──────────────────────────────────────────────────────────┐    │
│  │  ArgoCD (GitOps Controller)                               │    │
│  │  ├── 监听 GitHub main 分支 k8s/ 目录变化                  │    │
│  │  ├── 自动同步到 K8s 集群                                  │    │
│  │  └── 健康检查 + 自动回滚                                   │    │
│  └──────────────────────────────────────────────────────────┘    │
│                          │                                       │
│                          ▼                                       │
│  ┌──────────────────────────────────────────────────────────┐    │
│  │  Staging Namespace           Production Namespace         │    │
│  │  ├── web-deployment (1)      ├── web-deployment (2)       │    │
│  │  ├── api-deployment (1)      ├── api-deployment (3)       │    │
│  │  ├── worker-deployment (1)   ├── worker-deployment (2)    │    │
│  │  ├── postgres (1)            ├── postgres (1+1 replica)   │    │
│  │  └── redis (1)               ├── redis (1+2 sentinel)     │    │
│  │                              └── monitoring (Prom+Graf)   │    │
│  └──────────────────────────────────────────────────────────┘    │
└──────────────────────────────────────────────────────────────────┘
```

---

## 2. CI/CD 流水线

### 2.1 GitHub Actions 完整配置

```yaml
# .github/workflows/ci.yml
name: CI - Build, Test, and Push

on:
  push:
    branches: [develop, main]
  pull_request:
    branches: [develop, main]

env:
  REGISTRY: docker.io
  IMAGE_NAME_API: taskflow/taskflow-api
  IMAGE_NAME_WEB: taskflow/taskflow-web

jobs:
  lint:
    name: Lint & Type Check
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: pnpm/action-setup@v2
        with:
          version: 9
      - uses: actions/setup-node@v4
        with:
          node-version: '20'
          cache: 'pnpm'
      - run: pnpm install --frozen-lockfile
      - run: pnpm run lint
      - run: pnpm run type-check

  test:
    name: Unit & Integration Tests
    runs-on: ubuntu-latest
    needs: lint
    services:
      postgres:
        image: postgres:16-alpine
        env:
          POSTGRES_USER: test
          POSTGRES_PASSWORD: test
          POSTGRES_DB: taskflow_test
        ports:
          - 5432:5432
        options: >-
          --health-cmd pg_isready
          --health-interval 10s
          --health-timeout 5s
          --health-retries 5
      redis:
        image: redis:7-alpine
        ports:
          - 6379:6379
        options: >-
          --health-cmd "redis-cli ping"
          --health-interval 10s
          --health-timeout 5s
          --health-retries 5
    steps:
      - uses: actions/checkout@v4
      - uses: pnpm/action-setup@v2
        with:
          version: 9
      - uses: actions/setup-node@v4
        with:
          node-version: '20'
          cache: 'pnpm'
      - run: pnpm install --frozen-lockfile
      - run: pnpm run test:cov
        env:
          DATABASE_HOST: localhost
          DATABASE_PORT: 5432
          DATABASE_USER: test
          DATABASE_PASSWORD: test
          DATABASE_NAME: taskflow_test
          REDIS_HOST: localhost
          REDIS_PORT: 6379
          JWT_SECRET: test-secret-key
      - name: Upload coverage
        uses: codecov/codecov-action@v3
        with:
          files: ./coverage/lcov.info

  e2e:
    name: E2E Tests
    runs-on: ubuntu-latest
    needs: test
    timeout-minutes: 15
    steps:
      - uses: actions/checkout@v4
      - uses: pnpm/action-setup@v2
        with:
          version: 9
      - uses: actions/setup-node@v4
        with:
          node-version: '20'
          cache: 'pnpm'
      - run: pnpm install --frozen-lockfile
      - run: pnpm run test:e2e
        env:
          PLAYWRIGHT_BASE_URL: http://localhost:3000

  build-and-push:
    name: Build & Push Docker Images
    runs-on: ubuntu-latest
    needs: test
    if: github.ref == 'refs/heads/main'
    steps:
      - uses: actions/checkout@v4
      - name: Set up Docker Buildx
        uses: docker/setup-buildx-action@v3
      - name: Login to Docker Hub
        uses: docker/login-action@v3
        with:
          username: ${{ secrets.DOCKER_USERNAME }}
          password: ${{ secrets.DOCKER_PASSWORD }}
      - name: Extract metadata
        id: meta
        uses: docker/metadata-action@v5
        with:
          images: |
            ${{ env.IMAGE_NAME_API }}
            ${{ env.IMAGE_NAME_WEB }}
          tags: |
            type=sha,prefix=,format=short
            type=ref,event=branch
            type=semver,pattern={{version}}
      - name: Build and push API
        uses: docker/build-push-action@v5
        with:
          context: .
          file: ./apps/api/Dockerfile
          push: true
          tags: ${{ steps.meta.outputs.tags }}
          labels: ${{ steps.meta.outputs.labels }}
          cache-from: type=gha
          cache-to: type=gha,mode=max
      - name: Build and push Web
        uses: docker/build-push-action@v5
        with:
          context: .
          file: ./apps/web/Dockerfile
          push: true
          tags: ${{ steps.meta.outputs.tags }}
          labels: ${{ steps.meta.outputs.labels }}
          cache-from: type=gha
          cache-to: type=gha,mode=max
```

---

## 3. Docker 配置

### 3.1 API Dockerfile

```dockerfile
# apps/api/Dockerfile
FROM node:20-alpine AS builder
WORKDIR /app
RUN corepack enable && corepack prepare pnpm@9 --activate
COPY pnpm-lock.yaml pnpm-workspace.yaml ./
COPY package.json ./
COPY apps/api/package.json ./apps/api/
COPY packages/shared/package.json ./packages/shared/
RUN pnpm install --frozen-lockfile
COPY . .
RUN pnpm run build:api

FROM node:20-alpine AS runner
WORKDIR /app
RUN corepack enable && corepack prepare pnpm@9 --activate
ENV NODE_ENV=production
COPY --from=builder /app/node_modules ./node_modules
COPY --from=builder /app/apps/api/dist ./dist
COPY --from=builder /app/apps/api/package.json ./
COPY --from=builder /app/packages/shared ./packages/shared
EXPOSE 4000
USER node
CMD ["node", "dist/main.js"]
```

### 3.2 docker-compose.yml (生产环境)

```yaml
# docker-compose.yml
version: '3.8'

services:
  postgres:
    image: postgres:16-alpine
    container_name: taskflow-postgres
    restart: unless-stopped
    environment:
      POSTGRES_USER: ${DATABASE_USER}
      POSTGRES_PASSWORD: ${DATABASE_PASSWORD}
      POSTGRES_DB: ${DATABASE_NAME}
    volumes:
      - postgres_data:/var/lib/postgresql/data
      - ./backups:/backups
    ports:
      - '5432:5432'
    healthcheck:
      test: ['CMD-SHELL', 'pg_isready -U ${DATABASE_USER}']
      interval: 10s
      timeout: 5s
      retries: 5

  redis:
    image: redis:7-alpine
    container_name: taskflow-redis
    restart: unless-stopped
    command: redis-server --appendonly yes --requirepass ${REDIS_PASSWORD}
    volumes:
      - redis_data:/data
    ports:
      - '6379:6379'
    healthcheck:
      test: ['CMD', 'redis-cli', '--raw', 'incr', 'ping']
      interval: 10s
      timeout: 5s
      retries: 5

  api:
    image: taskflow/taskflow-api:${IMAGE_TAG:-latest}
    container_name: taskflow-api
    restart: unless-stopped
    environment:
      NODE_ENV: production
      DATABASE_HOST: postgres
      DATABASE_PORT: 5432
      DATABASE_USER: ${DATABASE_USER}
      DATABASE_PASSWORD: ${DATABASE_PASSWORD}
      DATABASE_NAME: ${DATABASE_NAME}
      REDIS_HOST: redis
      REDIS_PORT: 6379
      REDIS_PASSWORD: ${REDIS_PASSWORD}
      JWT_PRIVATE_KEY: ${JWT_PRIVATE_KEY}
      JWT_PUBLIC_KEY: ${JWT_PUBLIC_KEY}
      OPENAI_API_KEY: ${OPENAI_API_KEY}
      SENDGRID_API_KEY: ${SENDGRID_API_KEY}
      AWS_ACCESS_KEY_ID: ${AWS_ACCESS_KEY_ID}
      AWS_SECRET_ACCESS_KEY: ${AWS_SECRET_ACCESS_KEY}
      AWS_S3_BUCKET: ${AWS_S3_BUCKET}
      AWS_REGION: ${AWS_REGION}
    ports:
      - '4000:4000'
    depends_on:
      postgres:
        condition: service_healthy
      redis:
        condition: service_healthy
    healthcheck:
      test: ['CMD', 'curl', '-f', 'http://localhost:4000/health']
      interval: 30s
      timeout: 10s
      retries: 3

  web:
    image: taskflow/taskflow-web:${IMAGE_TAG:-latest}
    container_name: taskflow-web
    restart: unless-stopped
    ports:
      - '3000:80'
    depends_on:
      - api
    healthcheck:
      test: ['CMD', 'curl', '-f', 'http://localhost:80/health']
      interval: 30s
      timeout: 10s
      retries: 3

volumes:
  postgres_data:
  redis_data:
```

---

## 4. 环境配置

| 环境 | 域名 | 数据库 | 镜像标签 | 自动部署 |
|------|------|--------|----------|----------|
| dev | localhost | Docker Compose | latest | 否 |
| staging | staging.taskflow.com | 独立 PostgreSQL | sha-{short} | 推送 develop 分支自动部署 |
| production | taskflow.com | 高可用 PostgreSQL | v{semver} | 推送 main 分支自动部署 |

---

## 5. 监控与告警

### 5.1 监控指标

| 指标类别 | 指标 | 工具 | 告警阈值 |
|----------|------|------|----------|
| 可用性 | HTTP 2xx 率 | Prometheus | < 99.5% |
| 延迟 | API P95 响应时间 | Prometheus | > 500ms |
| 错误率 | 5xx 错误率 | Prometheus | > 1% |
| 资源 | CPU 使用率 | Prometheus | > 80% |
| 资源 | 内存使用率 | Prometheus | > 85% |
| 数据库 | 活跃连接数 | PostgreSQL Exporter | > 80% 连接池 |
| 业务 | 登录失败率 | 自定义指标 | > 10% |
| 业务 | AI 排期降级率 | 自定义指标 | > 5% |

### 5.2 告警配置示例

```yaml
# k8s/base/prometheus-rules.yaml
apiVersion: monitoring.coreos.com/v1
kind: PrometheusRule
metadata:
  name: taskflow-alerts
spec:
  groups:
    - name: taskflow
      rules:
        - alert: HighErrorRate
          expr: rate(http_requests_total{status=~"5.."}[5m]) > 0.01
          for: 5m
          labels:
            severity: critical
          annotations:
            summary: "High error rate detected"
            description: "5xx error rate is {{ $value }} over last 5min"

        - alert: HighLatency
          expr: histogram_quantile(0.95, rate(http_request_duration_seconds_bucket[5m])) > 0.5
          for: 5m
          labels:
            severity: warning
          annotations:
            summary: "API P95 latency > 500ms"

        - alert: AIServiceDegraded
          expr: rate(ai_scheduling_degraded_total[15m]) > 0.05
          for: 10m
          labels:
            severity: warning
          annotations:
            summary: "AI scheduling service degraded"
```

---

## 6. 备份与恢复

### 6.1 备份策略

| 备份类型 | 频率 | 保留时间 | 工具 |
|----------|------|----------|------|
| 全量备份 | 每日 03:00 UTC | 30 天 | pg_dump |
| WAL 归档 | 持续 | 7 天 | PostgreSQL WAL Archiving |
| S3 附件 | 每日 04:00 UTC | 30 天 | AWS S3 Replication |

### 6.2 备份脚本

```bash
#!/bin/bash
# scripts/backup-db.sh

BACKUP_DIR="/backups"
TIMESTAMP=$(date +%Y%m%d_%H%M%S)
BACKUP_FILE="${BACKUP_DIR}/taskflow_${TIMESTAMP}.sql.gz"

export PGPASSWORD="${DATABASE_PASSWORD}"

pg_dump -h ${DATABASE_HOST} -U ${DATABASE_USER} -d ${DATABASE_NAME} \
    --no-owner --no-acl | gzip > ${BACKUP_FILE}

# 上传到 S3
aws s3 cp ${BACKUP_FILE} s3://${BACKUP_BUCKET}/database/${TIMESTAMP}.sql.gz

# 删除 30 天前的本地备份
find ${BACKUP_DIR} -name "*.sql.gz" -mtime +30 -delete

echo "Backup completed: ${BACKUP_FILE}"
```

### 6.3 恢复步骤

```bash
# 1. 从 S3 下载备份文件
aws s3 cp s3://${BACKUP_BUCKET}/database/20260711_030000.sql.gz ./restore.sql.gz

# 2. 解压
gunzip restore.sql.gz

# 3. 恢复数据库
psql -h ${DATABASE_HOST} -U ${DATABASE_USER} -d ${DATABASE_NAME} < restore.sql

# 4. 验证
psql -h ${DATABASE_HOST} -U ${DATABASE_USER} -d ${DATABASE_NAME} \
    -c "SELECT COUNT(*) FROM users; SELECT COUNT(*) FROM tasks;"
```

---

## 7. 回滚方案

### 7.1 自动回滚

ArgoCD 配置自动回滚：当健康检查连续失败 3 次，自动回滚到上一个稳定版本。

### 7.2 手动回滚

```bash
# 方式 1: ArgoCD 回滚
argocd app rollback taskflow-production

# 方式 2: kubectl 回滚
kubectl rollout undo deployment/api -n production
kubectl rollout undo deployment/web -n production
kubectl rollout undo deployment/worker -n production

# 方式 3: 数据库回滚
pnpm run migration:revert
```

### 7.3 回滚检查清单

- [ ] 确认回滚版本号
- [ ] 通知团队回滚开始
- [ ] 执行回滚操作
- [ ] 验证核心功能（登录、创建任务、查看列表）
- [ ] 检查数据库迁移是否需要回滚
- [ ] 确认监控指标恢复正常
- [ ] 通知团队回滚完成
- [ ] 记录回滚原因到 Postmortem 文档

---

## 8. 部署检查清单

### 部署前

- [ ] 所有 CI 流水线通过
- [ ] 代码审查完成
- [ ] 数据库迁移已测试
- [ ] 发布说明已准备
- [ ] 备份已完成
- [ ] 回滚方案已确认

### 部署中

- [ ] 部署到 staging 环境
- [ ] 冒烟测试通过
- [ ] 性能测试通过
- [ ] 部署到 production 环境
- [ ] 监控指标正常
- [ ] 日志无异常错误

### 部署后

- [ ] 核心功能验证通过
- [ ] 监控仪表盘正常
- [ ] 用户反馈渠道畅通
- [ ] 发布说明已发布

---

**版本: 1.0.0 | 作者: DevOps Engineer | 日期: 2026-07-11**