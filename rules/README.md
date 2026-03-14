# 📜 Rules - 项目规则系统

> 参考 Cursor Rules 和 Claude Code Skills 的设计理念，为 IT 项目开发场景设计的结构化规则系统

---

## 🎯 什么是 Rules？

Rules 是在特定场景下 AI 应该遵循的**强制性规则**和**最佳实践**。

### 特点

- ✅ **场景化** - 针对特定开发场景
- ✅ **结构化** - 清晰的格式和分类
- ✅ **可执行** - AI 可以直接应用
- ✅ **可组合** - 多个 rules 可以组合使用

### 与 Skills 的区别

| 特性 | Rules | Skills |
|------|-------|--------|
| **定位** | 应该遵循的规则 | 可以执行的能力 |
| **强制性** | 高（必须遵守） | 中（按需使用） |
| **范围** | 具体场景 | 通用能力 |
| **示例** | API 命名规则 | Git 操作技能 |

---

## 📁 Rules 分类

### 1. 通用规则 (General)

适用于所有项目的基础规则。

- **[coding-standards.rule](rules/general/coding-standards.rule)** - 编码规范
- **[git-commit.rule](rules/general/git-commit.rule)** - Git 提交规范
- **[api-design.rule](rules/general/api-design.rule)** - API 设计原则
- **[security.rule](rules/general/security.rule)** - 安全规范

### 2. 技术栈规则 (Tech Stack)

针对特定技术栈的规则。

- **[vue3.rule](rules/tech/vue3.rule)** - Vue3 开发规范
- **[fastapi.rule](rules/tech/fastapi.rule)** - FastAPI 开发规范
- **[typescript.rule](rules/tech/typescript.rule)** - TypeScript 使用规范
- **[python.rule](rules/tech/python.rule)** - Python 编码规范

### 3. 项目特定规则 (Project)

针对特定项目的规则。

- **[nribeijing-api.rule](rules/project/nribeijing-api.rule)** - NRI API 规则
- **[nribeijing-frontend.rule](rules/project/nribeijing-frontend.rule)** - NRI 前端规则

---

## 📝 Rule 文件格式

### 基本结构

```markdown
# Rule: 规则名称

## 适用场景
（什么时候使用这个 rule）

## 规则内容
1. 规则 1
2. 规则 2
3. 规则 3

## 示例

### ✅ 正确示例
```代码
正确代码示例
```

### ❌ 错误示例
```代码
错误代码示例
```

## 相关规则
- [相关 rule 1](./related-1.rule)
- [相关 rule 2](./related-2.rule)

## 版本历史
- v1.0 - 初始版本
```

---

## 🚀 使用方式

### 1. 任务开始前指定 Rules

```markdown
任务：开发用户管理功能

请遵循以下 Rules：
- [vue3.rule](./rules/tech/vue3.rule)
- [api-design.rule](./rules/general/api-design.rule)
- [nribeijing-frontend.rule](./rules/project/nribeijing-frontend.rule)
```

### 2. 在 Onboarding 中引用

```markdown
新成员请阅读并遵循：
- [coding-standards.rule](./rules/general/coding-standards.rule)
- [git-commit.rule](./rules/general/git-commit.rule)
```

### 3. AI 自动应用

配置 AI 助手在特定场景下自动应用对应 Rules。

---

## 💡 最佳实践

### 1. 规则要具体

❌ **差**: "写好代码"
✅ **好**: "函数不超过 50 行，参数不超过 5 个"

### 2. 提供示例

❌ **差**: 只有规则描述
✅ **好**: 正确示例 + 错误示例

### 3. 保持更新

- 定期 review rules
- 根据实际经验更新
- 收集团队反馈

### 4. 适度原则

- 规则不是越多越好
- 聚焦关键问题
- 避免过度约束

---

## 📊 效果对比

### 没有 Rules

```
人类：写个 API
AI: （按自己理解写）
人类：不对，应该是 RESTful 风格
AI: （重新写）
人类：还要加错误处理
...（多轮沟通）
```

### 有 Rules

```
人类：写个 API（遵循 api-design.rule）
AI: （按 rule 自动包含 RESTful、错误处理、验证等）
人类：很好，只需要调整一个小地方
```

---

## 🔗 参考资源

- [Cursor Rules 设计理念](https://cursor.sh/docs/rules)
- [Claude Code Skills](https://claude.ai/code)
- [我们的沟通原则](docs/communication-principles.md)

---

**维护**: NRI Beijing Development Team  
**版本**: v1.0  
**最后更新**: 2026-03-14
