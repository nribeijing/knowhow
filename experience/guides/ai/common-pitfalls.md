# AI 辅助编程常见陷阱

> 5 个核心陷阱及预防方案

**创建日期**: 2026-03-16  
**最后更新**: 2026-03-16  
**状态**: 实战总结

---

## 📋 陷阱概览

| 陷阱 | 出现频率 | 影响 | 预防方法 |
|------|----------|------|----------|
| **1. 路由顺序** | 🔴 高 | API 404 | 静态路由在前 |
| **2. Mock 异步** | 🔴 高 | 测试失败 | 异步函数包装 |
| **3. 权限验证** | 🟡 中 | 安全漏洞 | 正确依赖注入 |
| **4. 测试隔离** | 🟡 中 | 测试失败 | autouse fixture |
| **5. 响应验证** | 🟡 中 | 响应报错 | Helper 函数 |

---

## 1️⃣ 路由顺序陷阱

### 问题描述

FastAPI 按**定义顺序**匹配路由，参数路由在前会捕获静态路由。

### ❌ 错误示例

```python
# ❌ 错误：参数路由在前
@router.get("/{reservation_id}")
async def get_reservation(reservation_id: str):
    ...

@router.get("/admin")  # ❌ 永远不会被匹配！
async def get_all_reservations():
    ...

# 访问 /admin 时，被匹配为 reservation_id="admin"
# → 404 Not Found
```

### ✅ 正确做法

```python
# ✅ 正确：静态路由在前
@router.get("/admin")
async def get_all_reservations():
    """获取所有预约 - 管理员专用"""
    ...

@router.get("/{reservation_id}")
async def get_reservation(reservation_id: str):
    """获取预约详情"""
    ...
```

### 路由优先级规则

```
1. 静态路由 (最高优先级) - /admin, /mine
2. 带操作的路由 (中) - /{id}/approve
3. 纯参数路由 (最低) - /{id}
```

### 预防措施

- [ ] 在检查清单中添加"路由顺序"项
- [ ] AI 自查时重点检查
- [ ] 人类审查时确认

### 相关案例

- [API 路由顺序问题](../case-studies/api-route-order-issue.md)

---

## 2️⃣ Mock 异步方法陷阱

### 问题描述

`AsyncMock` 的属性也是 Mock，不能直接 await。

### ❌ 错误示例

```python
# ❌ 错误
mock_service = AsyncMock()
mock_service.get_all.return_value = [...]

# 调用时抛出：TypeError: object MagicMock can't be used in 'await' expression
```

### ✅ 正确做法

**方法 1: 使用异步函数包装 (推荐)**
```python
mock_service = MagicMock()

async def mock_get_all(*args, **kwargs):
    return [{"id": "res1", "status": "pending"}]

mock_service.get_all = mock_get_all
```

**方法 2: 使用 Helper 函数**
```python
def create_mock_service():
    """创建 Mock 服务实例"""
    mock_service = MagicMock()
    
    async def mock_get_all(*args, **kwargs):
        return [{"id": "res1", "status": "pending"}]
    
    mock_service.get_all = mock_get_all
    return mock_service

# 使用
mock_service = create_mock_service()
```

### 预防措施

- [ ] 使用 Helper 函数生成 Mock
- [ ] 检查清单包含"Mock 验证"项
- [ ] 测试失败时优先检查 Mock

### 相关案例

- [Mock 异步方法问题](../case-studies/mock-async-method-issue.md)
- [Pytest 测试实践](../technical/pytest-practices.md#mock-异步方法)

---

## 3️⃣ 权限验证陷阱

### 问题描述

使用了错误的依赖注入，导致权限控制失效。

### ❌ 错误示例

```python
# ❌ 错误：使用 get_current_user (所有登录用户)
@router.post("/")
async def create_museum(
    current_user: User = Depends(get_current_user),
):
    """创建新展览馆 - 管理员专用"""
    ...

# 结果：一般用户也可以创建展览馆
```

### ✅ 正确做法

```python
# ✅ 正确：使用 get_current_admin_user
@router.post("/")
async def create_museum(
    current_user: User = Depends(get_current_admin_user),
):
    """创建新展览馆 - 管理员专用"""
    ...
```

### 依赖注入选择

| 依赖注入 | 说明 | 适用场景 |
|----------|------|----------|
| `get_current_user` | 验证登录 | 所有登录用户可访问 |
| `get_current_admin_user` | 验证管理员 | 仅管理员可访问 |
| `get_current_approver_node1` | 验证一级审批人 | 一级审批功能 |
| `get_current_approver_node2` | 验证二级审批人 | 二级审批功能 |

### 预防措施

- [ ] 在 Design 文档中明确依赖注入选择
- [ ] 检查清单包含"权限验证"项
- [ ] 人类审查时重点检查

### 相关案例

- [权限验证问题](../case-studies/permission-validation-issue.md)
- [认证授权陷阱](../technical/authentication-gotchas.md)

---

## 4️⃣ 测试隔离陷阱

### 问题描述

全局状态导致测试相互影响。

### ❌ 错误示例

```python
# ❌ 错误：限流器全局状态
async def test_login_rate_limit():
    # 第 1 个测试消耗了限流配额
    for i in range(6):
        response = await client.post("/api/v1/auth/jwt/login", ...)

async def test_login_success():
    # ❌ 直接返回 429（因为限流配额已用完）
    response = await client.post("/api/v1/auth/jwt/login", ...)
```

### ✅ 正确做法

```python
# ✅ 正确：使用 autouse fixture 自动重置
@pytest.fixture(autouse=True)
def reset_rate_limiter():
    """每个测试前自动重置限流器"""
    reset_limiter()
    yield
    reset_limiter()  # 测试后也清理
```

### 常见全局状态

| 状态 | 清理方法 |
|------|----------|
| 限流器 | reset_limiter() |
| 缓存 | cache.clear() |
| 单例 | 重置实例 |
| 环境变量 | 恢复原值 |

### 预防措施

- [ ] 使用 autouse fixture 自动清理
- [ ] 检查清单包含"测试隔离"项
- [ ] 测试随机顺序运行验证

### 相关案例

- [测试隔离问题](../case-studies/test-isolation-issue.md)
- [Pytest 测试实践](../technical/pytest-practices.md#测试隔离)

---

## 5️⃣ 响应验证陷阱

### 问题描述

FastAPI 严格验证响应模型，缺少必填字段会抛出异常。

### ❌ 错误示例

```python
# ❌ 错误：缺少必填字段
return {"id": "res1", "status": "pending"}

# 抛出：ResponseValidationError: 17 validation errors
# - visit_date: Field required
# - visit_time: Field required
# - ...
```

### ✅ 正确做法

**方法 1: 返回完整数据**
```python
return {
    "id": "res1",
    "status": "pending",
    "visit_date": "2026-03-20",
    "visit_time": 14,
    "visitor_count": 30,
    # ... 所有必填字段
}
```

**方法 2: 使用 Helper 函数 (推荐)**
```python
def create_mock_reservation(user_id, res_id="test_res", status="pending"):
    """生成完整的预约响应数据"""
    return {
        "id": res_id,
        "user_id": user_id,
        "user_name": "测试用户",
        "museum_id": str(uuid4()),
        "museum_name": "测试展览馆",
        "hall_id": str(uuid4()),
        "hall_name": "测试展厅",
        "visit_date": "2026-03-20",
        "visit_time": 14,
        "visitor_count": 30,
        "contact_name": "测试联系人",
        "contact_phone": "13800138000",
        "purpose": "测试预约",
        "remarks": "测试备注",
        "status": status,
        "approver_node1_id": None,
        "approver_node1_name": None,
        "approver_node1_action_at": None,
        "approver_node2_id": None,
        "approver_node2_name": None,
        "approver_node2_action_at": None,
        "created_at": datetime.now().isoformat(),
        "updated_at": datetime.now().isoformat(),
    }

# 使用
result = create_mock_reservation(str(user_id), "res1", "pending")
```

### 预防措施

- [ ] 使用 Helper 函数生成完整响应数据
- [ ] 检查清单包含"响应验证"项
- [ ] 运行测试验证响应

### 相关案例

- [响应验证问题](../case-studies/response-validation-issue.md)

---

## 📊 陷阱检查清单

### 编码前

- [ ] 了解本任务可能涉及的陷阱
- [ ] 查阅相关案例

### 编码中

- [ ] 路由定义：静态在前，参数在后
- [ ] Mock 异步：使用异步函数包装
- [ ] 权限验证：使用正确的依赖注入
- [ ] 响应数据：包含所有必填字段

### 测试前

- [ ] 测试隔离：使用 autouse fixture
- [ ] Mock 配置：异步方法正确包装
- [ ] 测试数据：Helper 函数生成

### 提交前

- [ ] 按 [提交前检查清单](../checklists/before-commit.md) 自查
- [ ] 重点检查 5 个陷阱

---

## 🔗 相关资源

### 内部文档
- [AI 辅助编程最佳实践](./ai-assisted-programming.md)
- [AI 进阶技巧](./ai-advanced-tips.md)
- [提交前检查清单](../checklists/before-commit.md)
- [代码审查清单](../checklists/code-review.md)
- [FastAPI 经验总结](./technical/fastapi-lessons.md)
- [Pytest 测试实践](./technical/pytest-practices.md)

### 案例研究
- [API 路由顺序问题](../case-studies/api-route-order-issue.md)
- [Mock 异步方法问题](../case-studies/mock-async-method-issue.md)
- [权限验证问题](../case-studies/permission-validation-issue.md)
- [测试隔离问题](../case-studies/test-isolation-issue.md)
- [响应验证问题](../case-studies/response-validation-issue.md)

### 外部资源
- [FastAPI 路由文档](https://fastapi.tiangolo.com/tutorial/path-params/#order-of-routes)
- [unittest.mock 文档](https://docs.python.org/3/library/unittest.mock.html)

---

**维护者**: NRI Beijing Development Team  
**创建日期**: 2026-03-16  
**最后更新**: 2026-03-16  
**版本**: v1.0
