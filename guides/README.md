# 🧭 团队经验指南 (Guides)

> 实战经验总结 · 最佳实践 · 避坑指南

## 📖 定位

本目录收录团队在开发过程中的**经验总结**和**最佳实践**，与 Rules/Skills 的区别：

| 类型 | 强制性 | 内容 | 示例 |
|------|--------|------|------|
| **Rules** | ⚠️ 必须遵守 | 代码规范、约束条件 | `api-slash.rule` |
| **Skills** | ✅ 推荐流程 | 标准化操作步骤 | `systematic-fix.skill` |
| **Guides** | 💡 经验参考 | 心得体会、技巧、陷阱 | `fastapi-lessons.md` |

---

## 📚 目录导航

### 🤖 AI 辅助编程

- **[AI 辅助编程最佳实践](ai-assisted-programming.md)** ⭐ 必读 - 单 Agent 工作流、检查清单、实战经验

### 🗣️ 语言类 (Languages)

- [Python 实战技巧](languages/python-tips.md) - Python 开发中的经验和技巧
- [TypeScript 最佳实践](languages/typescript-tips.md) - TypeScript 使用心得

### 🛠️ 框架类 (Frameworks)

- [FastAPI 经验总结](frameworks/fastapi-lessons.md) - FastAPI 实战中的教训和技巧
- [Vue3 开发模式](frameworks/vue3-patterns.md) - Vue3 组件设计模式
- [SQLAlchemy 陷阱](frameworks/sqlalchemy-traps.md) - ORM 使用中的常见错误

### 🔧 工具类 (Tools)

- [Git 高级技巧](tools/git-advanced.md) - Git 工作流和高级命令
- [Docker 踩坑记录](tools/docker-lessons.md) - 容器化过程中的问题
- [ElasticSearch 指南](tools/elasticsearch-guide.md) - ES 查询和优化
- [Pytest 测试实践](tools/pytest-practices.md) - 单元测试最佳实践

### ⚠️ 易错点 (Common Pitfalls)

- [认证授权陷阱](common-pitfalls/authentication-gotchas.md) - JWT、Cookie、权限验证
- [数据库迁移注意事项](common-pitfalls/database-migrations.md) - Alembic 迁移规范
- [API 设计误区](common-pitfalls/api-design-mistakes.md) - RESTful API 设计教训
- [测试 Mock 陷阱](common-pitfalls/mock-traps.md) - 单元测试 Mock 技巧

---

## 🎯 何时查阅？

### 开始新任务前
- 查看相关框架的 Guides，了解最佳实践
- 阅读易错点，避免重复踩坑

### 遇到问题时
- 先搜索 Guides 中是否有相关经验
- 参考类似问题的解决方案

### 代码审查时
- 对照 Guides 检查是否符合最佳实践
- 发现新的陷阱时，补充到 Guides

### 解决问题后
- **必须**记录经验到 Guides
- 帮助团队避免重复踩坑

---

## 📝 贡献指南

### 记录时机

✅ 应该记录的情况：
- 花费超过 30 分钟解决的问题
- 团队多人重复遇到的错误
- 反直觉的框架行为
- 性能优化的关键发现
- 测试中的特殊场景

❌ 不需要记录：
- 简单的拼写错误
- 明显的逻辑错误
- 已有文档覆盖的内容

### 文档结构

```markdown
# 标题

## 问题描述
简短说明遇到的问题和场景

## 错误示例
```python
# ❌ 错误代码
```

## 正确做法
```python
# ✅ 正确代码
```

## 原因分析
解释为什么会有这个问题

## 相关资源
- 官方文档链接
- 相关 Rules/Skills
- 类似案例
```

### 提交流程

1. 在对应分类下创建 `.md` 文件
2. 更新本索引文档的链接
3. 提交时 message 包含 `[Guides]` 标签
4. 团队评审后合并

---

## 🔄 与 Case Studies 的区别

| Guides | Case Studies |
|--------|--------------|
| 通用经验总结 | 具体案例记录 |
| 面向未来预防 | 面向过去复盘 |
| 结构化知识 | 故事性叙述 |
| 可复用性强 | 特定场景 |

**示例**：
- Guides: `fastapi-lessons.md` - FastAPI 路由顺序的一般规则
- Case Studies: `api-slash-issue.md` - 某次具体的 API 斜杠问题排查过程

---

## 📊 维护状态

| 分类 | 文档数 | 最后更新 |
|------|--------|----------|
| Languages | 0 | - |
| Frameworks | 0 | - |
| Tools | 0 | - |
| Common Pitfalls | 0 | - |

**目标**: 每月至少新增 2 篇经验文档

---

## 🔗 相关资源

- [Rules 目录](../rules/README.md) - 强制性规则
- [Skills 目录](../skills/README.md) - 技能系统
- [Case Studies](../case-studies/) - 案例研究
- [Templates](../templates/) - 文档模板

---

**维护者**: NRI Beijing Development Team  
**创建日期**: 2026-03-15  
**最后更新**: 2026-03-15
