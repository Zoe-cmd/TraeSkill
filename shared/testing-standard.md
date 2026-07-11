# Testing Standard - 统一测试规范

## 概述

本文档定义了所有 AI Agent 在编写和执行测试时必须遵守的统一测试规范。测试是质量保证的核心环节，确保代码的正确性和稳定性。

## 适用范围

- 本规范适用于所有类型的测试：单元测试、集成测试、端到端测试、性能测试、安全测试
- 所有 QA Engineer 和编写测试的 Engineer 必须遵守本规范

## 语言规范

本规范文档及所有相关文档、代码注释、提交信息均使用简体中文编写。测试用例描述使用中文，测试函数名使用英文。技术术语（如 E2E、Mock、Stub、Spy、Jest、pytest、JUnit、CI/CD 等）保留英文原文。

## 测试原则

| 原则 | 说明 |
|------|------|
| 测试金字塔 | 单元测试 > 集成测试 > E2E 测试 |
| 独立性 | 每个测试应独立运行，不依赖其他测试 |
| 可重复 | 测试结果应稳定可重复 |
| 快速 | 测试应快速执行，CI 中不超过 10 分钟 |
| 可读性 | 测试代码应清晰表达测试意图 |
| 覆盖率 | 关键路径必须有测试覆盖 |
| 边界测试 | 测试边界条件和异常情况 |

## 测试金字塔

```
        ┌──────┐
        │ E2E  │  ← 少量，验证关键用户流程
        │ 10%  │
       ┌┴──────┴┐
       │Integration│ ← 中等，验证模块间交互
       │   30%    │
      ┌┴──────────┴┐
      │    Unit     │ ← 大量，验证单个函数/方法
      │    60%      │
      └─────────────┘
```

## 测试类型

### 单元测试

| 属性 | 说明 |
|------|------|
| 测试范围 | 单个函数/方法/类 |
| 执行速度 | < 1ms 每个测试 |
| 依赖 | 无外部依赖（Mock 所有依赖） |
| 覆盖率目标 | >= 80% |
| 编写者 | Backend/Frontend/AI Engineer |

### 集成测试

| 属性 | 说明 |
|------|------|
| 测试范围 | 多个模块交互 |
| 执行速度 | < 100ms 每个测试 |
| 依赖 | 数据库、API、消息队列 |
| 覆盖率目标 | 关键路径 100% |
| 编写者 | QA Engineer |

### 端到端测试

| 属性 | 说明 |
|------|------|
| 测试范围 | 完整用户流程 |
| 执行速度 | < 1s 每个测试 |
| 依赖 | 完整系统 |
| 覆盖率目标 | 核心用户流程 100% |
| 编写者 | QA Engineer |

### 性能测试

| 属性 | 说明 |
|------|------|
| 测试范围 | 系统性能指标 |
| 执行速度 | 分钟级 |
| 依赖 | 生产环境或等价环境 |
| 目标 | 满足非功能需求 |
| 编写者 | QA Engineer |

### 安全测试

| 属性 | 说明 |
|------|------|
| 测试范围 | 系统安全 |
| 执行速度 | 分钟级 |
| 依赖 | 测试环境 |
| 目标 | 无 Critical/High 漏洞 |
| 编写者 | Security Engineer |

## 测试命名规范

### 测试文件命名

```
{source_file}.test.{ext}
{source_file}.spec.{ext}

user.service.test.ts
user.service.spec.ts
test_user_service.py
```

### 测试函数命名

```
describe('{Module}', () => {
  describe('{Method}', () => {
    it('should {expected behavior} when {condition}', () => {
      // 测试代码
    });
  });
});
```

示例：
```typescript
describe('UserService', () => {
  describe('getUserById', () => {
    it('should return user when user exists', () => {});
    it('should throw NotFoundError when user does not exist', () => {});
    it('should throw ValidationError when id is empty', () => {});
  });
  
  describe('createUser', () => {
    it('should create user with valid data', () => {});
    it('should throw ConflictError when email already exists', () => {});
    it('should hash password before saving', () => {});
  });
});
```

## 测试结构

### AAA 模式

```
Arrange   — 准备测试数据和环境
Act       — 执行被测试的操作
Assert    — 验证结果
```

### 示例

```typescript
describe('UserService', () => {
  describe('createUser', () => {
    it('should create user with valid data', async () => {
      // Arrange
      const dto: CreateUserDto = {
        email: 'test@example.com',
        name: 'Test User',
        password: 'Password123!',
      };
      
      // Act
      const user = await userService.createUser(dto);
      
      // Assert
      expect(user).toBeDefined();
      expect(user.email).toBe(dto.email);
      expect(user.name).toBe(dto.name);
      expect(user.passwordHash).not.toBe(dto.password); // 密码被哈希
    });
  });
});
```

## 测试数据

### 测试数据原则

| 原则 | 说明 |
|------|------|
| 隔离 | 每个测试使用独立的数据 |
| 清理 | 测试后清理数据 |
| 真实 | 测试数据应接近真实数据 |
| 边界 | 包含边界值和异常值 |

### 测试数据工厂

```typescript
// 测试数据工厂
class UserFactory {
  static create(overrides: Partial<CreateUserDto> = {}): CreateUserDto {
    return {
      email: `test-${Date.now()}-${Math.random()}@example.com`,
      name: 'Test User',
      password: 'Password123!',
      ...overrides,
    };
  }
  
  static createMany(count: number): CreateUserDto[] {
    return Array.from({ length: count }, (_, i) => 
      this.create({ email: `user-${i}@example.com` })
    );
  }
}
```

## Mock 规范

### Mock 原则

| 原则 | 说明 |
|------|------|
| 只 Mock 外部依赖 | 不 Mock 被测试的模块 |
| 验证交互 | 验证 Mock 的调用参数和次数 |
| 不 Mock 数据对象 | 使用真实数据对象 |

### Mock 示例

```typescript
describe('UserService', () => {
  let userService: UserService;
  let userRepository: jest.Mocked<UserRepository>;
  
  beforeEach(() => {
    userRepository = {
      findById: jest.fn(),
      findByEmail: jest.fn(),
      save: jest.fn(),
      delete: jest.fn(),
    };
    
    userService = new UserService(userRepository);
  });
  
  describe('getUserById', () => {
    it('should return user when found', async () => {
      // Arrange
      const mockUser = { id: 'user-1', email: 'test@example.com' };
      userRepository.findById.mockResolvedValue(mockUser);
      
      // Act
      const result = await userService.getUserById('user-1');
      
      // Assert
      expect(result).toBe(mockUser);
      expect(userRepository.findById).toHaveBeenCalledWith('user-1');
      expect(userRepository.findById).toHaveBeenCalledTimes(1);
    });
    
    it('should throw NotFoundError when not found', async () => {
      // Arrange
      userRepository.findById.mockResolvedValue(null);
      
      // Act & Assert
      await expect(userService.getUserById('user-1'))
        .rejects.toThrow(NotFoundError);
    });
  });
});
```

## 测试覆盖率

### 覆盖率目标

| 指标 | 目标 |
|------|------|
| 语句覆盖率 | >= 80% |
| 分支覆盖率 | >= 70% |
| 函数覆盖率 | >= 80% |
| 行覆盖率 | >= 80% |

### 覆盖率豁免

以下情况可以豁免覆盖率要求：

- 配置文件
- 类型定义文件
- 简单的 getter/setter
- 框架生成的代码
- 第三方库的适配器

## 测试工具

### 推荐工具

| 语言 | 测试框架 | Mock 库 | 断言库 | 覆盖率 |
|------|----------|---------|--------|--------|
| TypeScript | Jest | Jest | Jest | Jest |
| Python | pytest | pytest-mock | pytest | coverage |
| Java | JUnit 5 | Mockito | AssertJ | JaCoCo |
| Go | testing | testify | testify | go test -cover |

## 测试配置

### Jest 配置示例

```javascript
module.exports = {
  preset: 'ts-jest',
  testEnvironment: 'node',
  roots: ['<rootDir>/src'],
  testMatch: ['**/*.test.ts', '**/*.spec.ts'],
  collectCoverageFrom: [
    'src/**/*.ts',
    '!src/**/*.d.ts',
    '!src/**/*.test.ts',
    '!src/**/*.spec.ts',
  ],
  coverageThreshold: {
    global: {
      branches: 70,
      functions: 80,
      lines: 80,
      statements: 80,
    },
  },
};
```

### pytest 配置示例

```ini
[tool.pytest.ini_options]
testpaths = ["tests"]
python_files = ["test_*.py"]
python_classes = ["Test*"]
python_functions = ["test_*"]
addopts = [
    "--strict-markers",
    "--cov=src",
    "--cov-report=term-missing",
    "--cov-fail-under=80",
]
```

## 测试文档

### 测试计划

参见 `templates/test-template.md`

### 测试报告

```markdown
# Test Report

## 测试摘要

| 指标 | 数值 |
|------|------|
| 测试总数 | 150 |
| 通过 | 145 |
| 失败 | 3 |
| 跳过 | 2 |
| 通过率 | 96.7% |
| 执行时间 | 45s |
| 覆盖率 | 82% |

## 失败测试

### FAIL-001: UserService.createUser - duplicate email
- **文件**: user.service.test.ts:45
- **错误**: Expected ConflictError but got ValidationError
- **原因**: 邮箱重复检查逻辑在验证层而非服务层
- **建议**: 在服务层添加邮箱重复检查
```

## 反模式

| 反模式 | 说明 | 正确做法 |
|--------|------|----------|
| 测试依赖顺序 | 测试之间有依赖关系 | 每个测试独立运行 |
| 不稳定的测试 | 测试结果不稳定 | 确保测试可重复 |
| 测试实现细节 | 测试内部实现而非行为 | 测试行为，不测试实现 |
| 无断言测试 | 测试没有断言 | 每个测试必须有断言 |
| 过度 Mock | Mock 了被测试的模块 | 只 Mock 外部依赖 |
| 忽略边界 | 没有测试边界条件 | 测试边界值和异常情况 |
| 慢速测试 | 测试执行时间过长 | 优化测试，使用 Mock |

## 检查清单

### 测试编写检查

- [ ] 测试文件命名正确
- [ ] 测试函数命名清晰
- [ ] 遵循 AAA 模式
- [ ] Mock 使用正确
- [ ] 测试覆盖正常路径
- [ ] 测试覆盖异常路径
- [ ] 测试覆盖边界条件
- [ ] 测试数据独立
- [ ] 测试后清理数据

### 测试执行检查

- [ ] 所有测试通过
- [ ] 覆盖率达标
- [ ] 无跳过测试
- [ ] 执行时间合理
- [ ] CI 中测试通过

---

**本规范是所有 AI Agent 编写测试的基础标准。每个 QA Engineer 和 Engineer 在编写测试时必须严格遵守本规范。**