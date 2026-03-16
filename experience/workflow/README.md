# Workflow - 工作流程

> 从需求到运维的完整流程指南

**特点**: 流程化、可执行、AI 辅助

---

## 📋 流程总览

```
Requirement → Design → Develop → Testing → Ops
```

| 阶段 | 说明 | 关键产出 |
|------|------|----------|
| **[需求分析](#需求分析)** | 理解需求，明确目标 | 需求文档 + 验收标准 |
| **[设计](#设计)** | 技术方案，架构设计 | Design Doc + API 规范 |
| **[开发](#开发)** | 代码实现，测试编写 | 代码 + 单元测试 |
| **[测试](#测试)** | 质量保证，问题发现 | 测试报告 + 发布说明 |
| **[运维](#运维)** | 稳定运行，持续改进 | 监控报告 + 改进措施 |

---

## 📖 核心文档

### 流程指南

| 文档 | 说明 | 使用场景 |
|------|------|----------|
| **[development-workflow.md](./development-workflow.md)** | 完整工作流程 | 了解整体流程 |
| **requirement-phase.md** | 需求阶段指南 | 需求分析时 (待创建) |
| **design-phase.md** | 设计阶段指南 | 技术设计时 (待创建) |
| **development-phase.md** | 开发阶段指南 | 编码实现时 (待创建) |
| **testing-phase.md** | 测试阶段指南 | 测试执行时 (待创建) |
| **ops-phase.md** | 运维阶段指南 | 运维监控时 (待创建) |

---

## 🎯 关键实践

### 设计评审 (Design Review) 🔴 P0

**为什么重要**:
- 预防架构问题
- 发现设计缺陷
- 统一技术方案

**如何执行**:
1. 编写 Design Doc (按 [设计文档模板](../templates/design-doc-template.md))
2. 提前 24 小时发送给评审者
3. 召开评审会议 (30-60 分钟)
4. 按 [设计评审清单](../checklists/design-review.md) 审查
5. 修改后批准实施

**AI 辅助**:
- AI 按模板编写 Design Doc
- AI 按清单预审查
- AI 记录评审意见

---

### 需求确认 (Requirement Confirmation) 🔴 P0

**为什么重要**:
- 避免理解偏差
- 减少返工
- 明确验收标准

**如何执行**:
1. 阅读需求文档
2. 按 [开发前需求确认清单](../checklists/before-development.md) 逐项确认
3. 与产品经理对齐
4. 与技术负责人确认方案

**AI 辅助**:
- AI 按清单确认需求
- AI 输出需求理解
- AI 识别风险点

---

### 代码审查 (Code Review) 🟡 P1

**为什么重要**:
- 保证代码质量
- 知识共享
- 发现潜在问题

**如何执行**:
1. 创建 Pull Request
2. 按 [提交前检查清单](../checklists/before-commit.md) 自查
3. 评审者按 [代码审查清单](../checklists/code-review.md) 审查
4. 修改后合并

**AI 辅助**:
- AI 按清单自查
- AI 准备 PR 描述
- AI 回答审查问题

---

## 📊 流程执行状态

| 环节 | 状态 | 文档 | 清单 |
|------|------|------|------|
| **需求确认** | ✅ 已实施 | [任务描述模板](../templates/task-description-template.md) | [before-development.md](../checklists/before-development.md) |
| **设计评审** | ✅ 已实施 | [设计文档模板](../templates/design-doc-template.md) | [design-review.md](../checklists/design-review.md) |
| **代码审查** | ✅ 已实施 | - | [before-commit.md](../checklists/before-commit.md) |
| **测试执行** | 🟡 部分实施 | - | 待创建 |
| **监控复盘** | ❌ 未实施 | - | 待创建 |

---

## 🤖 AI 执行指南

### AI 如何参与流程？

**需求阶段**:
```
1. 阅读需求文档
2. 按 before-development.md 确认需求
3. 输出需求理解
4. 识别风险点
```

**设计阶段**:
```
1. 按 design-doc-template.md 编写 Design Doc
2. 提供替代方案分析
3. 按 design-review.md 自查
4. 准备评审材料
```

**开发阶段**:
```
1. 按设计文档编码
2. 编写单元测试
3. 按 before-commit.md 自查
4. 准备 PR
```

**测试阶段**:
```
1. 运行自动化测试
2. 记录测试结果
3. 分析失败原因
4. 修复问题
```

**运维阶段**:
```
1. 监控关键指标
2. 响应告警
3. 分析问题根因
4. 提出改进措施
```

---

## 📚 相关资源

### 模板库
- [任务描述模板](../templates/task-description-template.md)
- [设计文档模板](../templates/design-doc-template.md)
- [PR 模板](../templates/pr-template.md)
- [事故复盘模板](../templates/post-mortem.md) (待创建)

### 检查清单
- [开发前需求确认清单](../checklists/before-development.md)
- [提交前检查清单](../checklists/before-commit.md)
- [代码审查清单](../checklists/code-review.md)
- [设计评审清单](../checklists/design-review.md)

### 最佳实践
- [AI 辅助编程最佳实践](../guides/ai/ai-assisted-programming.md)
- [常见陷阱](../guides/ai/common-pitfalls.md)

---

**维护者**: NRI Beijing Development Team  
**创建日期**: 2026-03-16  
**最后更新**: 2026-03-16  
**版本**: v1.0
