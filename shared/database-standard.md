# Database Standard - 统一数据库规范

## 概述

本文档定义了所有 AI Agent 在设计和操作数据库时必须遵守的统一数据库规范。涵盖数据库设计、命名、索引、查询、迁移、安全等。

## 适用范围

- 本规范适用于所有数据库相关的设计和操作
- 包括但不限于：关系型数据库（PostgreSQL、MySQL）、NoSQL 数据库（MongoDB、Redis）
- 所有 Database Engineer 和 Backend Engineer 在操作数据库时必须遵守本规范

## 语言规范

本规范文档及所有相关文档、代码注释、提交信息均使用简体中文编写。技术术语（如 SQL、PostgreSQL、MySQL、Redis、MongoDB、JSONB、UUID、ORM、CRUD 等）保留英文原文。代码标识符（变量名、函数名、类名等）遵循各语言的命名规范，禁止使用中文命名。

## 数据库设计原则

| 原则 | 说明 |
|------|------|
| 规范化 | 遵循 3NF（第三范式），减少数据冗余 |
| 适当反规范化 | 在性能瓶颈处适当反规范化 |
| 数据完整性 | 使用约束保证数据完整性 |
| 可扩展性 | 设计时考虑未来扩展 |
| 安全性 | 最小权限原则，敏感数据加密 |
| 性能优先 | 索引设计先行，查询优化 |
| 可维护性 | 清晰的命名和注释，便于维护 |

## 命名规范

### 数据库命名

| 对象 | 规范 | 示例 |
|------|------|------|
| 数据库名 | snake_case 单数 | `user_service`, `order_service` |
| Schema | snake_case 单数 | `public`, `audit`, `reporting` |
| 表名 | snake_case 复数 | `users`, `order_items`, `product_categories` |
| 列名 | snake_case 单数 | `user_id`, `created_at`, `is_active` |
| 主键 | `id` | `id` |
| 外键 | `{referenced_table_singular}_id` | `user_id`, `order_id` |
| 索引 | `idx_{table}_{column}` | `idx_users_email`, `idx_orders_user_id` |
| 唯一索引 | `uq_{table}_{column}` | `uq_users_email`, `uq_orders_order_number` |
| 外键约束 | `fk_{table}_{referenced_table}` | `fk_orders_users`, `fk_order_items_orders` |
| 检查约束 | `ck_{table}_{column}` | `ck_users_age`, `ck_orders_status` |
| 序列 | `{table}_{column}_seq` | `users_id_seq`, `orders_order_number_seq` |
| 视图 | `vw_{name}` | `vw_user_summary`, `vw_order_statistics` |
| 物化视图 | `mv_{name}` | `mv_daily_sales_report` |
| 函数 | `fn_{name}` | `fn_calculate_order_total`, `fn_get_user_orders` |
| 存储过程 | `sp_{name}` | `sp_process_order`, `sp_cleanup_expired_sessions` |
| 触发器 | `trg_{table}_{event}` | `trg_users_before_insert`, `trg_orders_after_update` |

### 命名禁止项

- 禁止使用关键字作为名称
- 禁止使用拼音命名
- 禁止使用中文命名
- 禁止使用特殊字符（$、#、@ 等）
- 禁止使用空格
- 禁止使用过长的名称（超过 63 字符）
- 禁止使用无意义的名称（a、b、c、temp、data）

## 表设计规范

### 表结构模板

```sql
-- ============================================
-- Table: users
-- Description: 用户表，存储用户基本信息
-- ============================================
CREATE TABLE users (
    -- 主键
    id              UUID            NOT NULL DEFAULT gen_random_uuid(),
    
    -- 业务字段
    email           VARCHAR(255)    NOT NULL,
    display_name    VARCHAR(100)    NOT NULL,
    password_hash   VARCHAR(255)    NOT NULL,
    role            VARCHAR(50)     NOT NULL DEFAULT 'user',
    status          VARCHAR(20)     NOT NULL DEFAULT 'active',
    avatar_url      TEXT,
    phone           VARCHAR(20),
    
    -- 审计字段
    created_at      TIMESTAMPTZ     NOT NULL DEFAULT NOW(),
    updated_at      TIMESTAMPTZ     NOT NULL DEFAULT NOW(),
    deleted_at      TIMESTAMPTZ,
    
    -- 主键约束
    CONSTRAINT pk_users PRIMARY KEY (id),
    
    -- 唯一约束
    CONSTRAINT uq_users_email UNIQUE (email),
    
    -- 检查约束
    CONSTRAINT ck_users_role CHECK (role IN ('admin', 'user', 'moderator')),
    CONSTRAINT ck_users_status CHECK (status IN ('active', 'inactive', 'suspended', 'deleted'))
);

-- 索引
CREATE INDEX idx_users_email ON users (email) WHERE deleted_at IS NULL;
CREATE INDEX idx_users_status ON users (status) WHERE deleted_at IS NULL;
CREATE INDEX idx_users_created_at ON users (created_at);

-- 注释
COMMENT ON TABLE users IS '用户表，存储用户基本信息';
COMMENT ON COLUMN users.id IS '用户唯一标识符';
COMMENT ON COLUMN users.email IS '用户邮箱，用于登录和通知';
COMMENT ON COLUMN users.display_name IS '用户显示名称';
COMMENT ON COLUMN users.password_hash IS '密码哈希值，使用 bcrypt 加密';
COMMENT ON COLUMN users.role IS '用户角色：admin-管理员, user-普通用户, moderator-版主';
COMMENT ON COLUMN users.status IS '用户状态：active-活跃, inactive-未激活, suspended-已停用, deleted-已删除';
COMMENT ON COLUMN users.avatar_url IS '用户头像地址';
COMMENT ON COLUMN users.phone IS '用户手机号';
COMMENT ON COLUMN users.created_at IS '创建时间';
COMMENT ON COLUMN users.updated_at IS '最后更新时间';
COMMENT ON COLUMN users.deleted_at IS '软删除时间，NULL 表示未删除';
```

### 表设计规则

| 规则 | 说明 |
|------|------|
| 每个表必须有主键 | 主键类型推荐 UUID 或 BIGINT |
| 每个表必须有审计字段 | created_at, updated_at, deleted_at |
| 每个表必须有注释 | 表和每个列都必须有 COMMENT |
| 使用软删除 | 使用 deleted_at 字段，不物理删除数据 |
| 使用合适的字段类型 | 日期用 TIMESTAMPTZ，布尔用 BOOLEAN，金额用 NUMERIC |
| 字段必须有默认值 | 除非业务要求必须提供，否则应有默认值 |
| 避免 NULL | 使用 NOT NULL 约束，除非明确需要 NULL |

### 字段类型选择

| 数据类型 | 使用场景 | 示例 |
|----------|----------|------|
| UUID | 主键、分布式系统 | `id UUID` |
| BIGINT | 自增主键、计数器 | `id BIGSERIAL` |
| VARCHAR(n) | 有限长度的字符串 | `email VARCHAR(255)` |
| TEXT | 无限长度的文本 | `description TEXT` |
| BOOLEAN | 布尔值 | `is_active BOOLEAN` |
| INTEGER | 整数 | `age INTEGER` |
| BIGINT | 大整数 | `view_count BIGINT` |
| NUMERIC(p,s) | 精确小数（金额） | `price NUMERIC(10,2)` |
| TIMESTAMPTZ | 时间戳（带时区） | `created_at TIMESTAMPTZ` |
| DATE | 日期 | `birth_date DATE` |
| JSONB | JSON 数据 | `metadata JSONB` |
| ENUM | 枚举值 | `status user_status` |
| UUID[] | UUID 数组 | `tag_ids UUID[]` |
| BYTEA | 二进制数据 | `file_data BYTEA` |

## 索引规范

### 索引设计原则

| 原则 | 说明 |
|------|------|
| 选择性高的列 | 索引选择性（distinct/total）> 0.1 的列适合建索引 |
| 外键列 | 所有外键列必须建索引 |
| 查询条件列 | WHERE 子句中经常使用的列 |
| 排序列 | ORDER BY 子句中经常使用的列 |
| 联合查询列 | JOIN 条件中使用的列 |
| 避免过多索引 | 索引不是越多越好，每个索引都有维护成本 |
| 部分索引 | 对于大表，使用 WHERE 条件创建部分索引 |
| 覆盖索引 | 查询频繁的列组合，创建覆盖索引 |

### 索引类型

| 类型 | 使用场景 | 示例 |
|------|----------|------|
| B-Tree | 等值查询、范围查询 | 默认索引类型 |
| Hash | 等值查询 | 不常用 |
| GIN | 全文搜索、数组查询 | JSONB 字段 |
| GiST | 空间数据、全文搜索 | 地理位置 |
| BRIN | 顺序数据、大表 | 时间序列数据 |

### 索引示例

```sql
-- 单列索引
CREATE INDEX idx_users_email ON users (email);

-- 复合索引
CREATE INDEX idx_orders_user_id_status ON orders (user_id, status);

-- 部分索引
CREATE INDEX idx_users_active_email ON users (email) WHERE status = 'active';

-- 唯一索引
CREATE UNIQUE INDEX uq_users_email ON users (email) WHERE deleted_at IS NULL;

-- 全文搜索索引
CREATE INDEX idx_products_name_gin ON products USING GIN (to_tsvector('english', name));

-- 覆盖索引
CREATE INDEX idx_users_id_email_name ON users (id) INCLUDE (email, display_name);
```

### 索引命名

```sql
-- 普通索引
CREATE INDEX idx_{table}_{column} ON {table} ({column});

-- 复合索引
CREATE INDEX idx_{table}_{col1}_{col2} ON {table} ({col1}, {col2});

-- 唯一索引
CREATE UNIQUE INDEX uq_{table}_{column} ON {table} ({column});

-- 部分索引
CREATE INDEX idx_{table}_{column}_{condition} ON {table} ({column}) WHERE {condition};
```

## 查询规范

### SQL 编写规范

| 规则 | 说明 |
|------|------|
| 关键字大写 | SELECT, FROM, WHERE, JOIN 等关键字大写 |
| 标识符小写 | 表名、列名小写 |
| 别名使用 AS | 明确使用 AS 关键字 |
| JOIN 明确类型 | 使用 INNER JOIN, LEFT JOIN 而非 JOIN |
| 避免 SELECT * | 明确列出需要的列 |
| 参数化查询 | 使用参数化查询，禁止拼接 SQL |
| LIMIT 必须 | 所有查询必须有 LIMIT |
| 事务明确 | 显式管理事务 |

### 查询示例

```sql
-- ✅ 正确：完整的查询
SELECT
    u.id,
    u.email,
    u.display_name,
    u.role,
    u.status,
    u.created_at
FROM users AS u
INNER JOIN user_profiles AS up
    ON u.id = up.user_id
WHERE
    u.status = 'active'
    AND u.created_at >= $1
ORDER BY u.created_at DESC
LIMIT $2
OFFSET $3;

-- ❌ 错误：SELECT *，无 LIMIT，无参数化
SELECT * FROM users WHERE status = 'active' ORDER BY created_at DESC;
```

### 查询性能规则

- 避免 N+1 查询，使用 JOIN 或批量查询
- 避免在 WHERE 子句中使用函数（如 `WHERE LOWER(email) = ?`）
- 避免在 WHERE 子句中对列进行运算（如 `WHERE price * 1.1 > 100`）
- 使用 EXISTS 替代 IN 子查询（当子查询结果集较大时）
- 使用 UNION ALL 替代 UNION（当不需要去重时）
- 大表查询使用游标或分页

### 事务规范

```sql
-- 事务模板
BEGIN;

-- 业务操作
UPDATE users SET status = 'suspended' WHERE id = $1;
INSERT INTO audit_logs (user_id, action, created_at) VALUES ($1, 'suspend_user', NOW());

-- 检查业务规则
-- 如果不符合预期，回滚
-- ROLLBACK;

COMMIT;
```

### 事务规则

- 事务尽可能短小
- 不在事务中执行长时间操作（如 HTTP 请求）
- 不在事务中执行可能失败的外部操作
- 使用适当的隔离级别
- 处理死锁和重试

## 数据库迁移规范

### 迁移原则

| 原则 | 说明 |
|------|------|
| 版本化 | 每个迁移文件有唯一版本号 |
| 可回滚 | 每个迁移有对应的回滚脚本 |
| 幂等 | 迁移可重复执行（使用 IF NOT EXISTS） |
| 原子性 | 每个迁移在一个事务中 |
| 向后兼容 | 迁移不破坏现有功能 |
| 测试先行 | 迁移先在测试环境验证 |

### 迁移文件命名

```
{timestamp}_{description}.sql
20260711120000_create_users_table.sql
20260711120100_add_email_index.sql
20260711120200_alter_users_add_phone.sql
```

### 迁移模板

```sql
-- ============================================
-- Migration: 20260711120000_create_users_table
-- Description: 创建用户表
-- Author: Database Engineer
-- Date: 2026-07-11
-- ============================================

-- UP Migration
BEGIN;

CREATE TABLE IF NOT EXISTS users (
    id              UUID            NOT NULL DEFAULT gen_random_uuid(),
    email           VARCHAR(255)    NOT NULL,
    display_name    VARCHAR(100)    NOT NULL,
    password_hash   VARCHAR(255)    NOT NULL,
    role            VARCHAR(50)     NOT NULL DEFAULT 'user',
    status          VARCHAR(20)     NOT NULL DEFAULT 'active',
    created_at      TIMESTAMPTZ     NOT NULL DEFAULT NOW(),
    updated_at      TIMESTAMPTZ     NOT NULL DEFAULT NOW(),
    deleted_at      TIMESTAMPTZ,
    CONSTRAINT pk_users PRIMARY KEY (id),
    CONSTRAINT uq_users_email UNIQUE (email),
    CONSTRAINT ck_users_role CHECK (role IN ('admin', 'user', 'moderator')),
    CONSTRAINT ck_users_status CHECK (status IN ('active', 'inactive', 'suspended', 'deleted'))
);

CREATE INDEX IF NOT EXISTS idx_users_email ON users (email) WHERE deleted_at IS NULL;
CREATE INDEX IF NOT EXISTS idx_users_status ON users (status) WHERE deleted_at IS NULL;

COMMIT;

-- DOWN Migration
-- BEGIN;
-- DROP TABLE IF EXISTS users;
-- COMMIT;
```

## 数据安全规范

### 敏感数据处理

| 数据类型 | 处理方式 | 说明 |
|----------|----------|------|
| 密码 | bcrypt 哈希 | 不可逆加密 |
| 身份证号 | AES-256 加密 | 可逆加密，按需解密 |
| 银行卡号 | AES-256 加密 | 可逆加密，按需解密 |
| 手机号 | AES-256 加密 | 可逆加密，按需解密 |
| 邮箱 | 明文或加密 | 取决于业务需求 |
| Token/密钥 | SHA-256 哈希 | 不可逆加密 |
| 日志中的敏感信息 | 脱敏 | 掩码处理 |

### 数据脱敏规则

```sql
-- 手机号脱敏：138****1234
-- 邮箱脱敏：j***@example.com
-- 身份证脱敏：3201**********1234
-- 银行卡脱敏：6222****1234
```

### 访问控制

| 级别 | 权限 | 说明 |
|------|------|------|
| 应用账号 | SELECT, INSERT, UPDATE, DELETE | 日常操作 |
| 迁移账号 | CREATE, ALTER, DROP | 数据库变更 |
| 只读账号 | SELECT | 报表查询 |
| 审计账号 | SELECT（审计表） | 审计查询 |

## 备份与恢复

### 备份策略

| 类型 | 频率 | 保留时间 |
|------|------|----------|
| 全量备份 | 每天 | 30 天 |
| 增量备份 | 每小时 | 7 天 |
| WAL 归档 | 持续 | 7 天 |

### 恢复时间目标

| 指标 | 目标 |
|------|------|
| RPO（恢复点目标） | < 1 小时 |
| RTO（恢复时间目标） | < 4 小时 |

## 性能规范

### 查询性能标准

| 查询类型 | 目标 | 最大 |
|----------|------|------|
| 主键查询 | < 1ms | 5ms |
| 索引查询 | < 10ms | 50ms |
| 全表扫描 | 禁止 | 禁止 |
| 复杂 JOIN | < 100ms | 500ms |
| 聚合查询 | < 500ms | 2s |

### 连接池配置

| 参数 | 推荐值 | 说明 |
|------|--------|------|
| max_connections | 20-50 | 最大连接数 |
| min_connections | 2-5 | 最小连接数 |
| idle_timeout | 60000ms | 空闲超时 |
| connection_timeout | 30000ms | 连接超时 |
| max_lifetime | 1800000ms | 连接最大生命周期 |

## 监控规范

### 监控指标

| 指标 | 说明 | 告警阈值 |
|------|------|----------|
| 连接数 | 当前活跃连接数 | > 80% max_connections |
| 查询延迟 | 平均查询响应时间 | > 100ms |
| 慢查询 | 慢查询数量 | > 10/分钟 |
| 死锁 | 死锁次数 | > 0/小时 |
| 磁盘使用率 | 数据库磁盘使用率 | > 80% |
| 复制延迟 | 主从复制延迟 | > 10s |
| 缓存命中率 | Buffer cache hit ratio | < 95% |

## 反模式

| 反模式 | 说明 | 正确做法 |
|--------|------|----------|
| 无主键表 | 表没有主键 | 每个表必须有主键 |
| 无索引外键 | 外键列没有索引 | 所有外键必须建索引 |
| VARCHAR 无长度 | 使用 VARCHAR 不指定长度 | 指定合适的长度 |
| 用 TEXT 存所有字符串 | 滥用 TEXT 类型 | 根据数据特性选择合适类型 |
| 用 VARCHAR 存 JSON | 用 VARCHAR 存 JSON 数据 | 使用 JSONB 类型 |
| 用 FLOAT 存金额 | 浮点数存金额 | 使用 NUMERIC 类型 |
| 无审计字段 | 表没有 created_at, updated_at | 每表必须有审计字段 |
| 物理删除 | 直接 DELETE 数据 | 使用软删除 |
| SELECT * | 查询所有列 | 明确列出需要的列 |
| 无 LIMIT 查询 | 查询不加 LIMIT | 所有查询必须有 LIMIT |
| 拼接 SQL | 字符串拼接 SQL | 使用参数化查询 |
| 长事务 | 事务包含过多操作 | 事务尽可能短小 |

## 检查清单

### 表设计检查

- [ ] 表名符合命名规范（snake_case 复数）
- [ ] 列名符合命名规范（snake_case 单数）
- [ ] 主键已定义（UUID 或 BIGINT）
- [ ] 审计字段已定义（created_at, updated_at, deleted_at）
- [ ] 所有外键已定义
- [ ] 所有外键列有索引
- [ ] 所有检查约束已定义
- [ ] 所有唯一约束已定义
- [ ] 所有列有默认值（或 NOT NULL）
- [ ] 所有列和表有 COMMENT
- [ ] 字段类型选择合适

### 索引检查

- [ ] 所有外键列有索引
- [ ] WHERE 常用列有索引
- [ ] ORDER BY 常用列有索引
- [ ] JOIN 条件列有索引
- [ ] 复合索引列顺序正确
- [ ] 索引数量合理（不冗余）

### 查询检查

- [ ] 不使用 SELECT *
- [ ] 所有查询有 LIMIT
- [ ] 使用参数化查询
- [ ] 避免 N+1 查询
- [ ] 避免在 WHERE 中对列使用函数
- [ ] 事务管理正确

### 迁移检查

- [ ] 迁移文件命名正确
- [ ] 迁移有 UP 和 DOWN 脚本
- [ ] 迁移可重复执行
- [ ] 迁移在测试环境验证
- [ ] 迁移不影响现有功能

### 安全审查

- [ ] 敏感数据已加密
- [ ] 密码使用 bcrypt 哈希
- [ ] 不使用数据库 root 账号
- [ ] 应用账号权限最小化
- [ ] 备份策略已配置

---

**本规范是所有 AI Agent 操作数据库的基础标准。每个 Database Engineer 和 Backend Engineer 在操作数据库时必须严格遵守本规范。**