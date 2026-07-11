# Coding Standard - 统一编码规范

## 概述

本文档定义了所有 AI Agent 在编写代码时必须遵守的统一编码规范。该规范是技术栈无关的，同时提供各主流语言的参考实现。

## 适用范围

- 本规范适用于所有角色产生的所有代码
- 包括但不限于：后端代码、前端代码、AI 代码、测试代码、脚本、配置文件
- 所有 Agent 在编写代码时必须遵守本规范

## 语言规范

本规范文档及所有相关文档、代码注释、提交信息均使用简体中文编写。技术术语（如 API、REST、SQL、JSON、HTTP、JWT、CI/CD、Git、TypeScript、React 等）保留英文原文。代码标识符（变量名、函数名、类名等）遵循各语言的命名规范，禁止使用中文命名。

## 命名规范

### 通用命名原则

| 原则 | 说明 | 示例 |
|------|------|------|
| 可读性优先 | 名称应自解释，不需要注释说明 | `getUserById` > `gub` |
| 一致性 | 同一概念在不同文件中使用相同名称 | `userId` 不混用 `user_id` |
| 简洁性 | 在保证可读性的前提下尽量简洁 | `user` > `theUserObject` |
| 无缩写 | 不使用不常见的缩写 | `configuration` > `cfg` |
| 无魔法数字 | 不直接使用数字字面量 | `MAX_RETRY_COUNT` > `3` |
| 无前缀 | 不使用匈牙利命名法 | `name` > `strName` |
| 无类型后缀 | 名称中不包含类型信息 | `users` > `userList` |

### 通用禁止项

- 禁止使用拼音命名
- 禁止使用中文命名
- 禁止使用保留字作为标识符
- 禁止使用单字母变量（循环变量 i, j, k 除外）
- 禁止使用双下划线开头（Python 特殊方法除外）
- 禁止使用下划线结尾（特殊情况除外）

### 按类型命名规范

| 类型 | 规范 | 示例 |
|------|------|------|
| 文件 | kebab-case | `user-service.ts`, `user-profile.tsx` |
| 目录 | kebab-case | `user-profile/`, `api-gateway/` |
| 类 | PascalCase | `UserService`, `UserProfile` |
| 接口 | PascalCase（I 前缀可选） | `UserService`, `IUserRepository` |
| 类型 | PascalCase | `UserProfile`, `ApiResponse<T>` |
| 枚举 | PascalCase | `UserStatus`, `OrderState` |
| 枚举值 | UPPER_SNAKE_CASE | `ACTIVE`, `PENDING_APPROVAL` |
| 函数/方法 | camelCase | `getUserById`, `handleSubmit` |
| 变量 | camelCase | `userId`, `totalCount` |
| 常量 | UPPER_SNAKE_CASE | `MAX_RETRY_COUNT`, `API_BASE_URL` |
| 私有属性 | camelCase（_ 前缀可选） | `_internalState`, `privateField` |
| 布尔变量 | is/has/can/should 前缀 | `isActive`, `hasPermission` |
| 事件处理 | handle/on 前缀 | `handleClick`, `onSubmit` |
| 数据库表 | snake_case 复数 | `users`, `order_items` |
| 数据库列 | snake_case 单数 | `user_id`, `created_at` |
| API 端点 | kebab-case 或 snake_case | `/api/user-profiles`, `/api/user_profiles` |
| 环境变量 | UPPER_SNAKE_CASE | `DATABASE_URL`, `JWT_SECRET` |

### 各语言特定命名规范

#### TypeScript/JavaScript

```typescript
// 类型定义
type UserProfile = {
  userId: string;
  displayName: string;
  isActive: boolean;
};

// 接口定义
interface IUserRepository {
  findById(id: string): Promise<User | null>;
  save(user: User): Promise<void>;
}

// 类定义
class UserService {
  private readonly userRepository: IUserRepository;
  
  constructor(userRepository: IUserRepository) {
    this.userRepository = userRepository;
  }
  
  async getUserById(userId: string): Promise<User> {
    const user = await this.userRepository.findById(userId);
    if (!user) {
      throw new NotFoundError(`User not found: ${userId}`);
    }
    return user;
  }
}

// 常量定义
const MAX_RETRY_COUNT = 3;
const DEFAULT_PAGE_SIZE = 20;

// 枚举定义
enum OrderStatus {
  PENDING = 'PENDING',
  CONFIRMED = 'CONFIRMED',
  SHIPPED = 'SHIPPED',
  DELIVERED = 'DELIVERED',
  CANCELLED = 'CANCELLED',
}
```

#### Python

```python
# 类定义
class UserService:
    def __init__(self, user_repository: UserRepository) -> None:
        self._user_repository = user_repository
    
    def get_user_by_id(self, user_id: str) -> User:
        user = self._user_repository.find_by_id(user_id)
        if user is None:
            raise NotFoundError(f"User not found: {user_id}")
        return user

# 常量定义
MAX_RETRY_COUNT = 3
DEFAULT_PAGE_SIZE = 20

# 枚举定义
class OrderStatus(Enum):
    PENDING = "PENDING"
    CONFIRMED = "CONFIRMED"
    SHIPPED = "SHIPPED"
    DELIVERED = "DELIVERED"
    CANCELLED = "CANCELLED"
```

#### Java

```java
// 接口定义
public interface UserRepository {
    Optional<User> findById(String userId);
    User save(User user);
}

// 类定义
public class UserService {
    private final UserRepository userRepository;
    
    public UserService(UserRepository userRepository) {
        this.userRepository = userRepository;
    }
    
    public User getUserById(String userId) {
        return userRepository.findById(userId)
            .orElseThrow(() -> new NotFoundException("User not found: " + userId));
    }
}

// 常量定义
public static final int MAX_RETRY_COUNT = 3;
public static final int DEFAULT_PAGE_SIZE = 20;

// 枚举定义
public enum OrderStatus {
    PENDING,
    CONFIRMED,
    SHIPPED,
    DELIVERED,
    CANCELLED
}
```

## 代码结构规范

### 文件结构

每个代码文件应遵循以下结构：

```
1. 文件头部注释（可选）
2. 导入语句
   - 外部依赖
   - 内部模块
   - 类型导入
3. 常量定义
4. 类型/接口定义
5. 主类/函数定义
6. 辅助函数/工具函数
7. 导出语句（如适用）
```

### TypeScript 文件结构示例

```typescript
/**
 * User Service
 * 
 * 负责用户相关的业务逻辑处理。
 * 
 * @module services/user
 */

// 外部依赖导入
import { Injectable } from '@nestjs/common';
import { Repository } from 'typeorm';

// 内部模块导入
import { User } from '../entities/user.entity';
import { CreateUserDto } from '../dto/create-user.dto';

// 类型导入
import type { UserProfile } from '../types/user.types';

// 常量定义
const MAX_LOGIN_ATTEMPTS = 5;
const TOKEN_EXPIRY_HOURS = 24;

// 接口定义
interface UserSearchParams {
  keyword?: string;
  status?: UserStatus;
  page?: number;
  limit?: number;
}

// 主类定义
@Injectable()
export class UserService {
  constructor(
    private readonly userRepository: Repository<User>,
  ) {}

  async createUser(dto: CreateUserDto): Promise<User> {
    // 实现
  }

  async findUsers(params: UserSearchParams): Promise<[User[], number]> {
    // 实现
  }
}
```

### 函数/方法结构

每个函数/方法应遵循以下结构：

```
1. 函数签名（参数类型、返回类型）
2. 参数验证
3. 核心逻辑
4. 错误处理
5. 返回值
```

### 函数设计原则

| 原则 | 说明 | 标准 |
|------|------|------|
| 单一职责 | 一个函数只做一件事 | 函数名应准确描述其行为 |
| 短小精悍 | 函数体不宜过长 | 不超过 50 行 |
| 参数最少 | 参数数量尽量少 | 不超过 4 个参数 |
| 无副作用 | 函数不应修改外部状态 | 纯函数优先 |
| 明确返回 | 所有路径都有明确的返回值 | 无隐式 undefined 返回 |
| 类型安全 | 参数和返回值有明确的类型 | 不使用 any/unknown |

## 格式化规范

### 通用格式化规则

| 规则 | 设置 |
|------|------|
| 缩进 | 2 空格（TypeScript/JS）或 4 空格（Python） |
| 行宽 | 120 字符 |
| 行尾 | LF |
| 文件结尾 | 空行 |
| 尾随空格 | 删除 |
| 引号 | 单引号（TypeScript/JS），双引号（Python） |
| 分号 | 必须（TypeScript/JS），无（Python） |
| 逗号 | 尾随逗号 |
| 括号 | 花括号不换行 |

### 空行规则

- 导入语句和代码之间：1 空行
- 类/函数之间：1 空行
- 逻辑块之间：1 空行
- 文件结尾：1 空行

### 空格规则

- 运算符两侧：1 空格
- 逗号后：1 空格
- 冒号后：1 空格（对象字面量）
- 函数名和括号之间：无空格
- 注释符号后：1 空格

## 注释规范

### 注释原则

| 原则 | 说明 |
|------|------|
| 解释 Why，不是 What | 注释应解释为什么这样做，而不是重复代码做了什么 |
| 保持同步 | 修改代码时同步更新注释 |
| 简洁明了 | 注释应简洁明了，避免冗长 |
| 英文优先 | 注释使用英文，除非项目明确要求中文 |
| 无过期注释 | 删除不再适用的注释 |

### 注释类型

#### 文件头部注释

```typescript
/**
 * User Service Module
 * 
 * 提供用户管理相关的业务逻辑，包括用户注册、登录、信息管理等。
 * 
 * @module services/user
 * @author Backend Engineer
 * @created 2026-07-11
 * @updated 2026-07-11
 */
```

#### 函数注释（JSDoc）

```typescript
/**
 * 根据用户ID获取用户信息
 * 
 * 从数据库中查询用户信息，如果用户不存在则抛出 NotFoundError。
 * 
 * @param userId - 用户唯一标识符
 * @returns 用户对象
 * @throws {NotFoundError} 当用户不存在时
 * @example
 * const user = await userService.getUserById('user-123');
 * console.log(user.displayName);
 */
async getUserById(userId: string): Promise<User> {
  // 实现
}
```

#### 行内注释

```typescript
// 使用批量插入提高性能，避免逐条插入
// 对于大批量数据，考虑使用 COPY 命令
const results = await this.repository.insert(users);

// 注意：此处的排序逻辑依赖于数据库索引
// 如果索引发生变化，需要同步更新此查询
const users = await this.repository.find({
  order: { createdAt: 'DESC' },
});
```

#### 待办注释

```typescript
// TODO(user-service): 实现用户缓存机制，减少数据库查询
// FIXME(user-service): 修复并发场景下的用户状态更新竞态条件
// HACK(user-service): 临时方案，等待数据库迁移完成后移除
// NOTE(user-service): 此方法被多个服务依赖，修改时需谨慎
```

### 禁止的注释

```typescript
// ❌ 错误：注释重复代码
// 获取用户名称
const userName = getUserName();

// ❌ 错误：注释过期
// 使用旧版 API，已废弃
const result = await oldApi.getUser();

// ❌ 错误：注释不准确
// 返回用户列表（实际返回的是单个用户）
async function getUser(id: string): Promise<User> {
  // ...
}

// ✅ 正确：有用的注释
// 降级策略：当 Redis 不可用时，直接查询数据库
// 避免缓存故障导致整个服务不可用
const user = await cache.get(key) ?? await db.query(sql);
```

## 错误处理规范

### 错误处理原则

| 原则 | 说明 |
|------|------|
| 明确错误类型 | 使用自定义错误类，而非通用 Error |
| 合理错误信息 | 错误信息应包含足够上下文，便于排查 |
| 不吞没错误 | 不要捕获错误后不做任何处理 |
| 不泄露敏感信息 | 错误信息不应包含密码、Token 等敏感信息 |
| 统一错误格式 | 所有 API 返回统一的错误响应格式 |

### 错误类层次结构

```typescript
// 基础错误类
abstract class AppError extends Error {
  abstract readonly code: string;
  abstract readonly statusCode: number;
  
  constructor(message: string) {
    super(message);
    this.name = this.constructor.name;
  }
}

// 业务错误
class NotFoundError extends AppError {
  readonly code = 'NOT_FOUND';
  readonly statusCode = 404;
}

class ValidationError extends AppError {
  readonly code = 'VALIDATION_ERROR';
  readonly statusCode = 400;
}

class UnauthorizedError extends AppError {
  readonly code = 'UNAUTHORIZED';
  readonly statusCode = 401;
}

class ForbiddenError extends AppError {
  readonly code = 'FORBIDDEN';
  readonly statusCode = 403;
}

class ConflictError extends AppError {
  readonly code = 'CONFLICT';
  readonly statusCode = 409;
}

class InternalError extends AppError {
  readonly code = 'INTERNAL_ERROR';
  readonly statusCode = 500;
}
```

### 错误处理模式

```typescript
// Controller 层 — 处理 HTTP 请求，不处理业务错误
async getUser(req: Request, res: Response): Promise<void> {
  try {
    const user = await this.userService.getUserById(req.params.id);
    res.status(200).json({ data: user });
  } catch (error) {
    if (error instanceof NotFoundError) {
      res.status(404).json({ error: { code: error.code, message: error.message } });
    } else {
      // 未知错误，记录日志后返回通用错误
      logger.error('Failed to get user', { userId: req.params.id, error });
      res.status(500).json({ error: { code: 'INTERNAL_ERROR', message: 'Internal server error' } });
    }
  }
}

// Service 层 — 处理业务逻辑，抛出业务错误
async getUserById(userId: string): Promise<User> {
  const user = await this.userRepository.findById(userId);
  if (!user) {
    throw new NotFoundError(`User not found: ${userId}`);
  }
  return user;
}

// Repository 层 — 处理数据访问，抛出数据访问错误
async findById(id: string): Promise<User | null> {
  try {
    return await this.db.query('SELECT * FROM users WHERE id = $1', [id]);
  } catch (error) {
    throw new DataAccessError('Failed to query user', { cause: error });
  }
}
```

## 日志规范

### 日志级别

| 级别 | 使用场景 | 示例 |
|------|----------|------|
| ERROR | 系统错误，需要立即关注 | 数据库连接失败、第三方服务不可用 |
| WARN | 警告信息，可能需要关注 | 降级策略触发、重试次数过多 |
| INFO | 重要业务流程信息 | 用户登录、订单创建、服务启动 |
| DEBUG | 调试信息，开发环境使用 | 函数调用参数、中间计算结果 |
| TRACE | 最详细的追踪信息 | 每个步骤的执行时间和结果 |

### 日志格式

```json
{
  "timestamp": "2026-07-11T12:00:00.000Z",
  "level": "INFO",
  "service": "user-service",
  "traceId": "abc-123-def-456",
  "message": "User created successfully",
  "context": {
    "userId": "user-123",
    "action": "create_user",
    "duration": 45
  }
}
```

### 日志内容规则

- 禁止记录敏感信息（密码、Token、身份证号、银行卡号）
- 必须包含 traceId 用于链路追踪
- 必须包含服务名称
- 必须包含时间戳
- 错误日志必须包含堆栈跟踪
- 关键业务操作必须记录 INFO 日志

## 类型安全规范

### TypeScript 类型规范

- 禁止使用 `any` 类型（除非有充分理由并添加注释）
- 禁止使用 `as` 类型断言（除非有充分理由并添加注释）
- 使用 `unknown` 替代 `any`，使用前进行类型守卫
- 所有函数必须有明确的参数类型和返回类型
- 使用 `strict` 模式
- 启用 `noImplicitAny`
- 启用 `strictNullChecks`

### Python 类型规范

- 所有函数必须有类型注解
- 使用 `mypy` 进行静态类型检查
- 使用 `Protocol` 定义接口
- 使用 `TypedDict` 定义字典类型

## 代码组织规范

### 模块化原则

| 原则 | 说明 |
|------|------|
| 单一职责 | 每个模块只负责一个功能领域 |
| 高内聚 | 相关功能放在同一模块中 |
| 低耦合 | 模块间通过接口通信，减少直接依赖 |
| 依赖倒置 | 高层模块不依赖低层模块，都依赖抽象 |
| 接口隔离 | 接口应该小而专注，不强迫实现不需要的方法 |

### 目录结构规范

```
src/
├── modules/           # 业务模块
│   ├── users/         # 用户模块
│   │   ├── controllers/
│   │   ├── services/
│   │   ├── repositories/
│   │   ├── entities/
│   │   ├── dto/
│   │   ├── types/
│   │   └── tests/
│   ├── orders/        # 订单模块
│   └── products/      # 产品模块
├── common/            # 公共模块
│   ├── decorators/
│   ├── filters/
│   ├── guards/
│   ├── interceptors/
│   ├── pipes/
│   └── utils/
├── config/            # 配置文件
├── database/          # 数据库相关
│   ├── migrations/
│   └── seeds/
└── main.ts            # 入口文件
```

## 依赖管理规范

### 依赖原则

- 最小依赖：只引入必要的依赖
- 版本锁定：使用 lock 文件锁定依赖版本
- 定期更新：定期检查并更新依赖
- 安全审计：定期对依赖进行安全审计
- 许可证检查：确保依赖的许可证兼容

### 依赖方向

```
Controller → Service → Repository → Database
     ↓           ↓
   DTOs       Entities
```

- Controller 依赖 Service 和 DTOs
- Service 依赖 Repository 和 Entities
- Repository 依赖 Database
- 不允许反向依赖（Repository 不应依赖 Service）

## 性能规范

### 通用性能规则

- 避免 N+1 查询问题
- 使用批量操作代替循环操作
- 合理使用缓存
- 避免不必要的数据库查询
- 使用连接池管理数据库连接
- 异步操作优先（不阻塞主线程）

### 前端性能规则

- 避免不必要的重渲染
- 使用虚拟列表处理大数据列表
- 图片懒加载
- 代码分割
- Tree Shaking
- 合理的 Bundle 大小

## 安全规范

### 代码安全规则

- 所有用户输入必须验证和过滤
- 禁止 SQL 拼接，使用参数化查询
- 禁止在代码中硬编码密码、密钥、Token
- 敏感数据必须加密存储
- 使用 HTTPS 传输敏感数据
- 实施最小权限原则

## 检查清单

### 代码提交前检查

- [ ] 命名符合规范
- [ ] 代码格式化正确
- [ ] 注释完整且准确
- [ ] 错误处理完整
- [ ] 日志记录合理
- [ ] 类型标注完整
- [ ] 无硬编码敏感信息
- [ ] 无 console.log（生产代码）
- [ ] 无未使用的导入
- [ ] 无注释掉的代码
- [ ] 单元测试通过
- [ ] Lint 检查通过
- [ ] 类型检查通过

## 工具配置

### ESLint 配置（TypeScript）

```json
{
  "extends": [
    "eslint:recommended",
    "plugin:@typescript-eslint/recommended",
    "plugin:@typescript-eslint/strict"
  ],
  "rules": {
    "@typescript-eslint/no-explicit-any": "error",
    "@typescript-eslint/explicit-function-return-type": "error",
    "@typescript-eslint/no-unused-vars": ["error", { "argsIgnorePattern": "^_" }],
    "no-console": "error",
    "no-debugger": "error"
  }
}
```

### Prettier 配置

```json
{
  "semi": true,
  "trailingComma": "all",
  "singleQuote": true,
  "printWidth": 120,
  "tabWidth": 2,
  "endOfLine": "lf"
}
```

### Python 配置

```ini
[tool.ruff]
line-length = 120
target-version = "py311"

[tool.ruff.lint]
select = ["E", "F", "I", "N", "W"]

[tool.mypy]
strict = true
python_version = "3.11"
```

## 反模式

### 常见反模式

| 反模式 | 说明 | 正确做法 |
|--------|------|----------|
| God Class | 一个类承担过多职责 | 按职责拆分为多个类 |
| Magic Number | 代码中直接使用数字字面量 | 使用命名常量 |
| Copy-Paste | 复制粘贴代码 | 提取公共函数/模块 |
| Premature Optimization | 过早优化 | 先保证正确性，再优化性能 |
| Cargo Cult | 不理解原理就照搬代码 | 理解后再使用 |
| Golden Hammer | 对所有问题使用同一方案 | 根据问题选择合适方案 |
| Spaghetti Code | 代码结构混乱，难以维护 | 遵循模块化原则 |
| Hard Coding | 硬编码配置值 | 使用配置文件或环境变量 |

## 优秀实践

### 代码编写最佳实践

1. **先写测试，再写代码（TDD）** — 测试驱动开发确保代码质量
2. **小步提交** — 每次提交只包含一个逻辑变更
3. **代码审查** — 所有代码必须经过审查
4. **持续重构** — 不断改进代码质量
5. **文档同步** — 代码变更时同步更新文档
6. **自动化检查** — 使用 CI 自动执行 Lint、测试、类型检查

---

**本规范是所有 AI Agent 编写代码的基础标准。每个 Agent 在编写代码时必须严格遵守本规范，Code Reviewer 在审查时以本规范为准。**