# Mock 异步方法问题

> AsyncMock 不能直接 await 的问题

**发生日期**: 2026-03-15  
**发现人**: OpenClaw  
**项目**: 展览馆预约管理系统

---

## 📋 问题概述

### 问题描述

在编写单元测试时，使用 `AsyncMock` Mock 异步服务层，测试时抛出 `TypeError: object MagicMock can't be used in 'await' expression`。

### 影响范围

- **影响模块**: 单元测试
- **影响功能**: 所有使用 Mock 的测试
- **严重程度**: 🟡 中 (测试无法运行)

---

## 🔍 问题现象

### 错误表现

```python
# ❌ 错误的 Mock 方式
mock_service = AsyncMock()
mock_service.get_all.return_value = [...]

# 测试代码
result = await service.get_all()  # ❌ 抛出 TypeError

# 错误信息
TypeError: object MagicMock can't be used in 'await' expression
```

### 错误日志

```
FAILED tests/test_reservations_api.py::TestAdminReservations::test_get_all_reservations
TypeError: object MagicMock can't be used in 'await' expression
```

---

## 🔬 问题分析

### 问题根因

**AsyncMock 的属性也是 Mock**：

1. `AsyncMock()` 创建的对象本身可以 await
2. 但它的属性 (如 `mock_service.get_all`) 返回的是 `MagicMock`
3. `MagicMock` 不能直接 await
4. 导致 `await service.get_all()` 失败

### 技术细节

```python
from unittest.mock import AsyncMock, MagicMock

# AsyncMock 本身可以 await
mock = AsyncMock()
await mock()  # ✅ 可以

# 但属性返回的是 MagicMock
mock.method.return_value = [...]
await mock.method()  # ❌ 不可以 - MagicMock 不能 await

# 正确做法
async def mock_method(*args, **kwargs):
    return [...]
mock.method = mock_method
await mock.method()  # ✅ 可以
```

---

## ✅ 解决方案

### 方法 1: 使用异步函数包装 (推荐)

```python
# ✅ 正确：使用异步函数包装
mock_service = MagicMock()

async def mock_get_all(*args, **kwargs):
    return [{"id": "res1", "status": "pending"}]

mock_service.get_all = mock_get_all

# 测试代码
result = await mock_service.get_all()  # ✅ 可以
```

### 方法 2: 使用 AsyncMock + spec

```python
# ✅ 正确：使用 AsyncMock + spec
mock_service = AsyncMock(spec=['get_all'])
mock_service.get_all.return_value = [{"id": "res1"}]

# 注意：这种方法在某些情况下可能不工作
```

### 方法 3: 使用 Helper 函数

```python
# ✅ 最佳实践：创建 Helper 函数
def create_mock_service():
    """创建 Mock 服务实例"""
    mock_service = MagicMock()
    
    async def mock_get_all(*args, **kwargs):
        return [{"id": "res1", "status": "pending"}]
    
    async def mock_get_by_id(res_id):
        return {"id": res_id, "status": "pending"}
    
    mock_service.get_all = mock_get_all
    mock_service.get_by_id = mock_get_by_id
    
    return mock_service

# 使用
mock_service = create_mock_service()
result = await mock_service.get_all()  # ✅ 可以
```

---

## 🔧 修复过程

### 修复步骤

1. **识别问题**: 测试抛出 `TypeError`
2. **分析原因**: AsyncMock 属性不能 await
3. **修复代码**: 使用异步函数包装
4. **验证修复**: 运行测试

### 修复代码对比

**❌ 修复前**:

```python
# tests/test_reservations_api.py

@pytest.mark.asyncio
async def test_get_all_reservations(client, admin_user, mock_es_client):
    # Mock Service
    with patch('app.api.reservations.get_reservation_service') as mock_svc:
        mock_service_instance = AsyncMock()  # ❌ 错误
        mock_service_instance.get_all_reservations.return_value = [...]
        mock_svc.return_value = mock_service_instance
        
        response = await client.get("/api/v1/reservations/admin")
```

**✅ 修复后**:

```python
# tests/test_reservations_api.py

@pytest.mark.asyncio
async def test_get_all_reservations(client, admin_user, mock_es_client):
    # Mock Service
    with patch('app.api.reservations.get_reservation_service') as mock_svc:
        mock_service_instance = MagicMock()  # ✅ 正确
        
        async def mock_get_all(*args, **kwargs):
            return [{"id": "res1", "status": "pending"}]
        
        mock_service_instance.get_all_reservations = mock_get_all
        mock_svc.return_value = mock_service_instance
        
        response = await client.get("/api/v1/reservations/admin")
```

---

## 📚 经验教训

### 问题原因

1. **对 AsyncMock 理解不足**: 不知道属性也是 Mock
2. **缺乏文档**: 没有 Mock 异步方法的最佳实践
3. **缺乏检查清单**: 测试审查未包含 Mock 验证

### 关键教训

1. ✅ **AsyncMock 属性不能直接 await** - 需要异步函数包装
2. ✅ **使用 Helper 函数** - 创建可复用的 Mock 对象
3. ✅ **测试 Mock 本身** - 确保 Mock 配置正确

### 预防措施

**在测试策略文档中添加规范**:

```markdown
## Mock 异步方法

### 错误做法

```python
mock_service = AsyncMock()
mock_service.get_all.return_value = [...]  # ❌
```

### 正确做法

```python
mock_service = MagicMock()

async def mock_get_all(*args, **kwargs):
    return [...]

mock_service.get_all = mock_get_all  # ✅
```
```

**在检查清单中添加检查项**:

```markdown
## 测试质量检查

### Mock 验证
- [ ] Mock 异步方法使用异步函数包装
- [ ] Mock 配置正确
- [ ] Mock 范围适当
```

---

## 📊 影响统计

| 指标 | 数值 |
|------|------|
| **返工时间** | 3 小时 |
| **影响范围** | 所有单元测试 |
| **严重程度** | 🟡 中 |
| **发现阶段** | 测试编写阶段 |
| **修复成本** | 低 (修改 Mock 配置) |

---

## 🔗 相关资源

### 内部文档
- [测试策略](../../design/07-testing.md#mock-异步方法)
- [Pytest 测试实践](../guides/tools/pytest-practices.md)
- [提交前检查清单](../checklists/before-commit.md)

### 外部资源
- [unittest.mock 文档](https://docs.python.org/3/library/unittest.mock.html)
- [AsyncMock 文档](https://docs.python.org/3/library/unittest.mock.html#unittest.mock.AsyncMock)

---

**案例作者**: OpenClaw  
**创建日期**: 2026-03-15  
**最后更新**: 2026-03-15  
**状态**: ✅ 已解决
