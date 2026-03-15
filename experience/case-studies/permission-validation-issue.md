# 权限验证问题

> 依赖注入错误导致的权限漏洞

**发生日期**: 2026-03-15  
**发现人**: OpenClaw  
**项目**: 展览馆预约管理系统

---

## 📋 问题概述

### 问题描述

在开发展览馆管理 API 时，使用了错误的依赖注入 (`get_current_user` 而非 `get_current_admin_user`)，导致一般用户也可以创建展览馆。

### 影响范围

- **影响模块**: 展览馆管理 API
- **影响功能**: 创建、修改、删除展览馆
- **严重程度**: 🔴 高 (安全漏洞)

---

## 🔍 问题现象

### 错误表现

```python
# ❌ 错误的依赖注入
@router.post("/")
async def create_museum(
    museum_in: MuseumCreate,
    current_user: User = Depends(get_current_user),  # ❌ 所有登录用户
):
    """创建新展览馆 - 管理员专用"""
    ...

# 结果：一般用户也可以创建展览馆
```

### 测试结果

```python
# tests/test_museums_api.py

@pytest.mark.asyncio
async def test_create_museum_non_admin(client, general_user):
    """测试非管理员创建展览馆"""
    # 一般用户登录
    await client.post("/api/v1/auth/jwt/login", data={
        "username": "user@example.com",
        "password": "password123"
    })
    
    # 创建展览馆
    response = await client.post("/api/v1/museums/", json={
        "name": "新展览馆",
        "description": "新描述",
    })
    
    # ❌ 期望 403，实际返回 201
    assert response.status_code == 403  # 失败
```

---

## 🔬 问题分析

### 问题根因

**依赖注入选择错误**：

1. `get_current_user` - 验证用户是否登录
2. `get_current_admin_user` - 验证用户是否是管理员
3. 错误地使用了 `get_current_user`
4. 导致所有登录用户都可以访问管理员功能

### 依赖注入对比

| 依赖注入 | 说明 | 适用场景 |
|----------|------|----------|
| `get_current_user` | 验证登录 | 所有登录用户可访问的功能 |
| `get_current_admin_user` | 验证管理员 | 仅管理员可访问的功能 |
| `get_current_approver_node1` | 验证一级审批人 | 一级审批功能 |
| `get_current_approver_node2` | 验证二级审批人 | 二级审批功能 |

---

## ✅ 解决方案

### 修复代码

```diff
# app/api/museums.py

from app.core.dependencies import (
-    get_current_user,
+    get_current_admin_user,
)

@router.post("/")
async def create_museum(
    museum_in: MuseumCreate,
-    current_user: User = Depends(get_current_user),
+    current_user: User = Depends(get_current_admin_user),
):
    """创建新展览馆 - 管理员专用"""
    ...
```

### 所有管理员端点修复

```python
# app/api/museums.py

from app.core.dependencies import get_current_admin_user

@router.get("/")
@router.post("/")
@router.get("/{museum_id}")
@router.patch("/{museum_id}")
@router.delete("/{museum_id}")
@router.get("/{museum_id}/halls")
async def xxx_museum(
    ...,
    current_user: User = Depends(get_current_admin_user),  # ✅ 所有端点使用管理员验证
):
    ...
```

---

## 📚 经验教训

### 问题原因

1. **Design 文档不细致**: 未明确每个 API 的依赖注入选择
2. **缺乏检查清单**: 代码审查未包含权限验证检查
3. **测试覆盖不足**: 权限测试用例不足

### 关键教训

1. ✅ **明确依赖注入选择** - 在 Design 文档中说明
2. ✅ **权限验证检查清单** - 代码审查必查项
3. ✅ **权限测试覆盖** - 每个权限端点都要测试非授权访问

### 预防措施

**在 Design 文档中添加权限规范**:

```markdown
## 权限验证规范

### 依赖注入选择

| 角色 | 依赖注入 |
|------|----------|
| 所有登录用户 | get_current_user |
| 管理员 | get_current_admin_user |
| 一级审批人 | get_current_approver_node1 |

### API 权限标注

每个 API 必须明确标注：

| 方法 | 路径 | 依赖注入 |
|------|------|----------|
| POST | /museums | get_current_admin_user |
```

---

## 📊 影响统计

| 指标 | 数值 |
|------|------|
| **返工时间** | 2 小时 |
| **影响范围** | 6 个 API 端点 |
| **严重程度** | 🔴 高 (安全漏洞) |
| **发现阶段** | 测试阶段 |
| **修复成本** | 低 (修改依赖注入) |

---

**案例作者**: OpenClaw  
**创建日期**: 2026-03-15  
**状态**: ✅ 已解决
