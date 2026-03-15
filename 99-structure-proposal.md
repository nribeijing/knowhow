# KnowHow 目录结构重组方案

> 分析当前问题，提供 3 个改进方案

**创建日期**: 2026-03-15  
**当前文档数**: 71 个

---

## 📊 当前目录结构分析

### 现状

```
knowhow/
├── agents/              (3 个文件)
│   ├── best-practices/  (空)
│   ├── prompts/         (2 个)
│   ├── workflows/       (空)
│   └── README.md
├── case-studies/        (5 个)
├── checklists/          (2 个)
├── docs/                (2 个) ⚠️ 命名太通用
├── examples/            (空) ⚠️ 空目录
├── guides/              (8 个 + 3 个子目录)
│   ├── common-pitfalls/ (1 个)
│   ├── frameworks/      (1 个)
│   ├── languages/       (空)
│   ├── tools/           (1 个)
│   └── README.md
├── rules/               (7 个 + 1 个子目录)
│   ├── general/         (7 个)
│   └── README.md
├── skills/              (28 个 + 6 个子目录) ⚠️ 层级过深
│   ├── 01-architecture/ (6 个)
│   ├── 02-development/  (14 个)
│   ├── 03-devops/       (4 个)
│   ├── 04-api/          (2 个)
│   ├── 05-documentation/(1 个)
│   └── 06-management/   (2 个)
└── templates/           (6 个)
```

---

## ❌ 当前问题

### 1. 分类不清晰

| 问题 | 说明 | 示例 |
|------|------|------|
| **docs 命名太通用** | 不知道里面是什么 | `docs/communication-principles.md` |
| **guides 子目录混乱** | 有些有子目录，有些没有 | `guides/frameworks/` vs `guides/ai-*.md` |
| **agents 定位模糊** | 是工具？还是指南？ | `agents/` 独立存在 |

### 2. 空目录过多

```
agents/best-practices/  (空)
agents/workflows/       (空)
examples/               (空)
guides/languages/       (空)
```

### 3. 层级不一致

```
✅ 扁平：case-studies/*.md
⚠️ 中等：guides/frameworks/*.md
❌ 过深：skills/02-development/best-practices/*.skill
```

### 4. 内容分散

**AI 相关内容分散在 3 个地方**:
```
guides/ai-*.md          (4 篇)
agents/                 (3 个文件)
tools/                  (OpenClaw 使用指南)
```

**模板分散**:
```
templates/              (6 个)
docs/task-description-template.md (1 个)
```

---

## 📋 方案 A: 按内容类型组织 (推荐)

### 核心理念

**按文档类型分类**，适合快速查找"我需要什么类型的文档"

### 目录结构

```
knowhow/
├── 0-START-HERE.md          # 新手指南
├── README.md                 # 项目说明
│
├── 1-guides/                 # 指南文档 (经验总结)
│   ├── README.md
│   ├── ai/                   # AI 辅助编程
│   │   ├── best-practices.md
│   │   ├── advanced-tips.md
│   │   ├── value-analysis.md
│   │   └── openclaw-usage.md
│   ├── workflows/            # 工作流程
│   │   └── industry-workflow.md
│   ├── design/               # 设计最佳实践
│   │   └── design-best-practices.md
│   └── technical/            # 技术经验
│       ├── fastapi-lessons.md
│       ├── pytest-practices.md
│       └── authentication-gotchas.md
│
├── 2-templates/              # 模板库
│   ├── README.md
│   ├── development/          # 开发模板
│   │   ├── bug-report.md
│   │   ├── pr-template.md
│   │   └── code-review.md
│   ├── design/               # 设计模板
│   │   ├── alternatives-analysis.md
│   │   └── monitoring-design.md
│   └── operations/           # 运维模板
│       └── post-mortem.md
│
├── 3-checklists/             # 检查清单
│   ├── README.md
│   ├── before-commit.md
│   └── code-review.md
│
├── 4-rules/                  # 规则系统 (必须遵守)
│   ├── README.md
│   ├── api.md                # API 相关规则
│   ├── code-review.md
│   ├── elasticsearch.md
│   └── git.md
│
├── 5-skills/                 # 技能库 (操作流程)
│   ├── README.md
│   ├── architecture/         # 架构技能
│   ├── development/          # 开发技能
│   ├── devops/               # 运维技能
│   └── soft-skills/          # 软技能
│
├── 6-case-studies/           # 案例研究
│   ├── README.md
│   ├── api-route-order-issue.md
│   ├── mock-async-method-issue.md
│   └── ...
│
└── 7-tools/                  # 工具配置
    ├── README.md
    └── agents/               # AI Agent 配置
        ├── README.md
        └── prompts/
```

### 优点

✅ **查找快速** - 知道类型就能找到  
✅ **结构清晰** - 数字前缀明确顺序  
✅ **易于扩展** - 每个类别独立  
✅ **新人友好** - 0-START-HERE.md 引导  

### 缺点

❌ **迁移成本高** - 需要移动所有文件  
❌ **链接需更新** - 所有内部链接要改  

### 适用场景

- 文档数量 > 100 个
- 团队规模 > 10 人
- 需要频繁查阅

---

## 📋 方案 B: 按使用场景组织

### 核心理念

**按工作流程分类**，适合"我在做 X，需要什么文档"

### 目录结构

```
knowhow/
├── START.md                  # 新手指南
├── README.md
│
├── 01-onboarding/            # 新人入职
│   ├── README.md
│   ├── communication-principles.md
│   └── task-description-template.md
│
├── 02-planning/              # 项目规划
│   ├── README.md
│   ├── alternatives-analysis.md
│   └── industry-workflow.md
│
├── 03-design/                # 技术设计
│   ├── README.md
│   ├── design-best-practices.md
│   ├── monitoring-design.md
│   └── rules/                # 设计相关规则
│       ├── api.md
│       └── elasticsearch.md
│
├── 04-development/           # 开发实现
│   ├── README.md
│   ├── ai-assisted/          # AI 辅助开发
│   │   ├── best-practices.md
│   │   ├── advanced-tips.md
│   │   └── prompts/
│   ├── code-quality/         # 代码质量
│   │   ├── code-review.md
│   │   ├── checklists/
│   │   └── rules/
│   └── testing/              # 测试相关
│       ├── pytest-practices.md
│       └── templates/
│
├── 05-deployment/            # 部署运维
│   ├── README.md
│   ├── post-mortem.md
│   └── monitoring-design.md
│
├── 06-reference/             # 参考资料
│   ├── README.md
│   ├── case-studies/
│   ├── skills/
│   └── value-analysis.md
│
└── 07-tools/                 # 工具配置
    └── README.md
```

### 优点

✅ **场景导向** - 按工作流程查找  
✅ **自然分组** - 相关文档在一起  
✅ **减少跳转** - 一个场景一个目录  

### 缺点

❌ **文档可能重复** - 同一文档属于多个场景  
❌ **边界模糊** - 有些文档不好分类  

### 适用场景

- 团队规模中等 (5-20 人)
- 工作流程标准化
- 强调场景化学习

---

## 📋 方案 C: 混合方案 (平衡型)

### 核心理念

**保留现有结构**，只做必要优化，适合渐进式改进

### 目录结构

```
knowhow/
├── README.md                 # 项目说明 (更新)
├── START-HERE.md            # 新手指南 (新增)
│
├── guides/                   # 指南文档 (保留)
│   ├── README.md            # 更新索引
│   ├── ai/                   # 整合 AI 内容
│   │   ├── best-practices.md
│   │   ├── advanced-tips.md
│   │   ├── value-analysis.md
│   │   └── openclaw-usage.md
│   ├── technical/            # 技术经验
│   │   ├── fastapi-lessons.md
│   │   ├── pytest-practices.md
│   │   └── authentication-gotchas.md
│   ├── workflow/             # 工作流程
│   │   └── industry-workflow.md
│   └── design/               # 设计最佳实践
│       └── design-best-practices.md
│
├── templates/                # 模板库 (保留)
│   ├── README.md
│   └── *.md                 # 所有模板
│
├── checklists/               # 检查清单 (保留)
│   ├── README.md
│   └── *.md
│
├── rules/                    # 规则系统 (保留)
│   ├── README.md
│   └── *.rule               # 扁平化
│
├── skills/                   # 技能库 (保留)
│   ├── README.md
│   └── *.skill              # 扁平化
│
├── case-studies/             # 案例研究 (保留)
│   ├── README.md
│   └── *.md
│
└── tools/                    # 工具配置 (保留 agents)
    ├── README.md
    └── agents/
        ├── README.md
        └── prompts/
```

### 优化点

1. **新增 START-HERE.md** - 新手引导
2. **整合 AI 内容** - guides/ai/ 目录
3. **扁平化 rules/skills** - 减少层级
4. **清理空目录** - 删除 examples 等
5. **更新 README** - 清晰导航

### 优点

✅ **迁移成本低** - 只移动必要文件  
✅ **保留习惯** - 主要结构不变  
✅ **渐进改进** - 可分步实施  

### 缺点

❌ **改进有限** - 结构性问题未解决  
❌ **仍需整理** - 部分分类仍模糊  

### 适用场景

- 文档数量 < 100 个
- 团队已习惯现有结构
- 不想大规模改动

---

## 📊 方案对比

| 维度 | 方案 A (类型) | 方案 B (场景) | 方案 C (混合) |
|------|--------------|--------------|--------------|
| **查找效率** | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐ | ⭐⭐⭐ |
| **迁移成本** | ⭐⭐ | ⭐⭐⭐ | ⭐⭐⭐⭐⭐ |
| **学习曲线** | ⭐⭐⭐⭐ | ⭐⭐⭐ | ⭐⭐⭐⭐⭐ |
| **可扩展性** | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐ | ⭐⭐⭐ |
| **新人友好** | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐ | ⭐⭐⭐ |
| **实施难度** | 高 | 中 | 低 |
| **推荐度** | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐ | ⭐⭐⭐ |

---

## 🎯 推荐方案

### 首选：方案 A (按内容类型)

**理由**:
1. ✅ 结构最清晰
2. ✅ 查找最高效
3. ✅ 最适合扩展
4. ✅ 新人最友好

**实施步骤**:
```
第 1 步：创建新目录结构 (30 分钟)
第 2 步：移动文件 (1 小时)
第 3 步：更新链接 (1 小时)
第 4 步：更新 README (30 分钟)
第 5 步：团队培训 (30 分钟)
总计：3.5 小时
```

### 备选：方案 C (混合方案)

**适用情况**:
- 时间紧张
- 团队不想大改动
- 文档数量增长不快

**实施步骤**:
```
第 1 步：新增 START-HERE.md (30 分钟)
第 2 步：整合 AI 内容 (30 分钟)
第 3 步：清理空目录 (15 分钟)
第 4 步：更新 README (30 分钟)
总计：1.75 小时
```

---

## 📝 实施建议

### 无论选择哪个方案

1. **保留.git 历史** - 使用 git mv 而非手动移动
2. **更新所有链接** - 使用脚本批量更新
3. **团队沟通** - 提前通知变更
4. **过渡期** - 保留旧链接重定向 (如可能)

### 迁移检查清单

- [ ] 备份当前状态
- [ ] 创建新目录结构
- [ ] 移动文件 (git mv)
- [ ] 更新内部链接
- [ ] 更新 README
- [ ] 测试所有链接
- [ ] 团队培训
- [ ] 收集反馈

---

## 🔗 参考案例

### 优秀开源项目

- **[Google eng-practices](https://github.com/google/eng-practices)** - 扁平结构
- **[Microsoft RFC](https://github.com/microsoft/rfcs)** - 按类型分类
- **[Stripe Docs](https://stripe.com/docs)** - 场景化组织

### 最佳实践

1. **深度不超过 3 层**
2. **每个目录 5-15 个文件**
3. **提供清晰的 README**
4. **使用数字前缀排序**
5. **定期清理过时内容**

---

**维护者**: NRI Beijing Development Team  
**创建日期**: 2026-03-15  
**版本**: v1.0
