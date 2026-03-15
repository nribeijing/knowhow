# Pytest 测试实践指南

> 单元测试最佳实践 · Mock 技巧 · 测试隔离

## 📋 目录

- [测试环境配置](#-测试环境配置)
- [Mock 技巧](#-mock-技巧)
- [测试隔离](#-测试隔离)
- [异步测试](#-异步测试)
- [常见问题](#-常见问题)

---

## 🏗️ 测试环境配置

### 使用 PostgreSQL 测试容器

#### ❌ SQLite 的问题

```python
# ❌ SQLite 不支持 PostgreSQL 特有类型
TEST_DATABASE_URL = "sqlite+aiosqlite:///:memory:"

# 问题:
# - JSONB → 不支持
# - INET → 不支持
# - UUID → 存储为字符串
# - Boolean → 可能为 NULL
# - DateTime → 可能为 NULL
```

#### ✅ PostgreSQL 容器

```python
# tests/conftest.py

TEST_DATABASE_URL = os.getenv(
    "TEST_DATABASE_URL",
    "postgresql+asyncpg://museum_test:museum_test_password@localhost:5433/museum_test_db"
)

@pytest.fixture(scope="function")
async def test_engine(event_loop):
    """创建测试数据库引擎"""
    engine = create_async_engine(
        TEST_DATABASE_URL,
        echo=False,
        pool_pre_ping=True,
    )
    
    # 创建所有表
    async with engine.begin() as conn:
        await conn.run_sync(Base.metadata.create_all)
    
    yield engine
    
    # 清理所有表
    async with engine.begin() as conn:
        await conn.run_sync(Base.metadata.drop_all)
    
    await engine.dispose()
```

### Docker Compose 配置

```yaml
# docker-compose.test.yaml
version: '3.8'

services:
  postgres_test:
    image: postgres:15-alpine
    container_name: museum_postgres_test
    environment:
      POSTGRES_USER: museum_test
      POSTGRES_PASSWORD: museum_test_password
      POSTGRES_DB: museum_test_db
    ports:
      - "5433:5432"
    profiles:
      - test

  elasticsearch_test:
    image: docker.elastic.co/elasticsearch/elasticsearch:8.13.2
    container_name: museum_elasticsearch_test
    environment:
      - discovery.type=single-node
      - xpack.security.enabled=false
      - "ES_JAVA_OPTS=-Xms256m -Xmx256m"
    ports:
      - "9201:9200"
    profiles:
      - test

volumes: {}
```

### 启动测试环境

```bash
# 启动测试容器
docker compose -f docker-compose.test.yaml --profile test up -d

# 等待数据库就绪
sleep 5

# 运行测试
uv run pytest tests/ -v

# 停止测试容器
docker compose -f docker-compose.test.yaml --profile test down
```

---

## 🎭 Mock 技巧

### Mock 异步方法

#### ❌ 错误做法

```python
# ❌ AsyncMock 的属性也是 Mock，不能直接 await
mock_service = AsyncMock()
mock_service.get_all.return_value = [{"id": 1}]

# 调用时抛出：TypeError: object MagicMock can't be used in 'await' expression
```

#### ✅ 正确做法

**方法 1: 使用异步函数**
```python
mock_service = MagicMock()

async def mock_get_all(*args, **kwargs):
    return [{"id": 1}]

mock_service.get_all = mock_get_all
```

**方法 2: 使用 AsyncMock + spec**
```python
mock_service = AsyncMock(spec=['get_all'])
mock_service.get_all.return_value = [{"id": 1}]
```

**方法 3: 使用 AsyncMock + return_value**
```python
mock_service = AsyncMock()

async def mock_method(*args, **kwargs):
    return [{"id": 1}]

mock_service.get_all = mock_method
```

### Mock ES 客户端

```python
# tests/conftest.py

@pytest.fixture
def mock_es_client():
    """Mock ElasticSearch 客户端"""
    mock = AsyncMock()
    
    # 配置常用方法
    mock.index = AsyncMock(return_value={"_id": "test_id"})
    mock.search = AsyncMock(return_value={
        "hits": {
            "total": {"value": 0},
            "hits": []
        }
    })
    mock.update_by_query = AsyncMock(return_value={"updated": 1})
    
    # 配置 indices 属性
    mock.indices = MagicMock()
    mock.indices.refresh = AsyncMock()
    
    return mock
```

### 使用场景

```python
# tests/test_reservations_api.py

@pytest.mark.asyncio
async def test_get_all_reservations(client, admin_user, mock_es_client):
    """测试管理员获取所有预约"""
    # 登录
    await client.post("/api/v1/auth/jwt/login", data={
        "username": "admin@example.com",
        "password": "password123"
    })
    
    # Mock ES search
    mock_es_client.search = AsyncMock(return_value={
        "hits": {
            "total": {"value": 1},
            "hits": [{"_id": "res1", "_source": {...}}]
        }
    })
    
    # Mock Service
    with patch('app.api.reservations.get_es_client', return_value=mock_es_client):
        with patch('app.api.reservations.get_reservation_service') as mock_svc:
            async def mock_get_all(*args, **kwargs):
                return [{"id": "res1", "status": "pending"}]
            mock_svc_instance.get_all_reservations = mock_get_all
            mock_svc.return_value = mock_svc_instance
            
            response = await client.get("/api/v1/reservations/admin")
    
    assert response.status_code == 200
```

---

## 🔒 测试隔离

### 全局状态问题

#### 问题场景

SlowAPI 限流器使用内存存储，所有测试共享状态：

```python
# ❌ 测试 A
async def test_login_rate_limit():
    for i in range(6):
        response = await client.post("/api/v1/auth/jwt/login", ...)
        # 第 6 次触发限流

# ❌ 测试 B
async def test_login_success():
    response = await client.post("/api/v1/auth/jwt/login", ...)
    # ❌ 直接返回 429（因为测试 A 已经消耗了配额）
```

#### ✅ 解决方案

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

### 数据库隔离

```python
# tests/conftest.py

@pytest.fixture(scope="function")
async def test_db(test_engine) -> AsyncGenerator[AsyncSession, None]:
    """每个测试使用独立会话"""
    async_session_maker = async_sessionmaker(
        test_engine,
        class_=AsyncSession,
        expire_on_commit=False,
    )
    
    async with async_session_maker() as session:
        try:
            yield session
            await session.commit()
        except Exception:
            await session.rollback()
            raise
```

### Cookie 隔离

```python
# 每个测试使用新的 client 实例
@pytest.fixture
async def client(test_db: AsyncSession) -> AsyncGenerator[AsyncClient, None]:
    """创建测试客户端"""
    async with AsyncClient(
        transport=ASGITransport(app=app),
        base_url="http://test",
    ) as ac:
        yield ac
    # client 销毁时 Cookie 自动清理
```

---

## ⚡ 异步测试

### pytest-asyncio 配置

```toml
# pyproject.toml

[tool.pytest.ini_options]
asyncio_mode = "auto"  # 自动检测异步测试
asyncio_default_fixture_loop_scope = "function"
```

### 异步 Fixture

```python
# tests/conftest.py

@pytest.fixture(scope="session")
def event_loop() -> Generator:
    """创建事件循环"""
    loop = asyncio.get_event_loop_policy().new_event_loop()
    yield loop
    loop.close()

@pytest.fixture(scope="function")
async def test_engine(event_loop):
    """异步数据库引擎"""
    engine = create_async_engine(TEST_DATABASE_URL)
    
    async with engine.begin() as conn:
        await conn.run_sync(Base.metadata.create_all)
    
    yield engine
    
    async with engine.begin() as conn:
        await conn.run_sync(Base.metadata.drop_all)
    
    await engine.dispose()
```

### 异步测试用例

```python
# tests/test_auth_api.py

class TestAuthAPI:
    """认证 API 测试"""
    
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
```

---

## ❓ 常见问题

### 1. Event loop is closed

#### 问题
```
RuntimeError: Event loop is closed
```

#### 原因
- Fixture 作用域不匹配
- 在同步代码中 await 异步函数

#### 解决
```python
# ✅ 确保 event_loop 是 session 级别
@pytest.fixture(scope="session")
def event_loop() -> Generator:
    loop = asyncio.get_event_loop_policy().new_event_loop()
    yield loop
    loop.close()
```

### 2. Mock 返回值不能 await

#### 问题
```
TypeError: object MagicMock can't be used in 'await' expression
```

#### 原因
AsyncMock 的属性也是 Mock，不是真正的异步函数。

#### 解决
```python
# ✅ 使用异步函数包装
async def mock_method(*args, **kwargs):
    return [...]
mock_instance.method = mock_method
```

### 3. 依赖注入 Mock 无效

#### 问题
Patch 了依赖，但测试仍然使用真实实现。

#### 原因
- Patch 路径错误
- 没有使用 `dependency_overrides`

#### 解决
```python
# ✅ 使用 dependency_overrides
app.dependency_overrides[get_db] = override_get_db

# ✅ 或者在测试中 Patch
with patch('app.api.reservations.get_es_client', return_value=mock_es):
    response = await client.get("/api/v1/reservations/admin")
```

### 4. 响应模型验证失败

#### 问题
```
ResponseValidationError: 17 validation errors
```

#### 原因
返回数据缺少必填字段。

#### 解决
```python
# ✅ 返回完整数据
def create_mock_reservation():
    return {
        "id": "res1",
        "user_id": str(uuid4()),
        "user_name": "测试用户",
        # ... 所有必填字段
        "created_at": datetime.now().isoformat(),
        "updated_at": datetime.now().isoformat(),
    }
```

---

## 📚 相关资源

### 官方文档
- [pytest 文档](https://docs.pytest.org/)
- [pytest-asyncio](https://pytest-asyncio.readthedocs.io/)
- [unittest.mock](https://docs.python.org/3/library/unittest.mock.html)

### 内部文档
- [测试执行报告](../../backend/TEST_EXECUTION_REPORT.md)
- [单元测试计划](../../backend/UNIT_TEST_PLAN.md)

### Rules
- [代码审查规则](../rules/general/code-review.rule)

---

**作者**: NRI Beijing Development Team  
**创建日期**: 2026-03-15  
**最后更新**: 2026-03-15  
**相关案例**: 预约 API 测试 (24/24 通过)
