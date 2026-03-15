# 🧠 KnowHow - LLM 协作与团队最佳实践

> 提升人与 LLM、人与人之间的协作效率，融合全球优秀团队实践

**最后更新**: 2026-03-15  
**文档状态**: ✅ 已验证所有链接有效

---

## 📋 项目使命

汇集全球优秀 IT 团队的最佳实践，帮助团队：
- ✅ 与 LLM 更高效协作
- ✅ 建立卓越工程师文化
- ✅ 提升团队沟通质量
- ✅ 持续学习和成长

---

## 🚀 快速开始

### 与 LLM 协作

```markdown
1. 阅读 [沟通原则](docs/communication-principles.md) ⭐
2. 使用 [任务描述模板](docs/task-description-template.md)
3. 指定适用的 Rules 和 Skills
4. 参考 [AI 辅助编程最佳实践](guides/ai-assisted-programming.md)
```

### 新任务开始

```markdown
1. 填写任务描述
2. 指定适用的 Rules
3. 选择要使用的 Skills
4. 按 [检查清单](checklists/before-commit.md) 验证
```

---

## 📚 核心内容

### 🤖 AI 辅助编程 (新增) ⭐

**核心文档**:
- **[AI 辅助编程最佳实践](guides/ai-assisted-programming.md)** - 单 Agent 工作流、检查清单
- **[AI 进阶技巧](guides/ai-advanced-tips.md)** - 上下文管理、常见陷阱
- **[AI 价值分析](guides/ai-value-analysis.md)** - 人类 vs AI 三方对比、ROI 分析
- **[OpenClaw 使用指南](guides/openclaw-usage.md)** - 配置建议、技能使用

**Agent 工具**:
- **[AI Agent 使用指南](agents/README.md)** - Prompt 模板、工作流
- **[代码开发 Prompt](agents/prompts/code-development.prompt)** - 开发任务模板
- **[代码审查 Prompt](agents/prompts/code-review.prompt)** - 审查任务模板

---

### 📖 沟通原则 (Communication)

**基础文档**:
- **[沟通原则](docs/communication-principles.md)** ⭐ 必读
- **[任务描述模板](docs/task-description-template.md)**

**计划中**:
- [ ] 非暴力沟通指南 (待创建)
- [ ] 反馈文化 (待创建)

---

### 📜 Rules - 规则系统

参考 Cursor Rules 设计理念，针对特定场景的强制性规则。

#### ✅ 通用规则 (7 个)

- **[API 尾部斜杠规则](rules/general/api-slash.rule)** - FastAPI 斜杠规范
- **[API 代码规范](rules/general/api-code.rule)** - API 代码规范
- **[API 参数规范](rules/general/api-parameters.rule)** - API 参数规范
- **[API 安全规范](rules/general/api-security.rule)** - API 安全规范
- **[ES 查询规则](rules/general/elasticsearch-query.rule)** - Elasticsearch 查询规范
- **[代码审查规则](rules/general/code-review.rule)** - Google 代码审查实践
- **[Git 提交规则](rules/general/git-commit.rule)** - 提交信息规范

#### ⏳ 技术栈规则 (计划中)

- [ ] Vue3 规则 (待创建)
- [ ] FastAPI 规则 (待创建)
- [ ] TypeScript 规则 (待创建)
- [ ] Python 规则 (待创建)

**[Rules 索引](rules/README.md)** | **[查看规则详情](rules/)**

---

### 🛠️ Skills - 技能系统

参考 Claude Code Skills 设计理念，可复用的标准化操作流程。

#### ✅ 架构技能 (6 个)

- **[架构原则](skills/01-architecture/principles/architecture-principles.skill)**
- **[系统架构](skills/01-architecture/system/system-architecture.skill)**
- **[后端架构](skills/01-architecture/backend/backend-architecture.skill)**
- **[前端架构](skills/01-architecture/frontend/frontend-architecture.skill)**
- **[缓存设计](skills/01-architecture/caching/caching-design.skill)**
- **[消息队列](skills/01-architecture/messaging/message-queue.skill)**

#### ✅ 开发技能 (14 个)

- **[代码最佳实践](skills/02-development/best-practices/code-best-practices.skill)**
- **[数据库架构](skills/02-development/best-practices/database-architecture.skill)**
- **[Python 问题修复](skills/02-development/best-practices/fix-python.skill)**
- **[Vue 问题修复](skills/02-development/best-practices/fix-vue.skill)**
- **[进展汇报](skills/02-development/best-practices/reporting.skill)**
- **[代码审查](skills/02-development/code-review/code-review.skill)**
- **[Python 代码审查](skills/02-development/code-review/code-review-python.skill)**
- **[Vue 代码审查](skills/02-development/code-review/code-review-vue.skill)**
- **[调试技能](skills/02-development/debugging/debugging.skill)**
- **[Python 调试](skills/02-development/debugging/debugging-python.skill)**
- **[Vue 调试](skills/02-development/debugging/debugging-vue.skill)**
- **[主动验证](skills/02-development/debugging/verification.skill)**
- **[代码重构](skills/02-development/refactoring/code-refactoring.skill)**
- **[系统性修复](skills/02-development/refactoring/systematic-fix.skill)**
- **[测试原则](skills/02-development/testing/testing-principles.skill)**
- **[Python 测试](skills/02-development/testing/testing-python.skill)**
- **[Vue 测试](skills/02-development/testing/testing-vue.skill)**

#### ✅ 运维技能 (4 个)

- **[运维最佳实践](skills/03-devops/devops-best-practices.skill)**
- **[Git 工作流](skills/03-devops/git-workflow.skill)**
- **[安全最佳实践](skills/03-devops/security-best-practices.skill)**
- **[问题排查](skills/03-devops/troubleshooting.skill)**

#### ✅ API 技能 (2 个)

- **[API 设计](skills/04-api/api-design.skill)**
- **[性能优化](skills/04-api/performance-optimization.skill)**

#### ✅ 文档技能 (1 个)

- **[技术写作](skills/05-documentation/technical-writing.skill)**

#### ✅ 管理技能 (2 个)

- **[需求分析](skills/06-management/requirement-analysis.skill)**
- **[技术选型](skills/06-management/technology-selection.skill)**

**[Skills 索引](skills/README.md)** | **[查看技能详情](skills/)**

---

### 📝 Templates - 模板库

#### ✅ 开发模板 (4 个)

- **[Bug 报告模板](templates/bug-report.md)** - GitHub 实践
- **[PR 模板](templates/pr-template.md)** - GitHub 实践
- **[代码审查模板](templates/code-review.md)** - Google 实践
- **[事故复盘模板](templates/post-mortem.md)** - Atlassian 实践

#### ✅ 设计模板 (2 个)

- **[替代方案分析模板](templates/alternatives-analysis.md)** - 技术决策
- **[监控告警设计模板](templates/monitoring-design.md)** - 监控设计

#### ⏳ 计划中模板

- [ ] 技术方案模板 (待创建)
- [ ] 设计文档模板 (待创建)
- [ ] 决策记录 (ADR) (待创建)
- [ ] API 设计文档 (待创建)
- [ ] Issue 模板 (待创建)
- [ ] 周报模板 (待创建)
- [ ] 一对一模板 (待创建)

**[查看模板详情](templates/)**

---

### 📖 Guides - 指南文档

#### ✅ AI 辅助编程 (4 篇)

- **[AI 辅助编程最佳实践](guides/ai-assisted-programming.md)** ⭐ 必读
- **[AI 进阶技巧](guides/ai-advanced-tips.md)** - 上下文管理、任务拆解
- **[AI 价值分析](guides/ai-value-analysis.md)** - 人类 vs AI 三方对比
- **[OpenClaw 使用指南](guides/openclaw-usage.md)** - 配置、技能、工具

#### ✅ 技术经验 (3 篇)

- **[FastAPI 经验总结](guides/frameworks/fastapi-lessons.md)** - 路由顺序、Mock 技巧
- **[Pytest 测试实践](guides/tools/pytest-practices.md)** - 测试容器、Mock 规范
- **[认证授权陷阱](guides/common-pitfalls/authentication-gotchas.md)** - JWT、Cookie

#### ✅ 设计最佳实践 (1 篇)

- **[Design 文档最佳实践](guides/design-best-practices.md)** - 行业标准、改进建议

**[Guides 索引](guides/README.md)** | **[查看指南详情](guides/)**

---

### 📚 Case Studies - 案例研究

#### ✅ 实战案例 (5 个)

- **[API 路由顺序问题](case-studies/api-route-order-issue.md)** - FastAPI 404 错误
- **[Mock 异步方法问题](case-studies/mock-async-method-issue.md)** - AsyncMock 不能 await
- **[权限验证问题](case-studies/permission-validation-issue.md)** - 依赖注入错误
- **[测试隔离问题](case-studies/test-isolation-issue.md)** - 全局状态污染
- **[响应验证问题](case-studies/response-validation-issue.md)** - FastAPI 响应验证

#### ⏳ 计划中案例

- [ ] ES 查询问题 (待创建)
- [ ] 浏览器缓存问题 (待创建)
- [ ] 系统性修复案例 (待创建)

**[查看案例详情](case-studies/)**

---

### ✅ Checklists - 检查清单

#### ✅ 核心清单 (2 个)

- **[提交前检查清单](checklists/before-commit.md)** - 6 大类 40+ 检查项
- **[代码审查清单](checklists/code-review.md)** - 6 维度审查法

#### ⏳ 计划中清单

- [ ] 任务开始前检查清单 (待创建)
- [ ] 问题排查检查清单 (待创建)
- [ ] 部署前检查清单 (待创建)

**[查看检查清单详情](checklists/)**

---

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
   - 让 AI 先全面自查
   - 再一次性报告所有问题

4. **建设性反馈**
   - 对事不对人
   - 具体可操作
   - 关注改进

### 给 AI 的要求

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

4. **清晰同步**
   - 提供验证步骤
   - 提供预期结果
   - 告知如何排查

---

## 📊 效果对比

### ❌ 低效沟通

```
人类：这里有问题
AI: 修复 A
人类：那里也有问题
AI: 修复 B
人类：还有一个问题
AI: 修复 C
...（10+ 轮）
```

### ✅ 高效沟通

```
人类：这是完整规则（附文档链接）
      请检查所有相关文件
      一次性修复并验证
      
AI: 已全面检查，发现 5 个问题
      已批量修复
      已主动测试验证
      这是验证报告
```

---

## 🔗 相关资源

### 内部资源

- **[Onboarding 仓库](https://github.com/nribeijing/onboarding)** - 新成员融入指南
- **[项目文档仓库](https://github.com/nribeijing/nribeijing-docs)** - 技术文档
- **[Backend 仓库](https://github.com/nribeijing/backend)** - 后端代码
- **[Design 仓库](https://github.com/nribeijing/design)** - 设计文档

### 外部资源

- **[Cursor Rules](https://cursor.sh/docs/rules)** - AI 编程规则
- **[Claude Code Skills](https://claude.ai/code)** - AI 技能系统
- **[Google Code Review](https://google.github.io/eng-practices/)** - Google 工程实践
- **[Atlassian Playbook](https://www.atlassian.com/team-playbook)** - 团队协作指南
- **[OpenClaw 文档](https://docs.openclaw.ai)** - OpenClaw 使用指南

---

## 📈 项目状态

### 完成度统计

| 分类 | 已完成 | 总计 | 完成率 |
|------|--------|------|--------|
| **Guides** | 8 篇 | 15 篇 | 53% |
| **Checklists** | 2 个 | 5 个 | 40% |
| **Agents** | 3 个 | 5 个 | 60% |
| **Rules** | 7 个 | 11 个 | 64% |
| **Skills** | 28 个 | 31 个 | 90% |
| **Templates** | 6 个 | 16 个 | 38% |
| **Case Studies** | 5 个 | 9 个 | 56% |
| **总体** | 59 个 | 92 个 | **64%** |

### 质量评估

| 维度 | 评分 | 说明 |
|------|------|------|
| **内容深度** | 9/10 | 实战经验丰富，案例详细 |
| **实用性** | 9/10 | 可直接使用，有检查清单 |
| **可读性** | 8/10 | 结构清晰，示例丰富 |
| **完整性** | 7/10 | 核心内容完整，缺少部分扩展 |
| **一致性** | 8/10 | 格式统一，风格一致 |
| **平均分** | **8.2/10** | 良好 |

---

## 🤝 贡献指南

欢迎贡献你的经验：

### 贡献流程

1. Fork 本仓库
2. 添加你的规则、技能、模板或案例
3. 提交 PR
4. 团队评审后合并

### 贡献类型

- ✅ **Rules** - 新的场景规则
- ✅ **Skills** - 新的操作流程
- ✅ **Templates** - 新的模板
- ✅ **Case Studies** - 实际案例
- ✅ **Guides** - 指南文档
- ✅ **Checklists** - 检查清单

### 文档标准

- 使用 Markdown 格式
- 遵循现有文档结构
- 包含实际案例或示例
- 添加相关链接

---

## 📝 版本历史

| 版本 | 日期 | 更新内容 |
|------|------|----------|
| v1.0 | 2026-03-14 | 初始版本，包含基础沟通原则、Rules/Skills 系统 |
| v1.1 | 2026-03-15 | 新增 AI 辅助编程系列、Templates、Case Studies |
| v1.2 | 2026-03-15 | 修复所有失效链接，更新项目状态 |

---

## 📊 链接验证

**最后验证**: 2026-03-15  
**验证范围**: 所有内部链接  
**验证结果**: ✅ 全部有效

### 已修复的失效链接

| 原文档 | 失效链接 | 修复后 |
|--------|----------|--------|
| README.md | docs/nonviolent-communication.md | 标记为待创建 |
| README.md | docs/feedback-culture.md | 标记为待创建 |
| README.md | culture/*.md | 标记为待创建 |
| README.md | thinking/*.md | 标记为待创建 |
| README.md | work/*.md | 标记为待创建 |
| README.md | templates/issue-template.md | 标记为待创建 |
| README.md | templates/weekly-report.md | 标记为待创建 |
| README.md | templates/one-on-one.md | 标记为待创建 |
| README.md | case-studies/api-slash-issue.md | 替换为实际案例 |

---

**维护团队**: NRI Beijing Development Team  
**最后更新**: 2026-03-15  
**License**: MIT
