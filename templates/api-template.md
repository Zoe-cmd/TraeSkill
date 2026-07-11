# API Template - API 文档模板

## 使用说明

本模板用于 Backend Engineer 编写 API 规范文档。请完整填写所有章节。

## 文档元信息

<!--
Document: API Specification
Version: 1.0.0
Author: Backend Engineer
Created: {YYYY-MM-DD}
Updated: {YYYY-MM-DD}
Status: Draft
-->

---

# API Specification: {服务名称}

## 1. 概述

### 1.1 基本信息

| 属性 | 值 |
|------|------|
| 服务名称 | {服务名称} |
| 版本 | v1 |
| 基础 URL | `https://api.{domain}.com/v1` |
| 协议 | HTTPS |
| 数据格式 | JSON |

### 1.2 通用规范

本 API 遵循 `shared/api-standard.md` 中定义的所有规范。

## 2. 认证与授权

### 2.1 认证方式

```
Authorization: Bearer {token}
```

### 2.2 权限模型

| 角色 | 权限 |
|------|------|
| admin | 全部权限 |
| user | 基础读写权限 |
| viewer | 只读权限 |

## 3. 通用响应

### 3.1 成功响应

```json
{
  "data": {},
  "meta": {
    "requestId": "uuid",
    "pagination": {
      "page": 1,
      "limit": 20,
      "total": 100,
      "totalPages": 5
    }
  }
}
```

### 3.2 错误响应

```json
{
  "error": {
    "code": "ERROR_CODE",
    "message": "Human readable message",
    "details": []
  },
  "meta": {
    "requestId": "uuid"
  }
}
```

## 4. API 端点

### 4.1 资源: {资源名称}

#### 4.1.1 获取列表

```
GET /{resources}
```

**描述**: 获取{资源}列表

**认证**: 需要

**权限**: `{resource}:read`

**查询参数**:

| 参数 | 类型 | 必填 | 默认值 | 说明 |
|------|------|------|--------|------|
| page | integer | 否 | 1 | 页码 |
| limit | integer | 否 | 20 | 每页数量 |
| sort | string | 否 | created_at | 排序字段 |
| order | string | 否 | desc | 排序方向 |
| search | string | 否 | - | 搜索关键词 |
| filter[status] | string | 否 | - | 按状态过滤 |

**响应**:

```json
{
  "data": [
    {
      "id": "uuid",
      "type": "{resource}",
      "attributes": {
        "name": "string",
        "status": "active",
        "createdAt": "2026-07-11T12:00:00Z",
        "updatedAt": "2026-07-11T12:00:00Z"
      }
    }
  ],
  "meta": {
    "requestId": "uuid",
    "pagination": {
      "page": 1,
      "limit": 20,
      "total": 100,
      "totalPages": 5
    }
  }
}
```

**错误**:

| 状态码 | 错误码 | 说明 |
|--------|--------|------|
| 401 | UNAUTHORIZED | 未认证 |
| 403 | FORBIDDEN | 无权限 |

#### 4.1.2 获取单个

```
GET /{resources}/{id}
```

**描述**: 获取单个{资源}

**路径参数**:

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| id | UUID | 是 | 资源 ID |

**响应**:

```json
{
  "data": {
    "id": "uuid",
    "type": "{resource}",
    "attributes": {
      "name": "string",
      "status": "active",
      "createdAt": "2026-07-11T12:00:00Z",
      "updatedAt": "2026-07-11T12:00:00Z"
    }
  },
  "meta": {
    "requestId": "uuid"
  }
}
```

**错误**:

| 状态码 | 错误码 | 说明 |
|--------|--------|------|
| 404 | NOT_FOUND | 资源不存在 |

#### 4.1.3 创建

```
POST /{resources}
```

**描述**: 创建新的{资源}

**请求体**:

```json
{
  "name": "string (required)",
  "description": "string (optional)",
  "status": "active"
}
```

**验证规则**:

| 字段 | 规则 |
|------|------|
| name | 必填，1-100 字符 |
| description | 可选，最多 500 字符 |
| status | 必填，枚举值: active, inactive |

**响应**:

```json
{
  "data": {
    "id": "uuid",
    "type": "{resource}",
    "attributes": {
      "name": "string",
      "status": "active",
      "createdAt": "2026-07-11T12:00:00Z"
    }
  },
  "meta": {
    "requestId": "uuid"
  }
}
```

HTTP 状态码: `201 Created`

**错误**:

| 状态码 | 错误码 | 说明 |
|--------|--------|------|
| 400 | VALIDATION_ERROR | 参数验证失败 |
| 409 | CONFLICT | 资源已存在 |

#### 4.1.4 更新

```
PATCH /{resources}/{id}
```

**描述**: 部分更新{资源}

**请求体**:

```json
{
  "name": "string",
  "status": "active"
}
```

**响应**:

```json
{
  "data": {
    "id": "uuid",
    "type": "{resource}",
    "attributes": {
      "name": "string",
      "status": "active",
      "updatedAt": "2026-07-11T12:00:00Z"
    }
  },
  "meta": {
    "requestId": "uuid"
  }
}
```

#### 4.1.5 删除

```
DELETE /{resources}/{id}
```

**描述**: 删除{资源}（软删除）

**响应**: HTTP 状态码 `204 No Content`

## 5. 数据模型

### 5.1 {资源名称}

```json
{
  "id": "uuid",
  "type": "{resource}",
  "attributes": {
    "name": "string",
    "description": "string",
    "status": "active",
    "createdAt": "2026-07-11T12:00:00Z",
    "updatedAt": "2026-07-11T12:00:00Z",
    "deletedAt": null
  }
}
```

| 字段 | 类型 | 说明 | 约束 |
|------|------|------|------|
| id | UUID | 唯一标识符 | 自动生成 |
| name | string | 名称 | 必填，1-100 字符 |
| description | string | 描述 | 可选，最多 500 字符 |
| status | string | 状态 | active, inactive, deleted |
| createdAt | datetime | 创建时间 | 自动生成 |
| updatedAt | datetime | 更新时间 | 自动更新 |
| deletedAt | datetime | 删除时间 | 软删除标记 |

## 6. 错误码

| 错误码 | HTTP 状态码 | 说明 |
|--------|-------------|------|
| VALIDATION_ERROR | 400 | 请求参数验证失败 |
| UNAUTHORIZED | 401 | 未认证或 Token 无效 |
| FORBIDDEN | 403 | 无权限 |
| NOT_FOUND | 404 | 资源不存在 |
| CONFLICT | 409 | 资源冲突 |
| INTERNAL_ERROR | 500 | 服务器内部错误 |

## 7. 附录

### 7.1 变更历史

| 版本 | 日期 | 变更说明 | 作者 |
|------|------|----------|------|
| 1.0.0 | {YYYY-MM-DD} | 初始版本 | Backend Engineer |

---

## 完整的 API 请求/响应格式示例

### 成功响应示例

```json
// GET /api/v1/users/a0000000-0000-0000-0000-000000000001
// 200 OK
{
  "data": {
    "id": "a0000000-0000-0000-0000-000000000001",
    "type": "user",
    "attributes": {
      "email": "user@example.com",
      "displayName": "张三",
      "phone": "13800138000",
      "role": "user",
      "status": "active",
      "avatarUrl": "https://cdn.shopflow.com/avatars/abc123.jpg",
      "createdAt": "2026-07-11T12:00:00.000Z",
      "updatedAt": "2026-07-11T15:30:00.000Z"
    },
    "relationships": {
      "orders": {
        "links": {
          "related": "/api/v1/users/a0000000-0000-0000-0000-000000000001/orders"
        }
      }
    }
  },
  "meta": {
    "requestId": "req_abc123def456",
    "serverTime": "2026-07-11T16:00:00.000Z"
  }
}
```

### 分页响应示例

```json
// GET /api/v1/users?page=1&limit=20&status=active&sort=-createdAt
// 200 OK
{
  "data": [
    {
      "id": "a0000000-0000-0000-0000-000000000001",
      "type": "user",
      "attributes": {
        "email": "user1@example.com",
        "displayName": "张三",
        "role": "user",
        "status": "active",
        "createdAt": "2026-07-11T12:00:00.000Z"
      }
    }
  ],
  "meta": {
    "requestId": "req_abc123def456",
    "pagination": {
      "page": 1,
      "limit": 20,
      "totalItems": 156,
      "totalPages": 8,
      "hasNextPage": true,
      "hasPreviousPage": false
    }
  },
  "links": {
    "self": "/api/v1/users?page=1&limit=20",
    "first": "/api/v1/users?page=1&limit=20",
    "prev": null,
    "next": "/api/v1/users?page=2&limit=20",
    "last": "/api/v1/users?page=8&limit=20"
  }
}
```

### 创建资源响应示例

```json
// POST /api/v1/users
// 201 Created
{
  "data": {
    "id": "a0000000-0000-0000-0000-000000000002",
    "type": "user",
    "attributes": {
      "email": "newuser@example.com",
      "displayName": "新用户",
      "role": "user",
      "status": "active",
      "createdAt": "2026-07-11T16:00:00.000Z"
    }
  },
  "meta": {
    "requestId": "req_xyz789ghi012"
  }
}
```

### 错误响应示例

```json
// POST /api/v1/auth/register
// 400 Bad Request - 验证错误
{
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "请求参数验证失败",
    "details": [
      {
        "field": "email",
        "message": "邮箱格式不正确",
        "code": "INVALID_EMAIL"
      },
      {
        "field": "password",
        "message": "密码长度不能少于 8 个字符",
        "code": "PASSWORD_TOO_SHORT"
      }
    ]
  },
  "meta": {
    "requestId": "req_xyz789ghi013"
  }
}
```

```json
// POST /api/v1/auth/login
// 401 Unauthorized
{
  "error": {
    "code": "INVALID_CREDENTIALS",
    "message": "邮箱或密码错误，请重试",
    "details": []
  },
  "meta": {
    "requestId": "req_xyz789ghi014"
  }
}
```

```json
// GET /api/v1/users/00000000-0000-0000-0000-000000000000
// 404 Not Found
{
  "error": {
    "code": "RESOURCE_NOT_FOUND",
    "message": "用户不存在",
    "details": [
      {
        "field": "id",
        "message": "ID 为 00000000-0000-0000-0000-000000000000 的用户不存在"
      }
    ]
  },
  "meta": {
    "requestId": "req_xyz789ghi015"
  }
}
```

```json
// POST /api/v1/auth/register
// 409 Conflict
{
  "error": {
    "code": "EMAIL_ALREADY_EXISTS",
    "message": "该邮箱已被注册，请使用其他邮箱或直接登录",
    "details": []
  },
  "meta": {
    "requestId": "req_xyz789ghi016"
  }
}
```

```json
// Any endpoint
// 429 Too Many Requests
{
  "error": {
    "code": "RATE_LIMIT_EXCEEDED",
    "message": "请求过于频繁，请稍后重试",
    "details": [
      {
        "field": "retryAfter",
        "message": "60"
      }
    ]
  },
  "meta": {
    "requestId": "req_xyz789ghi017"
  }
}
```

```json
// Any endpoint
// 500 Internal Server Error
{
  "error": {
    "code": "INTERNAL_ERROR",
    "message": "服务器内部错误，请稍后重试。如问题持续存在，请联系技术支持。",
    "details": []
  },
  "meta": {
    "requestId": "req_xyz789ghi018"
  }
}
```

## 错误处理模板

### 错误码规范

| HTTP 状态码 | 错误代码 | 说明 | 使用场景 |
|-------------|----------|------|----------|
| 400 | VALIDATION_ERROR | 请求参数验证失败 | 字段格式不正确、必填字段缺失 |
| 400 | INVALID_EMAIL | 邮箱格式不正确 | 邮箱字段不符合 RFC 5322 |
| 400 | PASSWORD_TOO_SHORT | 密码太短 | 密码长度 < 8 字符 |
| 400 | PASSWORD_TOO_WEAK | 密码太弱 | 密码强度不足 |
| 400 | INVALID_PAGINATION | 分页参数无效 | page < 1 或 limit > 100 |
| 401 | UNAUTHORIZED | 未认证 | 缺少 Authorization 头 |
| 401 | INVALID_TOKEN | Token 无效 | Token 格式错误或被篡改 |
| 401 | TOKEN_EXPIRED | Token 过期 | accessToken 超过有效期 |
| 401 | INVALID_CREDENTIALS | 凭证错误 | 邮箱或密码错误 |
| 403 | FORBIDDEN | 无权限 | 用户无权限访问该资源 |
| 403 | INSUFFICIENT_PERMISSIONS | 权限不足 | 需要管理员权限，但用户是普通用户 |
| 404 | RESOURCE_NOT_FOUND | 资源不存在 | 用户、订单、产品等不存在 |
| 409 | EMAIL_ALREADY_EXISTS | 邮箱已存在 | 注册时邮箱重复 |
| 409 | CONFLICT | 资源冲突 | 并发更新冲突 |
| 413 | PAYLOAD_TOO_LARGE | 请求体过大 | 请求体超过 10MB |
| 415 | UNSUPPORTED_MEDIA_TYPE | 不支持的媒体类型 | Content-Type 不是 application/json |
| 429 | RATE_LIMIT_EXCEEDED | 请求频率超限 | 超过 API 调用频率限制 |
| 500 | INTERNAL_ERROR | 服务器内部错误 | 未预期的服务器错误 |
| 503 | SERVICE_UNAVAILABLE | 服务不可用 | 数据库连接失败、第三方服务不可用 |

### 错误响应格式

```json
{
  "error": {
    "code": "ERROR_CODE",
    "message": "人类可读的错误消息",
    "details": [
      {
        "field": "字段名",
        "message": "具体错误描述",
        "code": "子错误代码（可选）"
      }
    ]
  },
  "meta": {
    "requestId": "请求追踪 ID"
  }
}
```

### 错误处理实现

```typescript
// 自定义异常类
export class AppError extends Error {
  constructor(
    public readonly statusCode: number,
    public readonly errorCode: string,
    message: string,
    public readonly details: ErrorDetail[] = []
  ) {
    super(message);
    this.name = 'AppError';
  }
}

class ErrorDetail {
  constructor(
    public readonly field: string,
    public readonly message: string,
    public readonly code?: string
  ) {}
}

// 使用示例
throw new AppError(
  404,
  'RESOURCE_NOT_FOUND',
  '用户不存在',
  [{ field: 'id', message: 'ID 为 xxx 的用户不存在' }]
);

// 全局错误处理中间件
app.use((err: Error, req: Request, res: Response, next: NextFunction) => {
  if (err instanceof AppError) {
    return res.status(err.statusCode).json({
      error: {
        code: err.errorCode,
        message: err.message,
        details: err.details
      },
      meta: {
        requestId: req.id
      }
    });
  }
  
  // 未预期的错误
  logger.error('Unhandled error', { error: err, requestId: req.id });
  return res.status(500).json({
    error: {
      code: 'INTERNAL_ERROR',
      message: '服务器内部错误，请稍后重试。如问题持续存在，请联系技术支持。',
      details: []
    },
    meta: {
      requestId: req.id
    }
  });
});
```

## 认证授权说明模板

### 认证方案

#### JWT 认证流程

```
1. 用户注册
   POST /api/v1/auth/register
   → 返回用户信息

2. 用户登录
   POST /api/v1/auth/login
   → 返回 accessToken（15 分钟有效） + refreshToken（7 天有效）

3. 访问受保护资源
   Authorization: Bearer {accessToken}
   → 验证 Token 签名和有效期
   → 解析用户 ID 和角色
   → 检查权限

4. Token 刷新
   POST /api/v1/auth/refresh
   Body: { "refreshToken": "..." }
   → 返回新的 accessToken + refreshToken

5. 登出
   POST /api/v1/auth/logout
   Authorization: Bearer {accessToken}
   → 将 refreshToken 加入黑名单
```

#### Token 结构

```json
// Access Token Payload
{
  "sub": "a0000000-0000-0000-0000-000000000001",  // 用户 ID
  "role": "user",                                    // 用户角色
  "type": "access",                                  // Token 类型
  "iat": 1755993600,                                 // 签发时间
  "exp": 1755994500,                                 // 过期时间（15 分钟）
  "jti": "unique-token-id"                           // Token 唯一 ID
}

// Refresh Token Payload
{
  "sub": "a0000000-0000-0000-0000-000000000001",  // 用户 ID
  "type": "refresh",                                 // Token 类型
  "iat": 1755993600,                                 // 签发时间
  "exp": 1756598400,                                 // 过期时间（7 天）
  "jti": "unique-token-id"                           // Token 唯一 ID
}
```

### 授权方案

#### 角色定义

| 角色 | 权限 | 说明 |
|------|------|------|
| admin | 所有权限 | 系统管理员，可以管理所有资源 |
| merchant | 商家权限 | 管理自己的商品、订单、店铺 |
| user | 基础权限 | 管理自己的个人信息和订单 |

#### 权限矩阵

| 端点 | 操作 | admin | merchant | user | public |
|------|------|-------|----------|------|--------|
| /api/v1/auth/register | 注册 | - | - | - | Yes |
| /api/v1/auth/login | 登录 | - | - | - | Yes |
| /api/v1/auth/refresh | Token 刷新 | Yes | Yes | Yes | - |
| /api/v1/auth/logout | 登出 | Yes | Yes | Yes | - |
| GET /api/v1/users | 用户列表 | Yes | - | - | - |
| GET /api/v1/users/:id | 用户详情 | Yes | 自己的 | 自己的 | - |
| PATCH /api/v1/users/:id | 更新用户 | Yes | 自己的 | 自己的 | - |
| DELETE /api/v1/users/:id | 删除用户 | Yes | - | - | - |
| POST /api/v1/products | 创建商品 | Yes | Yes | - | - |
| GET /api/v1/products | 商品列表 | Yes | Yes | Yes | Yes |
| GET /api/v1/products/:id | 商品详情 | Yes | Yes | Yes | Yes |
| PATCH /api/v1/products/:id | 更新商品 | Yes | 自己的 | - | - |
| DELETE /api/v1/products/:id | 删除商品 | Yes | 自己的 | - | - |
| POST /api/v1/orders | 创建订单 | Yes | Yes | Yes | - |
| GET /api/v1/orders | 订单列表 | Yes | 自己的 | 自己的 | - |
| GET /api/v1/orders/:id | 订单详情 | Yes | 自己的 | 自己的 | - |

#### 权限中间件实现

```typescript
// 权限中间件
function requireRole(...roles: string[]) {
  return (req: Request, res: Response, next: NextFunction) => {
    const user = req.user;
    if (!user) {
      throw new AppError(401, 'UNAUTHORIZED', '请先登录');
    }
    if (!roles.includes(user.role)) {
      throw new AppError(403, 'INSUFFICIENT_PERMISSIONS', '您没有权限执行此操作');
    }
    next();
  };
}

// 资源所有权中间件
function requireOwnership(
  resourceService: any,
  paramName: string = 'id',
  ownerField: string = 'userId'
) {
  return async (req: Request, res: Response, next: NextFunction) => {
    const resourceId = req.params[paramName];
    const resource = await resourceService.findById(resourceId);
    
    if (!resource) {
      throw new AppError(404, 'RESOURCE_NOT_FOUND', '资源不存在');
    }
    
    // admin 可以访问所有资源
    if (req.user.role === 'admin') {
      return next();
    }
    
    // 普通用户只能访问自己的资源
    if (resource[ownerField] !== req.user.id) {
      throw new AppError(403, 'INSUFFICIENT_PERMISSIONS', '您没有权限访问此资源');
    }
    
    next();
  };
}

// 使用示例
router.get(
  '/api/v1/users/:id',
  authenticate,
  requireOwnership(userService, 'id', 'id'),
  userController.getUser
);

router.patch(
  '/api/v1/products/:id',
  authenticate,
  requireRole('admin', 'merchant'),
  requireOwnership(productService, 'id', 'merchantId'),
  productController.updateProduct
);
```

---

**本模板必须完整填写。不要使用占位符、省略号或 TODO 标记。**