# 测试隔离问题

> 全局状态导致的测试相互影响

**发生日期**: 2026-03-15  
**发现人**: OpenClaw  
**项目**: 展览馆预约管理系统

---

## 📋 问题概述

### 问题描述

SlowAPI 限流器使用内存存储，所有测试共享状态。第一个测试消耗了限流配额，导致后续测试直接返回 429。

### 影响范围

- **影响模块**: 单元测试
- **影响功能**: 限流相关测试
- **严重程度**: 🟡 中 (测试失败)

---

## 🔍 问题现象

### 错误表现

```python
# tests/test_limiter.py

@pytest.mark.asyncio
async def test_login_rate_limit(client):
    """测试登录限流"""
    # 第 1 个测试消耗了限流配额
    for i in range(6):
        response = await client.post("/api/v1/auth/jwt/login", ...)
        # 第 6 次返回 429

@pytest.mark.asyncio
async def test_login_success(client, test_user):
    """测试登录成功"""
    # ❌ 直接返回 429 (因为限流配额已用完)
    response = await client.post("/api/v1/auth/jwt/login", ...)
    assert response.status_code == 200  # 失败
```

---

## 🔬 问题分析

### 问题根因

**全局状态未清理**：

1. SlowAPI 限流器使用内存存储
2. 所有测试共享同一个限流器实例
3. 测试 A 消耗了限流配额
4. 测试 B 直接使用已消耗的配额
5. 导致测试 B 失败

---

## ✅ 解决方案

### 添加自动重置 Fixture

```python
# tests/conftest.py

from app.core.limiter import limiter

def reset_limiter():
    """重置限流器状态"""
    if hasattr(limiter, '_storage') and limiter._storage:
        storage = limiter._storage
        if hasattr(storage, 'data'):
            storage.data.clear()
        elif hasattr(storage, '_data'):
            storage._data.clear()

@pytest.fixture(autouse=True)
def reset_rate_limiter():
    """每个测试前自动重置限流器"""
    reset_limiter()
    yield
    reset_limiter()  # 测试后也清理
```

### 使用 autouse 自动执行

```python
# autouse=True 表示每个测试自动执行此 fixture
@pytest.fixture(autouse=True)
def reset_rate_limiter():
    reset_limiter()
    yield
    reset_limiter()
```

---

## 📚 经验教训

### 关键教训

1. ✅ **全局状态必须清理** - 使用 autouse fixture
2. ✅ **测试前和测试后都清理** - 确保隔离
3. ✅ **测试随机顺序运行** - 验证隔离有效性

### 预防措施

**在测试策略中添加规范**:

```markdown
## 测试隔离

### 全局状态清理

```python
@pytest.fixture(autouse=True)
def reset_global_state():
    reset_state()
    yield
    reset_state()
```
```

---

## 📊 影响统计

| 指标 | 数值 |
|------|------|
| **返工时间** | 1 小时 |
| **影响范围** | 限流测试 |
| **严重程度** | 🟡 中 |
| **修复成本** | 低 (添加 fixture) |

---

**案例作者**: OpenClaw  
**创建日期**: 2026-03-15  
**状态**: ✅ 已解决
