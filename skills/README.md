# 🛠️ Skills - 技能库

> 参考 Claude Code Skills 的设计理念，为 IT 项目开发场景设计的可复用技能集合

---

## 🎯 什么是 Skills？

Skills 是 AI 可以执行的**标准化操作流程**和**最佳实践集合**。

### 特点

- ✅ **可复用** - 跨项目、跨场景使用
- ✅ **模块化** - 独立的技能单元
- ✅ **可组合** - 多个 skills 可以组合使用
- ✅ **可验证** - 有明确的验收标准

### 与 Rules 的区别

| 特性 | Rules | Skills |
|------|-------|--------|
| **定位** | 应该遵循的规则 | 可以执行的能力 |
| **强制性** | 高（必须遵守） | 中（按需使用） |
| **范围** | 具体场景 | 通用能力 |
| **示例** | API 命名规则 | Git 操作技能 |

---

## 📁 Skills 分类

### 1. 开发技能 (Development)

- **[git-basic.skill](skills/dev/git-basic.skill)** - Git 基础操作
- **[code-review.skill](skills/dev/code-review.skill)** - 代码审查
- **[debugging.skill](skills/dev/debugging.skill)** - 调试技能
- **[testing.skill](skills/dev/testing.skill)** - 单元测试

### 2. 运维技能 (DevOps)

- **[deploy-basic.skill](skills/devops/deploy-basic.skill)** - 基础部署
- **[health-check.skill](skills/devops/health-check.skill)** - 健康检查
- **[log-analysis.skill](skills/devops/log-analysis.skill)** - 日志分析

### 3. 沟通技能 (Communication)

- **[task-analysis.skill](skills/comm/task-analysis.skill)** - 任务分析
- **[verification.skill](skills/comm/verification.skill)** - 主动验证
- **[reporting.skill](skills/comm/reporting.skill)** - 进展汇报

---

## 📝 Skill 文件格式

### 基本结构

```markdown
# Skill: 技能名称

## 技能描述
（这个技能是做什么的）

## 适用场景
（什么时候使用这个 skill）

## 执行步骤
1. 步骤 1
2. 步骤 2
3. 步骤 3

## 检查清单
- [ ] 检查项 1
- [ ] 检查项 2

## 示例
（使用示例）

## 相关技能
- [相关 skill 1](./related-1.skill)
- [相关 skill 2](./related-2.skill)

## 版本历史
- v1.0 - 初始版本
```

---

## 🚀 使用方式

### 1. 任务中指定 Skills

```markdown
任务：修复 Bug

请使用以下 Skills：
- [debugging.skill](./skills/dev/debugging.skill)
- [verification.skill](./skills/comm/verification.skill)
```

### 2. AI 自动应用

AI 根据任务类型自动选择适用的 Skills。

### 3. 组合使用

```markdown
复杂任务：
1. [task-analysis.skill](./skills/comm/task-analysis.skill) - 分析任务
2. [debugging.skill](./skills/dev/debugging.skill) - 调试问题
3. [code-review.skill](./skills/dev/code-review.skill) - 审查代码
4. [verification.skill](./skills/comm/verification.skill) - 验证修复
5. [reporting.skill](./skills/comm/reporting.skill) - 汇报结果
```

---

## 💡 示例 Skill

### 系统性修复技能

```markdown
# Skill: 系统性修复

## 技能描述
避免零散修复，采用系统性的方法解决问题。

## 执行步骤

1. **全面检查**
   - 检查所有相关文件
   - 列出所有潜在问题
   - 分析问题根本原因

2. **批量修复**
   - 一次性修复所有问题
   - 保持代码风格一致
   - 更新相关文档

3. **主动验证**
   - 本地测试验证
   - 检查边界情况
   - 确认无副作用

4. **清晰汇报**
   - 列出修复的问题
   - 提供验证报告
   - 告知注意事项

## 检查清单

- [ ] 已检查所有相关文件
- [ ] 已列出所有潜在问题
- [ ] 已批量修复
- [ ] 已主动测试验证
- [ ] 已清晰汇报

## 示例

### ❌ 错误做法
```
人类：A 有问题
AI: 修复 A
人类：B 也有问题
AI: 修复 B
...（10+ 轮）
```

### ✅ 正确做法
```
AI: 已全面检查，发现 5 个问题
      已批量修复
      已测试验证
      这是验证报告
```
```

---

## 📊 效果对比

### 没有 Skills

```
人类：修一下这个 Bug
AI: （随意修复）
人类：还有问题
AI: （再次修复）
人类：怎么又有问题
...（多轮）
```

### 使用 Skills

```
人类：修一下这个 Bug
AI: 使用 [debugging.skill] + [verification.skill]
      1. 分析根本原因
      2. 系统性修复
      3. 主动验证
      4. 汇报结果
```

---

## 🔗 相关资源

- [Rules 系统](../rules/README.md)
- [沟通原则](../docs/communication-principles.md)
- [Claude Code Skills](https://claude.ai/code)

---

**维护**: NRI Beijing Development Team  
**版本**: v1.0  
**最后更新**: 2026-03-14
