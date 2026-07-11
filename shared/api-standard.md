# API Standard - 统一 API 规范

## 概述

本文档定义了所有 AI Agent 在设计和实现 API 时必须遵守的统一 API 规范。该规范涵盖 RESTful API 设计、请求/响应格式、错误处理、版本管理、安全策略等。

## 适用范围

- 本规范适用于所有后端 API 的设计和实现
- 包括但不限于：REST API、内部服务 API、第三方 API 集成
- 所有 Backend Engineer 在编写 API 时必须遵守本规范

## 语言规范

本规范文档及所有相关文档、代码注释、提交信息均使用简体中文编写。技术术语（如 API、REST、JSON、HTTP、JWT、OAuth、OpenAPI、Swagger、CORS 等）保留英文原文。代码标识符（变量名、函数名、类名等）遵循各语言的命名规范，禁止使用中文命名。

## API 设计原则

| 原则 | 说明 |
|------|------|
| 资源导向 | API 围绕资源设计，而非操作 |
| 一致性 | 所有 API 遵循统一的命名、格式和错误处理 |
| 可预测性 | API 行为可预测，调用者不需要猜测 |
| 向后兼容 | 新版本不破坏旧版本 |
| 无状态 | 每个请求包含所有必要信息，不依赖服务器状态 |
| 幂等性 | GET、PUT、DELETE 必须是幂等的 |
| 安全性 | 所有 API 必须有适当的安全措施 |

## URL 设计规范

### 基础 URL 模式

```
https://api.{domain}.com/v{version}/{resource}
```

### 资源命名

| 规则 | 示例 |
|------|------|
| 使用名词复数 | `/users`, `/orders`, `/products` |
| 使用 kebab-case | `/user-profiles`, `/order-items` |
| 不使用动词 | 不用 `/getUsers`，用 `GET /users` |
| 层级关系用路径 | `/users/{userId}/orders` |
| 过滤用查询参数 | `/users?status=active` |

### 资源层级

```
/users                          # 用户集合
/users/{userId}                 # 单个用户
/users/{userId}/orders          # 用户的订单集合
/users/{userId}/orders/{orderId} # 用户的单个订单
/orders/{orderId}/items         # 订单的订单项集合
/orders/{orderId}/items/{itemId} # 订单的单个订单项
```

### 避免深层嵌套

嵌套深度不超过 3 层：

```
✅ 正确: /users/{userId}/orders
❌ 错误: /users/{userId}/orders/{orderId}/items/{itemId}/comments
```

## HTTP 方法规范

### 方法定义

| 方法 | 操作 | 幂等 | 安全 | 说明 |
|------|------|------|------|------|
| GET | 读取 | 是 | 是 | 获取资源，不修改服务器状态 |
| POST | 创建 | 否 | 否 | 创建新资源 |
| PUT | 全量更新 | 是 | 否 | 替换整个资源 |
| PATCH | 部分更新 | 否 | 否 | 部分更新资源 |
| DELETE | 删除 | 是 | 否 | 删除资源 |
| HEAD | 读取元数据 | 是 | 是 | 获取响应头，无响应体 |
| OPTIONS | 获取选项 | 是 | 是 | 获取支持的 HTTP 方法 |

### 方法使用规则

| 场景 | 方法 | 示例 |
|------|------|------|
| 获取列表 | GET | `GET /users` |
| 获取单个 | GET | `GET /users/{id}` |
| 创建资源 | POST | `POST /users` |
| 全量替换 | PUT | `PUT /users/{id}` |
| 部分更新 | PATCH | `PATCH /users/{id}` |
| 删除资源 | DELETE | `DELETE /users/{id}` |
| 搜索 | GET | `GET /users?search=keyword` |
| 批量操作 | POST | `POST /users/bulk-delete` |
| 执行动作 | POST | `POST /orders/{id}/cancel` |

## 请求规范

### 请求头

```http
Content-Type: application/json
Accept: application/json
Authorization: Bearer <token>
X-Request-ID: <uuid>
X-Client-Version: <version>
Accept-Language: zh-CN
```

### 查询参数

| 参数 | 类型 | 说明 | 示例 |
|------|------|------|------|
| page | integer | 页码（从 1 开始） | `?page=1` |
| limit | integer | 每页数量 | `?limit=20` |
| sort | string | 排序字段 | `?sort=created_at` |
| order | string | 排序方向 | `?order=desc` |
| search | string | 搜索关键词 | `?search=keyword` |
| fields | string | 返回字段 | `?fields=id,name,email` |
| expand | string | 展开关联 | `?expand=orders` |
| filter | string | 过滤条件 | `?filter[status]=active` |

### 请求体

```json
{
  "name": "John Doe",
  "email": "john@example.com",
  "role": "user"
}
```

### 请求体规则

- 使用 JSON 格式
- 字段名使用 camelCase
- 日期时间使用 ISO 8601 格式
- 金额使用字符串（避免浮点精度问题）
- 文件上传使用 multipart/form-data

## 响应规范

### 成功响应格式

#### 单个资源

```json
{
  "data": {
    "id": "user-123",
    "type": "user",
    "attributes": {
      "name": "John Doe",
      "email": "john@example.com",
      "role": "user",
      "createdAt": "2026-07-11T12:00:00Z",
      "updatedAt": "2026-07-11T12:00:00Z"
    }
  },
  "meta": {
    "requestId": "req-abc-123"
  }
}
```

#### 资源集合

```json
{
  "data": [
    {
      "id": "user-123",
      "type": "user",
      "attributes": {
        "name": "John Doe",
        "email": "john@example.com"
      }
    }
  ],
  "meta": {
    "requestId": "req-abc-123",
    "pagination": {
      "page": 1,
      "limit": 20,
      "total": 100,
      "totalPages": 5
    }
  }
}
```

#### 创建资源

```json
{
  "data": {
    "id": "user-123",
    "type": "user",
    "attributes": {
      "name": "John Doe",
      "email": "john@example.com",
      "role": "user",
      "createdAt": "2026-07-11T12:00:00Z"
    }
  },
  "meta": {
    "requestId": "req-abc-123"
  }
}
```

HTTP 状态码: `201 Created`
响应头: `Location: /users/user-123`

#### 删除资源

HTTP 状态码: `204 No Content`
无响应体

### 错误响应格式

```json
{
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Validation failed",
    "details": [
      {
        "field": "email",
        "message": "Invalid email format",
        "code": "INVALID_FORMAT"
      },
      {
        "field": "name",
        "message": "Name is required",
        "code": "REQUIRED"
      }
    ]
  },
  "meta": {
    "requestId": "req-abc-123"
  }
}
```

### HTTP 状态码

#### 2xx 成功

| 状态码 | 说明 | 使用场景 |
|--------|------|----------|
| 200 OK | 请求成功 | GET、PUT、PATCH 成功 |
| 201 Created | 资源创建成功 | POST 成功 |
| 202 Accepted | 请求已接受，异步处理 | 异步操作 |
| 204 No Content | 无内容 | DELETE 成功 |

#### 3xx 重定向

| 状态码 | 说明 | 使用场景 |
|--------|------|----------|
| 301 Moved Permanently | 永久重定向 | URL 变更 |
| 304 Not Modified | 未修改 | 缓存命中 |

#### 4xx 客户端错误

| 状态码 | 说明 | 使用场景 |
|--------|------|----------|
| 400 Bad Request | 请求格式错误 | 参数验证失败 |
| 401 Unauthorized | 未认证 | 缺少或无效 Token |
| 403 Forbidden | 无权限 | 权限不足 |
| 404 Not Found | 资源不存在 | 资源未找到 |
| 405 Method Not Allowed | 方法不允许 | HTTP 方法错误 |
| 409 Conflict | 资源冲突 | 重复创建 |
| 422 Unprocessable Entity | 语义错误 | 业务逻辑验证失败 |
| 429 Too Many Requests | 请求过多 | 限流 |

#### 5xx 服务器错误

| 状态码 | 说明 | 使用场景 |
|--------|------|----------|
| 500 Internal Server Error | 服务器内部错误 | 未预期的错误 |
| 502 Bad Gateway | 网关错误 | 上游服务错误 |
| 503 Service Unavailable | 服务不可用 | 维护中/过载 |
| 504 Gateway Timeout | 网关超时 | 上游服务超时 |

## 分页规范

### 分页参数

| 参数 | 默认值 | 最大值 | 说明 |
|------|--------|--------|------|
| page | 1 | - | 页码 |
| limit | 20 | 100 | 每页数量 |

### 游标分页（推荐）

```json
{
  "data": [...],
  "meta": {
    "pagination": {
      "type": "cursor",
      "nextCursor": "eyJpZCI6InVzZXItMTIzIn0=",
      "hasMore": true
    }
  }
}
```

### 分页响应头

```http
X-Pagination-Total: 100
X-Pagination-Page: 1
X-Pagination-Limit: 20
X-Pagination-Total-Pages: 5
Link: </users?page=2>; rel="next", </users?page=5>; rel="last"
```

## 过滤、排序、搜索

### 过滤

```
GET /users?filter[status]=active
GET /users?filter[role]=admin&filter[status]=active
GET /orders?filter[createdAt][gte]=2026-01-01&filter[createdAt][lte]=2026-12-31
GET /products?filter[price][gte]=100&filter[price][lte]=500
```

### 过滤操作符

| 操作符 | 说明 | 示例 |
|--------|------|------|
| eq | 等于 | `?filter[name][eq]=John` |
| neq | 不等于 | `?filter[status][neq]=deleted` |
| gt | 大于 | `?filter[price][gt]=100` |
| gte | 大于等于 | `?filter[date][gte]=2026-01-01` |
| lt | 小于 | `?filter[age][lt]=18` |
| lte | 小于等于 | `?filter[price][lte]=1000` |
| in | 包含 | `?filter[status][in]=active,pending` |
| nin | 不包含 | `?filter[status][nin]=deleted,archived` |
| like | 模糊匹配 | `?filter[name][like]=John` |
| null | 为空 | `?filter[deletedAt][null]=true` |

### 排序

```
GET /users?sort=createdAt
GET /users?sort=-createdAt         # 降序
GET /users?sort=name,-createdAt    # 多字段排序
```

### 搜索

```
GET /users?search=john
GET /products?search=iphone&fields=name,description
```

## 字段选择

### 稀疏字段集

```
GET /users?fields=id,name,email
GET /users/{id}?fields=id,name,email,role
```

### 展开关联

```
GET /users/{id}?expand=orders
GET /users/{id}?expand=orders,profile
GET /users/{id}?expand=orders.items
```

## 版本管理

### 版本策略

| 策略 | 说明 | 推荐 |
|------|------|------|
| URL 路径 | `/v1/users`, `/v2/users` | 推荐 |
| 请求头 | `Accept: application/vnd.api+v1+json` | 可选 |
| 查询参数 | `/users?version=1` | 不推荐 |

### 版本规则

- 主版本号变更：不兼容的 API 变更
- 次版本号变更：新增功能，向后兼容
- 同时支持最多 2 个主版本
- 旧版本有明确的废弃时间表

## 认证与授权

### 认证方式

| 方式 | 使用场景 | 说明 |
|------|----------|------|
| Bearer Token (JWT) | Web API | 标准认证方式 |
| API Key | 服务间调用 | 内部服务认证 |
| OAuth 2.0 | 第三方集成 | 用户授权 |
| Basic Auth | 内部工具 | 仅用于内部工具 |

### 认证请求头

```http
Authorization: Bearer eyJhbGciOiJIUzI1NiIs...
X-API-Key: sk_live_abc123...
```

### 授权检查

每个 API 端点必须明确定义所需的权限：

```yaml
GET /users:
  auth: required
  permissions: [users:read]
  
POST /users:
  auth: required
  permissions: [users:create]
  
DELETE /users/{id}:
  auth: required
  permissions: [users:delete]
  
GET /health:
  auth: none
```

## 速率限制

### 限流响应头

```http
X-RateLimit-Limit: 1000
X-RateLimit-Remaining: 999
X-RateLimit-Reset: 1699075200
Retry-After: 60
```

### 限流响应

```json
{
  "error": {
    "code": "RATE_LIMIT_EXCEEDED",
    "message": "Too many requests. Please try again in 60 seconds."
  },
  "meta": {
    "requestId": "req-abc-123",
    "retryAfter": 60
  }
}
```

HTTP 状态码: `429 Too Many Requests`

## API 文档规范

### OpenAPI 规范

所有 API 必须使用 OpenAPI 3.0+ 规范编写文档：

```yaml
openapi: 3.0.3
info:
  title: User Service API
  version: 1.0.0
  description: 用户服务 API 文档

paths:
  /users:
    get:
      summary: 获取用户列表
      operationId: listUsers
      tags:
        - Users
      parameters:
        - name: page
          in: query
          schema:
            type: integer
            default: 1
        - name: limit
          in: query
          schema:
            type: integer
            default: 20
      responses:
        '200':
          description: 用户列表
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/UserListResponse'
        '401':
          $ref: '#/components/responses/Unauthorized'

components:
  schemas:
    User:
      type: object
      properties:
        id:
          type: string
          format: uuid
        name:
          type: string
        email:
          type: string
          format: email
```

## 性能规范

### 响应时间

| 端点类型 | 目标 | 最大 |
|----------|------|------|
| 简单查询 | < 100ms | 500ms |
| 复杂查询 | < 500ms | 2s |
| 列表查询 | < 200ms | 1s |
| 写入操作 | < 200ms | 1s |

### 数据量限制

- 列表查询默认返回 20 条，最大 100 条
- 请求体大小不超过 10MB
- 文件上传大小不超过 50MB

## 安全规范

### API 安全规则

- 所有 API 必须使用 HTTPS
- 所有 API 必须有认证（公开 API 除外）
- 所有 API 必须有授权检查
- 所有 API 必须有输入验证
- 所有 API 必须有限流措施
- 敏感数据必须加密传输
- 响应中不暴露内部错误信息

### CORS 配置

```http
Access-Control-Allow-Origin: https://app.example.com
Access-Control-Allow-Methods: GET, POST, PUT, PATCH, DELETE, OPTIONS
Access-Control-Allow-Headers: Content-Type, Authorization, X-Request-ID
Access-Control-Max-Age: 86400
```

## 反模式

| 反模式 | 说明 | 正确做法 |
|--------|------|----------|
| 在 URL 中使用动词 | `/getUser`, `/createOrder` | 使用 HTTP 方法 + 名词资源 |
| 深层嵌套 | `/a/b/c/d/e/f` | 嵌套不超过 3 层 |
| 返回所有字段 | 不加选择的返回所有列 | 支持字段选择 |
| 无分页 | 列表接口无分页 | 所有列表接口必须分页 |
| 不一致的错误格式 | 每个端点不同的错误格式 | 统一错误格式 |
| 暴露内部实现 | API 响应包含数据库字段名 | 使用 DTO 映射 |
| 无版本管理 | API 变更无版本号 | 所有 API 必须有版本号 |

## 检查清单

### API 设计检查

- [ ] URL 使用名词复数，kebab-case
- [ ] HTTP 方法使用正确
- [ ] 请求/响应格式统一
- [ ] 错误格式统一
- [ ] 分页参数正确
- [ ] 过滤参数正确
- [ ] 排序参数正确
- [ ] 版本号存在
- [ ] 认证授权完整
- [ ] 限流措施到位
- [ ] OpenAPI 文档完整
- [ ] 输入验证完整
- [ ] 错误处理完整
- [ ] 日志记录完整

### API 实现检查

- [ ] HTTPS 强制
- [ ] CORS 配置正确
- [ ] 请求体验证
- [ ] 参数验证
- [ ] 权限检查
- [ ] 限流实现
- [ ] 错误处理
- [ ] 日志记录
- [ ] 监控埋点
- [ ] 超时设置
- [ ] 重试策略
- [ ] 降级策略

---

**本规范是所有 AI Agent 设计 API 的基础标准。每个 Backend Engineer 在编写 API 时必须严格遵守本规范。**