# 🧠 KnowHow - LLM 协作指南

> 提升人与 LLM 在 IT 项目开发场景下的沟通效率

## 📋 项目目标

帮助开发者与 LLM（如 OpenClaw、Claude、ChatGPT）更高效地协作，减少沟通成本，提升开发效率。

## 🎯 适用场景

- ✅ 代码开发和修复
- ✅ 架构设计讨论
- ✅ Bug 排查和调试
- ✅ 文档编写
- ✅ 代码审查
- ✅ 技术方案评审

## 📚 核心内容

### 1. 沟通原则

- **[沟通原则.md](docs/communication-principles.md)** - 与 LLM 沟通的基本原则
- **[任务描述模板.md](docs/task-description-template.md)** - 如何清晰描述任务
- **[上下文提供指南.md](docs/context-guidelines.md)** - 如何提供有效上下文

### 2. 最佳实践

- **[系统性执行](docs/systematic-execution.md)** - 避免零散修复
- **[主动验证](docs/active-verification.md)** - 修复后的验证流程
- **[边界思考](docs/boundary-thinking.md)** - 提前考虑边界情况

### 3. 模板库

- **[Bug 报告模板](templates/bug-report.md)**
- **[功能需求模板](templates/feature-request.md)**
- **[代码审查模板](templates/code-review.md)**
- **[技术方案模板](templates/tech-proposal.md)**

### 4. 案例研究

- **[API 斜杠问题](case-studies/api-slash-issue.md)** - 10+ 轮修复的教训
- **[ES 查询问题](case-studies/es-keyword-issue.md)** - 字段类型导致的空数据
- **[浏览器缓存问题](case-studies/browser-cache.md)** - 缓存导致的验证困难

### 5. 检查清单

- **[任务开始前检查清单](checklists/before-task.md)**
- **[代码提交前检查清单](checklists/before-commit.md)**
- **[问题排查检查清单](checklists/troubleshooting.md)**

## 🚀 快速开始

### 新用户使用

1. 阅读 [沟通原则](docs/communication-principles.md)
2. 使用 [任务描述模板](templates/task-description.md)
3. 参考 [案例研究](case-studies/) 避免常见错误

### 日常使用

```
接到任务 → 查看相关模板 → 按要求描述 → LLM 执行 → 按清单验证
```

## 💡 核心理念

### 给人类的建议

1. **一次性提供完整上下文**
   - 完整规则列表
   - 所有相关文件
   - 期望的输出格式

2. **明确任务边界**
   - 检查范围
   - 期望输出
   - 验收标准

3. **阶段性确认**
   - 让 LLM 先全面自查
   - 再一次性报告所有问题

### 给 LLM 的要求

1. **系统性执行**
   - 遇到问题先全面检查
   - 一次性列出所有潜在问题
   - 批量修复而非零散修复

2. **主动验证**
   - 修复后立即测试
   - 确认生效后再提交
   - 提供清晰的验证步骤

3. **边界思考**
   - 提前考虑 UUID、数字 ID、嵌套路径等
   - 搜索硬编码可能性
   - 检查配置文件

## 📊 效果对比

### ❌ 低效沟通

```
人类：这里有问题
LLM：修复 A
人类：那里也有问题
LLM：修复 B
人类：还有一个问题
LLM：修复 C
...（10+ 轮）
```

### ✅ 高效沟通

```
人类：这是完整规则（附文档链接）
      请检查所有相关文件
      一次性修复并验证
      
LLM：已全面检查，发现 5 个问题
      已批量修复
      已主动测试验证
      这是验证报告
```

## 🔗 相关资源

- [Onboarding 仓库](https://github.com/nribeijing/onboarding) - 新成员融入指南
- [项目文档仓库](https://github.com/nribeijing/nribeijing-docs) - 技术文档
- [Awesome LLM Prompts](https://github.com/awesome-llm-prompts) - 提示词集合

## 📝 贡献指南

欢迎贡献你的经验：

1. Fork 本仓库
2. 添加你的案例或模板
3. 提交 PR
4. 团队评审后合并

## 📈 版本历史

| 版本 | 日期 | 更新内容 |
|------|------|---------|
| v1.0 | 2026-03-14 | 初始版本，包含基础沟通原则和案例 |

---

**维护团队**: NRI Beijing Development Team  
**最后更新**: 2026-03-14  
**License**: MIT
