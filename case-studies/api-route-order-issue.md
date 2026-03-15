# API 路由顺序问题

> FastAPI 路由定义顺序导致的 404 错误

**发生日期**: 2026-03-15  
**发现人**: OpenClaw  
**项目**: 展览馆预约管理系统

---

## 📋 问题概述

### 问题描述

在开发预约管理 API 时，发现访问 `/api/v1/reservations/admin` 返回 404 错误，但路由已定义。

### 影响范围

- **影响模块**: 预约 API - 管理员功能
- **影响功能**: 获取所有预约列表、导出 Excel
- **严重程度**: 🔴 高 (API 不可用)

---

## 🔍 问题现象

### 错误表现

```bash
# 访问管理员端点返回 404
GET /api/v1/reservations/admin
→ 404 Not Found

# 但路由已定义
@router.get("/admin", response_model=List[ReservationResponse])
async def get_all_reservations(...):
    ...
```

### 错误日志

```
INFO:     127.0.0.1:12345 - "GET /api/v1/reservations/admin HTTP/1.1" 404 Not Found
```

---

## 🔬 问题分析

### 路由定义顺序

**❌ 错误的路由顺序**:

```python
# app/api/reservations.py

# ❌ 参数路由在前
@router.get("/{reservation_id}", response_model=ReservationResponse)
async def get_reservation(reservation_id: str, ...):
    """获取预约详情"""
    ...

# ❌ 静态路由在后 (永远不会被匹配)
@router.get("/admin", response_model=List[ReservationResponse])
async def get_all_reservations(...):
    """获取所有预约列表"""
    ...
```

### 问题根因

**FastAPI 按定义顺序匹配路由**：

1. FastAPI 路由匹配是**从上到下**的
2. 先定义的路由优先匹配
3. 参数路由 `/{id}` 会匹配任何路径，包括 `/admin`
4. 当访问 `/admin` 时，被 `/{reservation_id}` 匹配，参数值为 `"admin"`
5. 然后尝试查询 ID 为 `"admin"` 的预约，找不到返回 404

### 类型转换问题

更严重的是，如果参数类型是 `UUID`：

```python
@router.get("/{reservation_id}")
async def get_reservation(reservation_id: UUID, ...):
    ...

# 访问 /admin 时
# "admin" 无法转换为 UUID
# → 422 Validation Error
```

---

## ✅ 解决方案

### 正确的路由顺序

**✅ 静态路由优先**：

```python
# app/api/reservations.py

router = APIRouter()

# ==================== 一般用户功能 ====================
@router.post("/")                    # 1. 创建预约
@router.get("/mine")                 # 2. 我的预约
@router.get("/{reservation_id}")     # 3. 预约详情 (参数路由在后)
@router.patch("/{reservation_id}/withdraw")

# ==================== 一级审批人功能 ====================
@router.get("/pending/node1")        # 1. 静态路由
@router.get("/approved/node1")       # 1. 静态路由
@router.patch("/{reservation_id}/approve")  # 2. 操作路由

# ==================== 二级审批人功能 ====================
@router.get("/pending/node2")
@router.get("/approved/node2")
@router.patch("/{reservation_id}/approve-node2")

# ==================== 管理员功能 ====================
# ⚠️ 注意：管理员路由必须在 /{reservation_id} 之前定义
@router.get("/admin")                # ✅ 静态路由在前
@router.get("/admin/export")         # ✅ 静态路由在前
```

### 路由优先级规则

```
1. 静态路由 (最高优先级)
   - /admin
   - /mine
   - /pending/node1

2. 带操作的参数路由 (中等优先级)
   - /{id}/approve
   - /{id}/withdraw

3. 纯参数路由 (最低优先级)
   - /{id}
```

---

## 🔧 修复过程

### 修复步骤

1. **识别问题**: 访问 `/admin` 返回 404
2. **检查路由**: 发现路由定义顺序问题
3. **调整顺序**: 将静态路由移到参数路由之前
4. **验证修复**: 测试所有端点

### 修复代码

```diff
# app/api/reservations.py

- @router.get("/{reservation_id}", response_model=ReservationResponse)
- async def get_reservation(reservation_id: str, ...):
-     ...
-
- @router.get("/admin", response_model=List[ReservationResponse])
- async def get_all_reservations(...):
+ # ==================== 管理员功能 ====================
+ # 注意：管理员路由必须在 /{reservation_id} 之前定义
+ @router.get("/admin", response_model=List[ReservationResponse])
+ async def get_all_reservations(...):
+     """获取所有预约列表 - 管理员专用"""
      ...
+
+ # ==================== 其他路由 ====================
+ @router.get("/{reservation_id}", response_model=ReservationResponse)
+ async def get_reservation(reservation_id: str, ...):
+     """获取预约详情"""
      ...
```

### 验证测试

```python
# tests/test_reservations_api.py

@pytest.mark.asyncio
async def test_get_all_reservations_admin(client, admin_user):
    """测试管理员获取所有预约"""
    # 登录
    await client.post("/api/v1/auth/jwt/login", data={
        "username": "admin@example.com",
        "password": "password123"
    })
    
    # 访问管理员端点
    response = await client.get("/api/v1/reservations/admin")
    
    # ✅ 应该返回 200
    assert response.status_code == 200
    assert isinstance(response.json(), list)
```

---

## 📚 经验教训

### 问题原因

1. **FastAPI 路由匹配规则**: 从上到下，先定义优先
2. **参数路由过于宽泛**: `/{id}` 会匹配任何路径
3. **缺乏检查清单**: Design 文档未说明路由顺序规则

### 关键教训

1. ✅ **静态路由必须在前** - 如 `/admin`, `/mine`
2. ✅ **操作路由其次** - 如 `/{id}/approve`
3. ✅ **参数路由最后** - 如 `/{id}`
4. ✅ **使用检查清单** - 路由定义后自查顺序

### 预防措施

**在 Design 文档中添加路由顺序规范**:

```markdown
## 路由定义规范

### 路由顺序规则

FastAPI 按定义顺序匹配路由，必须遵循：

1. 静态路由优先 - /admin, /mine
2. 带操作的路由其次 - /{id}/approve
3. 参数路由最后 - /{id}
```

**在检查清单中添加检查项**:

```markdown
## 代码审查清单

### 路由顺序
- [ ] 静态路由在参数路由之前
- [ ] 操作路由在纯参数路由之前
- [ ] 路由顺序符合规范
```

---

## 📊 影响统计

| 指标 | 数值 |
|------|------|
| **返工时间** | 2 小时 |
| **影响范围** | 2 个 API 端点 |
| **严重程度** | 🔴 高 |
| **发现阶段** | 开发阶段 |
| **修复成本** | 低 (调整顺序) |

---

## 🔗 相关资源

### 内部文档
- [API 设计规范](../../design/04-api-design.md#路由定义规范)
- [FastAPI 经验总结](../guides/frameworks/fastapi-lessons.md)
- [提交前检查清单](../checklists/before-commit.md)

### 外部资源
- [FastAPI 路由文档](https://fastapi.tiangolo.com/tutorial/path-params/)
- [路由匹配顺序](https://fastapi.tiangolo.com/tutorial/path-params/#order-of-routes)

---

**案例作者**: OpenClaw  
**创建日期**: 2026-03-15  
**最后更新**: 2026-03-15  
**状态**: ✅ 已解决
