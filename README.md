# 🧠 KnowHow - LLM 协作与团队最佳实践

> 提升人与 LLM、人与人之间的协作效率，融合全球优秀团队实践

## 📋 项目使命

汇集全球优秀 IT 团队的最佳实践，帮助团队：
- ✅ 与 LLM 更高效协作
- ✅ 建立卓越工程师文化
- ✅ 提升团队沟通质量
- ✅ 持续学习和成长

## 🎯 适用场景

- AI 辅助编程（Cursor、Claude Code 等）
- 团队协作与沟通
- 代码审查与质量保障
- 知识管理与传承
- 团队文化建设
- 新成员融入

## 📚 核心内容

### 1. 沟通原则 (Communication)

- **[沟通原则](docs/communication-principles.md)** ⭐ 必读
- **[任务描述模板](docs/task-description-template.md)**
- **[非暴力沟通指南](docs/nonviolent-communication.md)**
- **[反馈文化](docs/feedback-culture.md)**

### 2. Rules - 规则系统 📜

参考 Cursor Rules 设计理念，针对特定场景的强制性规则。

#### 通用规则
- **[API 尾部斜杠规则](rules/general/api-slash.rule)** - FastAPI 斜杠规范
- **[ES 查询规则](rules/general/elasticsearch-query.rule)** - Elasticsearch 查询规范
- **[代码审查规则](rules/general/code-review.rule)** - Google 代码审查实践
- **[Git 提交规则](rules/general/git-commit.rule)** - 提交信息规范

#### 技术栈规则
- **[Vue3 规则](rules/tech/vue3.rule)** - Vue3 开发规范
- **[FastAPI 规则](rules/tech/fastapi.rule)** - FastAPI 开发规范
- **[TypeScript 规则](rules/tech/typescript.rule)** - TypeScript 使用规范
- **[Python 规则](rules/tech/python.rule)** - Python 编码规范

### 3. Skills - 技能系统 🛠️

参考 Claude Code Skills 设计理念，可复用的标准化操作流程。

#### 开发技能
- **[系统性修复](skills/dev/systematic-fix.skill)** - 避免零散修复
- **[主动验证](skills/comm/verification.skill)** - 修复后主动测试
- **[代码审查](skills/dev/code-review.skill)** - Google 实践
- **[调试技能](skills/dev/debugging.skill)** - 系统化调试

#### 沟通技能
- **[任务分析](skills/comm/task-analysis.skill)** - 需求理解
- **[清晰汇报](skills/comm/reporting.skill)** - 进展汇报
- **[非暴力沟通](skills/comm/nvc.skill)** - NVC 实践

#### 运维技能
- **[健康检查](skills/devops/health-check.skill)** - 服务验证
- **[日志分析](skills/devops/log-analysis.skill)** - 问题排查
- **[部署流程](skills/devops/deploy-basic.skill)** - 标准部署

### 4. 团队实践 (Team Practices)

#### 文化层面
- **[心理安全指南](culture/psychological-safety.md)** - Google 实践
- **[成长型思维](culture/growth-mindset.md)** - Carol Dweck 理论
- **[工程师文化](culture/engineering-culture.md)** - Netflix/Google
- **[创新文化](culture/innovation-culture.md)** - 3M/Apple
- **[仆人式领导](culture/servant-leadership.md)** - Greenleaf 理论
- **[学习组织](culture/learning-organization.md)** - Peter Senge

#### 思维层面
- **[系统思考](thinking/systems-thinking.md)** - 看到整体
- **[精益思维](thinking/lean-thinking.md)** - MVP 与快速迭代
- **[设计思维](thinking/design-thinking.md)** - IDEO 方法
- **[产品思维](thinking/product-thinking.md)** - 解决问题而非功能
- **[刻意练习](thinking/deliberate-practice.md)** - 技能提升

#### 工作层面
- **[深度工作](work/deep-work.md)** - Cal Newport 理论
- **[远程协作](work/remote-work.md)** - GitLab/Buffer 实践
- **[敏捷实践](work/agile-practice.md)** - Scrum/Kanban
- **[知识管理](work/knowledge-management.md)** - 知识沉淀与分享

### 5. 模板库 (Templates)

#### 开发模板
- **[Bug 报告模板](templates/bug-report.md)** - GitHub 实践
- **[PR 模板](templates/pr-template.md)** - GitHub 实践
- **[Issue 模板](templates/issue-template.md)** - GitHub 实践
- **[代码审查模板](templates/code-review.md)** - Google 实践

#### 文档模板
- **[技术方案模板](templates/tech-proposal.md)** - Amazon 六页纸
- **[设计文档模板](templates/design-doc.md)** - Microsoft 实践
- **[决策记录 (ADR)](templates/adr.md)** - 架构决策记录
- **[API 设计文档](templates/api-design.md)** - Stripe 实践

#### 复盘模板
- **[事故复盘模板](templates/post-mortem.md)** - Atlassian 实践
- **[Sprint 回顾模板](templates/sprint-retro.md)** - Scrum 实践
- **[项目复盘模板](templates/project-retro.md)** - 项目总结

#### 沟通模板
- **[周报模板](templates/weekly-report.md)** - 进展汇报
- **[小队周报模板](templates/squad-report.md)** - Spotify 实践
- **[一对一模板](templates/one-on-one.md)** - 管理沟通

### 6. 案例研究 (Case Studies)

- **[API 斜杠问题](case-studies/api-slash-issue.md)** - 10+ 轮修复的教训
- **[ES 查询问题](case-studies/es-keyword-issue.md)** - 字段类型导致的空数据
- **[浏览器缓存问题](case-studies/browser-cache.md)** - 缓存导致的验证困难
- **[系统性修复案例](case-studies/systematic-fix-example.md)** - 正确做法示例

### 7. 检查清单 (Checklists)

- **[任务开始前检查清单](checklists/before-task.md)**
- **[代码提交前检查清单](checklists/before-commit.md)**
- **[代码审查检查清单](checklists/code-review.md)**
- **[问题排查检查清单](checklists/troubleshooting.md)**
- **[部署前检查清单](checklists/before-deploy.md)**

## 🚀 快速开始

### 与 LLM 协作

```markdown
1. 阅读 [沟通原则](docs/communication-principles.md)
2. 使用 [任务描述模板](docs/task-description-template.md)
3. 指定适用的 Rules 和 Skills
4. 参考案例研究避免常见错误
```

### 新任务开始

```markdown
1. 填写 [任务描述模板](templates/task-description.md)
2. 指定适用的 Rules
3. 选择要使用的 Skills
4. 按检查清单验证
```

### 代码审查

```markdown
1. 使用 [代码审查模板](templates/code-review.md)
2. 遵循 [代码审查规则](rules/general/code-review.rule)
3. 应用 [非暴力沟通](docs/nonviolent-communication.md)
4. 使用 [检查清单](checklists/code-review.md)
```

### 事故复盘

```markdown
1. 使用 [事故复盘模板](templates/post-mortem.md)
2. 遵循无责备原则
3. 分析根本原因（5 Why）
4. 制定改进措施
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

## 🔗 相关资源

### 内部资源
- **[Onboarding 仓库](https://github.com/nribeijing/onboarding)** - 新成员融入指南
- **[项目文档仓库](https://github.com/nribeijing/nribeijing-docs)** - 技术文档

### 外部资源
- **[Cursor Rules](https://cursor.sh/docs/rules)** - AI 编程规则
- **[Claude Code Skills](https://claude.ai/code)** - AI 技能系统
- **[Google Code Review](https://google.github.io/eng-practices/)** - Google 工程实践
- **[Atlassian Playbook](https://www.atlassian.com/team-playbook)** - 团队协作指南

## 📈 版本历史

| 版本 | 日期 | 更新内容 |
|------|------|---------|
| v1.0 | 2026-03-14 | 初始版本，包含基础沟通原则、Rules/Skills 系统、团队最佳实践 |

## 🤝 贡献指南

欢迎贡献你的经验：

1. Fork 本仓库
2. 添加你的规则、技能、模板或案例
3. 提交 PR
4. 团队评审后合并

### 贡献类型

- **Rules** - 新的场景规则
- **Skills** - 新的操作流程
- **Templates** - 新的模板
- **Case Studies** - 实际案例
- **Guides** - 指南文档
- **Checklists** - 检查清单

---

**维护团队**: NRI Beijing Development Team  
**最后更新**: 2026-03-14  
**License**: MIT
