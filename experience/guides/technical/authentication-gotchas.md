# 认证授权陷阱

> JWT · Cookie · 权限验证 · 常见错误

## 📋 目录

- [Cookie 配置](#-cookie-配置)
- [Token 刷新流程](#-token-刷新流程)
- [权限验证](#-权限验证)
- [测试技巧](#-测试技巧)
- [常见错误](#-常见错误)

---

## 🍪 Cookie 配置

### 安全设置

#### ✅ 推荐配置

```python
# app/core/config.py

class Settings(BaseSettings):
    # Cookie 配置
    COOKIE_SECURE: bool = False  # 开发环境 False，生产 True
    COOKIE_SAME_SITE: str = "strict"  # 防止 CSRF
    COOKIE_HTTP_ONLY: bool = True  # 防止 XSS
    COOKIE_PATH: str = "/"  # 所有路径可访问
    
    # Token 配置
    ACCESS_TOKEN_EXPIRE_MINUTES: int = 15
    REFRESH_TOKEN_EXPIRE_DAYS: int = 7
```

#### 设置 Cookie

```python
# app/api/auth.py

@router.post("/jwt/login", response_model=TokenResponse)
async def login(response: Response, ...):
    # 生成 tokens
    access_token = create_access_token(subject=user.id)
    refresh_token = create_refresh_token(subject=user.id)
    
    # 设置 Cookie
    response.set_cookie(
        key="access_token",
        value=access_token,
        max_age=900,  # 15 分钟
        httponly=settings.COOKIE_HTTP_ONLY,
        secure=settings.COOKIE_SECURE,
        samesite=settings.COOKIE_SAME_SITE,
        path=settings.COOKIE_PATH,
    )
    response.set_cookie(
        key="refresh_token",
        value=refresh_token,
        max_age=604800,  # 7 天
        httponly=settings.COOKIE_HTTP_ONLY,
        secure=settings.COOKIE_SECURE,
        samesite=settings.COOKIE_SAME_SITE,
        path=settings.COOKIE_PATH,
    )
    
    return TokenResponse(user=...)
```

### SameSite 说明

| 值 | 说明 | 适用场景 |
|----|------|----------|
| `Strict` | 仅同站请求发送 Cookie | 高安全性，防止 CSRF |
| `Lax` | 部分跨站请求发送（如链接） | 平衡安全性和可用性 |
| `None` | 所有请求都发送（需 Secure） | 需要跨站访问的场景 |

**推荐**: 使用 `Strict`，配合刷新 token 机制。

---

## 🔄 Token 刷新流程

### 完整流程

```
┌─────────────┐
│  用户登录    │
└──────┬──────┘
       │
       ▼
┌─────────────────┐
│ 设置 Cookie:    │
│ - access_token  │ (15 分钟)
│ - refresh_token │ (7 天)
└──────┬──────────┘
       │
       │ 访问 API
       ▼
┌─────────────────┐
│ access_token    │
│ 未过期          │─────▶ ✅ 正常访问
└──────┬──────────┘
       │
       │ 过期
       ▼
┌─────────────────┐
│ 返回 401        │
│ WWW-Authenticate│
└──────┬──────────┘
       │
       │ 自动刷新
       ▼
┌─────────────────┐
│ POST /refresh   │
│ (携带 Cookie)   │
└──────┬──────────┘
       │
       │ refresh_token 有效
       ▼
┌─────────────────┐
│ 生成新的        │
│ access_token    │
│ 设置新 Cookie   │
└──────┬──────────┘
       │
       │ refresh_token 也过期
       ▼
┌─────────────────┐
│ 返回 401        │
│ 跳转登录页     │
└─────────────────┘
```

### 刷新端点实现

```python
# app/api/auth.py

@router.post("/jwt/refresh")
async def refresh_token(response: Response, request: Request, ...):
    """刷新 access_token"""
    # 从 Cookie 读取 refresh_token
    refresh_token = request.cookies.get("refresh_token")
    if not refresh_token:
        raise HTTPException(401, detail="Refresh token 不存在")
    
    # 验证 refresh_token
    payload = decode_token(refresh_token, token_type="refresh")
    if not payload:
        raise HTTPException(401, detail="Refresh token 无效或已过期")
    
    # 获取用户 ID
    user_id = payload.get("sub")
    if not user_id:
        raise HTTPException(401, detail="Refresh token 无效")
    
    # 查询用户
    user = await get_user_by_id(user_id)
    if not user or not user.is_active:
        raise HTTPException(401, detail="用户不存在或已禁用")
    
    # 生成新的 access_token
    new_access_token = create_access_token(
        subject=user.id,
        extra_claims={"role": user.role.name},
    )
    
    # 设置新的 Cookie
    response.set_cookie(
        key="access_token",
        value=new_access_token,
        max_age=900,
        httponly=True,
        secure=settings.COOKIE_SECURE,
        samesite=settings.COOKIE_SAME_SITE,
        path="/",
    )
    
    return {"message": "Token 刷新成功"}
```

### 前端处理

```javascript
// Axios 拦截器示例

axios.interceptors.response.use(
  response => response,
  async error => {
    const originalRequest = error.config
    
    // 如果是 401 且未重试过
    if (error.response?.status === 401 && !originalRequest._retry) {
      originalRequest._retry = true
      
      try {
        // 尝试刷新 token
        await axios.post('/api/v1/auth/jwt/refresh', {}, {
          withCredentials: true  // 发送 Cookie
        })
        
        // 重试原请求
        return axios(originalRequest)
      } catch (refreshError) {
        // 刷新失败，跳转登录
        window.location.href = '/login'
        return Promise.reject(refreshError)
      }
    }
    
    return Promise.reject(error)
  }
)
```

---

## 🔐 权限验证

### 依赖注入实现

```python
# app/core/dependencies.py

async def get_current_user(
    request: Request,
    db: AsyncSession = Depends(get_db),
    token: Optional[str] = Depends(oauth2_scheme),
) -> User:
    """获取当前登录用户"""
    # 优先从 Cookie 读取
    access_token = request.cookies.get("access_token")
    
    # 如果 Cookie 没有，尝试从 Header 读取
    if not access_token and token:
        access_token = token
    
    if not access_token:
        raise HTTPException(401, detail="未认证")
    
    # 解码 token
    payload = decode_token(access_token, token_type="access")
    if not payload:
        raise HTTPException(401, detail="Token 无效或已过期")
    
    user_id = payload.get("sub")
    if not user_id:
        raise HTTPException(401, detail="Token 无效")
    
    # 查询用户
    result = await db.execute(
        select(User).where(User.id == UUID(user_id))
        .options(selectinload(User.role))
    )
    user = result.scalar_one_or_none()
    
    if not user or not user.is_active:
        raise HTTPException(401, detail="用户不存在或已禁用")
    
    return user


async def get_current_admin_user(
    current_user: User = Depends(get_current_user),
) -> User:
    """获取当前管理员用户"""
    if current_user.role.name != "admin":
        raise HTTPException(403, detail="权限不足，需要管理员角色")
    return current_user


async def get_current_approver_node1(
    current_user: User = Depends(get_current_user),
) -> User:
    """获取当前一级审批人"""
    if current_user.role.name not in ["admin", "approver_node1"]:
        raise HTTPException(403, detail="权限不足，需要一级审批人角色")
    return current_user
```

### 使用示例

```python
# app/api/reservations.py

@router.get("/admin", response_model=List[ReservationResponse])
async def get_all_reservations(
    page: int = Query(1, ge=1),
    page_size: int = Query(10, ge=10, le=50),
    current_user: User = Depends(get_current_user),  # ✅ 需要登录
    es_client: AsyncElasticsearch = Depends(get_es_client),
):
    """获取所有预约列表 - 管理员"""
    # 额外权限检查
    if current_user.role.name != "admin":
        raise HTTPException(403, detail="权限不足，需要管理员角色")
    
    service = get_reservation_service(es_client)
    reservations = await service.get_all_reservations(
        days=90, page=page, page_size=page_size,
    )
    return reservations
```

---

## 🧪 测试技巧

### 测试登录

```python
# tests/test_auth_api.py

@pytest.mark.asyncio
async def test_login_success(self, client: AsyncClient, test_user):
    """测试登录成功"""
    response = await client.post(
        "/api/v1/auth/jwt/login",
        data={
            "username": "test@example.com",
            "password": "testpassword123",
        },
    )
    
    assert response.status_code == 200
    data = response.json()
    assert data["user"]["email"] == "test@example.com"
    
    # 检查 Cookie
    assert "access_token" in response.cookies
    assert "refresh_token" in response.cookies
```

### 测试受保护端点

```python
# tests/test_reservations_api.py

@pytest.mark.asyncio
async def test_create_reservation(self, client, general_user, test_museum, test_hall):
    """测试创建预约"""
    # 1. 登录获取 Cookie
    await client.post(
        "/api/v1/auth/jwt/login",
        data={"username": "user@example.com", "password": "password123"}
    )
    
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

### 测试权限拒绝

```python
@pytest.mark.asyncio
async def test_admin_only_endpoint(self, client, general_user):
    """测试仅管理员访问的端点"""
    # 普通用户登录
    await client.post("/api/v1/auth/jwt/login", data={
        "username": "user@example.com",
        "password": "password123"
    })
    
    # 访问管理员端点
    response = await client.get("/api/v1/reservations/admin")
    
    # 应该返回 403 或 404
    assert response.status_code in [403, 404]
```

### 测试未授权访问

```python
@pytest.mark.asyncio
async def test_unauthorized_access(self, client):
    """测试未授权访问"""
    # 未登录直接访问
    response = await client.get("/api/v1/reservations/mine")
    
    assert response.status_code == 401
```

---

## ❌ 常见错误

### 1. Cookie 未发送

#### 问题
```python
# ❌ 忘记配置 withCredentials
const apiClient = axios.create({
  baseURL: '/api/v1',
  // ❌ 没有 withCredentials: true
})
```

#### 解决
```javascript
// ✅ 允许发送 Cookie
const apiClient = axios.create({
  baseURL: '/api/v1',
  withCredentials: true,  // ✅ 关键配置
})
```

### 2. Token 类型混淆

#### 问题
```python
# ❌ 使用 access_token 刷新
payload = decode_token(access_token, token_type="refresh")
# 抛出：Token 类型不匹配
```

#### 解决
```python
# ✅ 使用正确的 token 类型
access_payload = decode_token(access_token, token_type="access")
refresh_payload = decode_token(refresh_token, token_type="refresh")
```

### 3. OAuth2Scheme 配置错误

#### 问题
```python
# ❌ auto_error=True (默认)
oauth2_scheme = OAuth2PasswordBearer(tokenUrl="/api/v1/auth/jwt/login")
# 会自动抛出 401，无法从 Cookie 读取
```

#### 解决
```python
# ✅ auto_error=False
oauth2_scheme = OAuth2PasswordBearer(
    tokenUrl="/api/v1/auth/jwt/login",
    auto_error=False  # ✅ 允许自定义处理
)

async def get_current_user(
    request: Request,
    token: Optional[str] = Depends(oauth2_scheme),
):
    # 优先从 Cookie 读取
    access_token = request.cookies.get("access_token")
    if not access_token and token:
        access_token = token
    # ...
```

### 4. 测试时依赖注入 Mock 无效

#### 问题
```python
# ❌ 直接 Patch 无效
with patch('app.core.dependencies.get_current_user'):
    response = await client.get("/api/v1/reservations/admin")
```

#### 解决
```python
# ✅ 使用 dependency_overrides
@pytest.fixture
async def client(test_db: AsyncSession):
    async def override_get_db():
        yield test_db
    
    app.dependency_overrides[get_db] = override_get_db
    
    async with AsyncClient(transport=ASGITransport(app=app), base_url="http://test") as ac:
        yield ac
    
    app.dependency_overrides.clear()  # ✅ 清理
```

### 5. 路由顺序导致权限验证失效

#### 问题
```python
# ❌ 参数路由在前
@router.get("/{reservation_id}")
async def get_reservation(reservation_id: str):
    ...

@router.get("/admin")  # ❌ 永远不会被匹配
async def get_all_reservations(
    current_user: User = Depends(get_current_admin_user),  # ❌ 不会执行
):
    ...
```

#### 解决
```python
# ✅ 具体路由在前
@router.get("/admin")
async def get_all_reservations(
    current_user: User = Depends(get_current_admin_user),  # ✅ 正常执行
):
    ...

@router.get("/{reservation_id}")
async def get_reservation(reservation_id: str):
    ...
```

---

## 📚 相关资源

### 官方文档
- [FastAPI 认证](https://fastapi.tiangolo.com/tutorial/security/)
- [OAuth2 Cookie](https://fastapi.tiangolo.com/tutorial/security/oauth2-jwt/)
- [SameSite Cookies](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Set-Cookie/SameSite)

### 内部文档
- [FastAPI 经验总结](../frameworks/fastapi-lessons.md)
- [API 设计文档](../templates/api-design.md)

### Rules
- [API 斜杠规则](../rules/general/api-slash.rule)

---

**作者**: NRI Beijing Development Team  
**创建日期**: 2026-03-15  
**最后更新**: 2026-03-15  
**相关案例**: 预约 API 权限验证测试
