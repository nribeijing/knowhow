# FastAPI 实战经验总结

> 记录 FastAPI 开发中的教训、技巧和最佳实践

## 📋 目录

- [路由顺序陷阱](#-路由顺序陷阱)
- [依赖注入的坑](#-依赖注入的坑)
- [响应模型验证](#-响应模型验证)
- [测试技巧](#-测试技巧)

---

## ⚠️ 路由顺序陷阱

### 问题描述

FastAPI 按**定义顺序**匹配路由，具体路由必须放在参数路由之前，否则会被错误匹配。

### ❌ 错误示例

```python
from fastapi import APIRouter

router = APIRouter()

@router.get("/{reservation_id}")  # ❌ 参数路由在前
async def get_reservation(reservation_id: str):
    ...

@router.get("/admin")  # ❌ 永远不会被匹配！
async def get_all_reservations():
    ...
```

**问题**: 访问 `/api/v1/reservations/admin` 时，FastAPI 会将 `admin` 匹配为 `reservation_id` 参数，导致 404 或逻辑错误。

### ✅ 正确做法

```python
from fastapi import APIRouter

router = APIRouter()

# ✅ 具体路由在前
@router.get("/admin")
async def get_all_reservations():
    ...

@router.get("/admin/export")
async def export_reservations():
    ...

# ✅ 参数路由在后
@router.get("/{reservation_id}")
async def get_reservation(reservation_id: str):
    ...
```

### 路由优先级规则

```
1. 静态路径 (/admin)
2. 带前缀的静态路径 (/admin/export)
3. 带参数的路径 (/{id})
4. 带多个参数的路径 (/{id}/items/{item_id})
```

### 完整示例

```python
router = APIRouter()

# P0: 静态路由（最具体）
@router.get("/admin")
@router.get("/admin/export")
@router.get("/mine")
@router.get("/pending/node1")
@router.get("/pending/node2")
@router.get("/approved/node1")
@router.get("/approved/node2")

# P1: 带操作的路由
@router.patch("/{id}/withdraw")
@router.patch("/{id}/approve")
@router.patch("/{id}/approve-node2")

# P2: 通用参数路由（最宽泛）
@router.get("/{reservation_id}")
```

### 相关案例

- [API 斜杠问题](../case-studies/api-slash-issue.md) - 路由匹配导致的 404

---

## 🔑 依赖注入的坑

### Cookie 认证配置

#### ❌ 错误做法

```python
oauth2_scheme = OAuth2PasswordBearer(tokenUrl="/api/v1/auth/jwt/login")
# ❌ auto_error=True (默认) - 会自动抛出 401，无法从 Cookie 读取
```

#### ✅ 正确做法

```python
oauth2_scheme = OAuth2PasswordBearer(
    tokenUrl="/api/v1/auth/jwt/login",
    auto_error=False  # ✅ 允许从 Cookie 读取
)

async def get_current_user(
    request: Request,
    db: AsyncSession = Depends(get_db),
    token: Optional[str] = Depends(oauth2_scheme),
) -> User:
    # 优先从 Cookie 读取
    access_token = request.cookies.get("access_token")
    
    # 如果 Cookie 没有，尝试从 Header 读取
    if not access_token and token:
        access_token = token
    
    if not access_token:
        raise HTTPException(status_code=401, detail="未认证")
    
    # 继续验证 token...
```

### 测试时 Mock 依赖注入

#### 问题场景

测试时需要 Mock `get_current_user` 等依赖，但直接 Patch 无效。

#### ✅ 正确做法

```python
# tests/conftest.py

@pytest.fixture
async def client(test_db: AsyncSession) -> AsyncGenerator[AsyncClient, None]:
    """创建测试客户端"""
    async def override_get_db():
        yield test_db
    
    # 使用 dependency_overrides
    app.dependency_overrides[get_db] = override_get_db
    
    async with AsyncClient(
        transport=ASGITransport(app=app),
        base_url="http://test",
    ) as ac:
        yield ac
    
    # 清理 overrides
    app.dependency_overrides.clear()
```

#### Mock 服务层

```python
# tests/test_reservations_api.py

@pytest.mark.asyncio
async def test_get_all_reservations_admin(client, admin_user, mock_es_client):
    """测试管理员获取所有预约"""
    # 登录获取 Cookie
    await client.post("/api/v1/auth/jwt/login", data={...})
    
    # Mock ES 和 Service
    mock_es_client.search = AsyncMock(return_value={...})
    
    with patch('app.api.reservations.get_es_client', return_value=mock_es_client):
        with patch('app.api.reservations.get_reservation_service') as mock_svc:
            # 关键：使用异步函数包装返回值
            async def mock_get_all(*args, **kwargs):
                return [create_mock_reservation(...)]
            mock_svc_instance.get_all_reservations = mock_get_all
            mock_svc.return_value = mock_svc_instance
            
            response = await client.get("/api/v1/reservations/admin")
    
    assert response.status_code == 200
```

---

## 📊 响应模型验证

### Pydantic 严格验证

FastAPI 会严格验证响应模型，返回数据必须包含所有必填字段。

#### ❌ 错误示例

```python
class ReservationResponse(BaseModel):
    id: str
    status: str
    visit_date: date  # ❌ 必填字段
    # ... 其他字段

# API 返回
return {"id": "res1", "status": "pending"}  # ❌ 缺少 visit_date
# 抛出：ResponseValidationError: 17 validation errors
```

#### ✅ 正确做法

1. **返回完整数据**
```python
return {
    "id": "res1",
    "status": "pending",
    "visit_date": "2026-03-20",
    # ... 所有字段
}
```

2. **或使用 Optional**
```python
class ReservationResponse(BaseModel):
    id: str
    status: str
    visit_date: Optional[date] = None  # ✅ 可选
```

3. **测试时使用完整 Mock 数据**
```python
def create_mock_reservation(user_id, res_id="test_res", status="pending"):
    """创建完整的 Mock 预约数据"""
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
```

---

## 🧪 测试技巧

### Mock 异步方法

#### ❌ 错误做法

```python
mock_service_instance = AsyncMock()
mock_service_instance.get_all_reservations.return_value = [...]
# ❌ AsyncMock 的属性也是 Mock，不能直接 await
```

#### ✅ 正确做法

```python
# 方法 1: 使用异步函数
mock_service_instance = MagicMock()
async def mock_get_all(*args, **kwargs):
    return [...]
mock_service_instance.get_all_reservations = mock_get_all

# 方法 2: 使用 AsyncMock + return_value
mock_service_instance = AsyncMock(spec=['get_all_reservations'])
mock_service_instance.get_all_reservations.return_value = [...]
```

### 测试认证流程

```python
@pytest.mark.asyncio
async def test_create_reservation(client, general_user, test_museum, test_hall):
    """测试创建预约"""
    # 1. 登录获取 Cookie
    login_response = await client.post(
        "/api/v1/auth/jwt/login",
        data={"username": "user@example.com", "password": "password123"}
    )
    assert login_response.status_code == 200
    
    # 2. 使用 Cookie 访问受保护端点
    response = await client.post(
        "/api/v1/reservations/",
        json={
            "museum_id": str(test_museum.id),
            "hall_id": str(test_hall.id),
            "visit_date": "2026-03-20",
            "visit_time": 14,
            "visitor_count": 30,
            "contact_name": "测试联系人",
            "contact_phone": "13800138000",
        }
    )
    
    assert response.status_code == 201
```

### 测试权限验证

```python
@pytest.mark.asyncio
async def test_admin_only_endpoint(client, general_user):
    """测试仅管理员访问的端点"""
    # 普通用户登录
    await client.post("/api/v1/auth/jwt/login", data={
        "username": "user@example.com",
        "password": "password123"
    })
    
    # 访问管理员端点
    response = await client.get("/api/v1/reservations/admin")
    
    # 应该返回 403 或 404（取决于实现）
    assert response.status_code in [403, 404]
```

---

## 🔧 性能优化

### 1. 使用异步 I/O

```python
# ✅ 正确：异步数据库操作
async def get_user(user_id: UUID, db: AsyncSession):
    result = await db.execute(select(User).where(User.id == user_id))
    return result.scalar_one_or_none()

# ❌ 错误：阻塞操作
def get_user(user_id: UUID, db: AsyncSession):
    result = db.execute(...)  # ❌ 阻塞事件循环
```

### 2. 数据库连接池配置

```python
# .env
DATABASE_URL=postgresql+asyncpg://user:pass@localhost/dbname

# 连接池配置（通过 asyncpg 自动管理）
# pool_size: 默认 10
# max_overflow: 默认 20
```

### 3. 避免 N+1 查询

```python
# ❌ N+1 查询
users = await db.execute(select(User))
for user in users:
    role = await db.execute(select(Role).where(Role.id == user.role_id))

# ✅ 使用 selectinload
users = await db.execute(
    select(User).options(selectinload(User.role))
)
```

---

## 📚 相关资源

### 官方文档
- [FastAPI 路由文档](https://fastapi.tiangolo.com/tutorial/path-params/)
- [依赖注入系统](https://fastapi.tiangolo.com/tutorial/dependencies/)
- [测试指南](https://fastapi.tiangolo.com/tutorial/testing/)

### 内部文档
- [API 设计文档](../templates/api-design.md)
- [API 斜杠规则](../rules/general/api-slash.rule)
- [代码审查规则](../rules/general/code-review.rule)

### Skills
- [系统性修复](../skills/dev/systematic-fix.skill)
- [主动验证](../skills/comm/verification.skill)

---

**作者**: NRI Beijing Development Team  
**创建日期**: 2026-03-15  
**最后更新**: 2026-03-15  
**相关案例**: [API 路由顺序问题](../case-studies/)
