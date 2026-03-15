# OpenClaw 使用指南

> 配置建议 · 最佳实践 · 效率提升

**创建日期**: 2026-03-15  
**状态**: 实战总结

---

## 📋 目录

- [快速开始](#-快速开始)
- [配置建议](#-配置建议)
- [技能使用](#-技能使用)
- [会话管理](#-会话管理)
- [工具使用](#-工具使用)
- [常见问题](#-常见问题)

---

## 🚀 快速开始

### 1. 首次启动

```bash
# 安装 OpenClaw
pip install openclaw

# 初始化配置
openclaw init

# 启动 Gateway
openclaw gateway start

# 查看状态
openclaw status
```

### 2. 配置渠道

```yaml
# ~/.openclaw/config.yaml
channels:
  feishu:
    enabled: true
    app_id: xxx
    app_secret: xxx
  telegram:
    enabled: false
    bot_token: xxx
```

### 3. 验证安装

```bash
# 发送测试消息
openclaw message send --target "test" --message "Hello"

# 查看日志
openclaw logs
```

---

## ⚙️ 配置建议

### 1. 模型配置

**推荐配置**:
```yaml
# ~/.openclaw/config.yaml
models:
  default: dashscope-coding/qwen3.5-plus  # 编码任务
  reasoning: dashscope/qwen3-max-2026-01-23  # 复杂推理
  vision: dashscope-us/qwen3-vl-plus  # 图像分析
  
# 按需切换
# /model default
# /model reasoning
```

**选择建议**:
| 任务类型 | 推荐模型 | 理由 |
|----------|----------|------|
| 编码开发 | qwen3.5-plus | 代码理解强，速度快 |
| 复杂推理 | qwen3-max | 推理能力强 |
| 图像分析 | qwen3-vl-plus | 视觉模型 |
| 日常对话 | qwen3.5-plus | 平衡性能和成本 |

### 2. 技能配置

**推荐技能**:
```yaml
# ~/.openclaw/config.yaml
skills:
  enabled:
    - searxng  # 隐私搜索
    - weather  # 天气查询
    - healthcheck  # 安全检查
    - skill-creator  # 技能创建
    - skill-vetter  # 技能审查
```

**技能管理**:
```bash
# 查看已安装技能
openclaw skills list

# 安装新技能
clawhub install <skill-name>

# 更新技能
clawhub update

# 卸载技能
clawhub uninstall <skill-name>
```

### 3. 会话配置

**推荐配置**:
```yaml
# ~/.openclaw/config.yaml
sessions:
  max_history: 50  # 最大历史消息数
  timeout: 3600  # 会话超时 (秒)
  auto_save: true  # 自动保存会话
```

---

## 🛠️ 技能使用

### 1. 搜索技能

**searxng 技能**:
```markdown
# 使用示例
请搜索 FastAPI 最佳实践

# 高级用法
请搜索 FastAPI 最佳实践，限制在最近 1 个月
```

**web_search 技能**:
```markdown
# 使用示例
请搜索 Python 异步编程教程

# 指定语言
请用中文搜索 Python 教程
```

### 2. 开发技能

**skill-creator 技能**:
```markdown
# 创建新技能
请帮我创建一个天气查询技能

# 技能结构
- SKILL.md (技能说明)
- scripts/ (脚本文件)
- references/ (参考资料)
```

**skill-vetter 技能**:
```markdown
# 审查技能
请审查这个技能的安全性

# 检查项
- 权限范围
- 可疑代码
- 依赖安全
```

### 3. 系统技能

**healthcheck 技能**:
```markdown
# 安全检查
请执行系统安全检查

# 检查内容
- 防火墙配置
- SSH 安全
- 系统更新
- OpenClaw 配置
```

**weather 技能**:
```markdown
# 天气查询
北京今天天气如何？

# 多日预报
上海未来 3 天天气预报
```

---

## 💬 会话管理

### 1. 会话类型

| 类型 | 用途 | 命令 |
|------|------|------|
| 主会话 | 日常对话 | 默认 |
| 子会话 | 独立任务 | `sessions_spawn` |
| 后台会话 | 长时间任务 | `mode=session` |

### 2. 会话控制

```bash
# 查看会话列表
openclaw sessions list

# 查看会话历史
openclaw sessions history <session-key>

# 发送消息到会话
openclaw sessions send <session-key> --message "xxx"

# 删除会话
openclaw sessions delete <session-key>
```

### 3. 会话最佳实践

**长任务处理**:
```markdown
# 使用子会话
这个任务比较复杂，我创建一个子会话来处理。

# 主会话继续其他工作
# 子会话完成后通知
```

**上下文管理**:
```markdown
# 定期清理会话
/cls  # 清空当前会话

# 重要信息保存到文件
已保存到 memory/2026-03-15.md
```

---

## 🔧 工具使用

### 1. 文件操作

**read 工具**:
```markdown
# 读取文件
请读取 config.yaml 的内容

# 大文件分块读取
请读取大文件，每次 100 行
```

**write 工具**:
```markdown
# 创建文件
请创建 README.md，内容如下...

# 更新文件
请更新配置文件，添加...
```

**edit 工具**:
```markdown
# 精确编辑
请将文件中的"old"替换为"new"

# 注意事项
- oldText 必须完全匹配
- 包括所有空白字符
```

### 2. 命令执行

**exec 工具**:
```markdown
# 执行命令
请运行 git status

# 后台执行
请在后台运行这个长任务
yieldMs: 60000

# 使用 PTY
请使用 PTY 运行这个交互式命令
pty: true
```

**process 工具**:
```markdown
# 查看后台进程
请列出所有后台进程

# 查看进程日志
请查看进程日志，偏移量 100

# 终止进程
请终止这个进程
```

### 3. 浏览器控制

**browser 工具**:
```markdown
# 打开网页
请打开 https://example.com

# 页面快照
请截取页面快照

# 页面操作
请点击"登录"按钮
请输入用户名和密码
```

**使用建议**:
- ✅ 使用 profile="chrome" 接管现有浏览器
- ✅ 使用 refs="aria" 获取稳定引用
- ✅ 操作后等待页面加载完成

---

## 📊 效率提升

### 1. 快捷键

| 快捷键 | 功能 |
|--------|------|
| `/cls` | 清空会话 |
| `/status` | 查看状态 |
| `/model` | 切换模型 |
| `/help` | 查看帮助 |

### 2. 模板使用

**任务模板**:
```markdown
## 任务描述
[清晰描述]

## 相关文件
[文件路径]

## 验收标准
- [ ] 标准 1
- [ ] 标准 2
```

**提交模板**:
```markdown
type: description

- 变更 1
- 变更 2
```

### 3. 自动化

**定时任务**:
```yaml
# crontab 配置
# 每天检查系统健康
0 9 * * * openclaw message send --message "执行健康检查"
```

**自动回复**:
```yaml
# 配置自动回复规则
auto_reply:
  keywords:
    - "你好": "你好！有什么可以帮助你的？"
    - "帮助": "请查看/help 获取帮助"
```

---

## ⚠️ 常见问题

### Q1: 如何选择合适的模型？

**A**: 根据任务类型选择：

| 任务 | 模型 | 理由 |
|------|------|------|
| 编码 | qwen3.5-plus | 代码理解强 |
| 推理 | qwen3-max | 推理能力强 |
| 图像 | qwen3-vl-plus | 视觉模型 |
| 日常 | qwen3.5-plus | 性价比高 |

### Q2: 技能安装失败怎么办？

**A**: 检查以下几点：

1. 网络连接是否正常
2. 技能源是否可访问
3. 权限是否足够
4. 查看错误日志

```bash
# 查看详细日志
openclaw logs --level debug

# 手动安装
cd /path/to/skill
pip install -e .
```

### Q3: 会话历史太长怎么办？

**A**: 有几种方法：

1. **清空会话**: `/cls`
2. **导出历史**: `openclaw sessions export <key>`
3. **限制长度**: 配置 `max_history: 50`
4. **定期清理**: 设置自动清理任务

### Q4: 如何备份配置和数据？

**A**: 备份以下目录：

```bash
# 配置文件
~/.openclaw/config.yaml
~/.openclaw/skills/

# 工作区
~/.openclaw/workspace/

# 会话数据
~/.openclaw/sessions/
```

**备份命令**:
```bash
tar -czf openclaw-backup.tar.gz ~/.openclaw/
```

### Q5: 如何提高响应速度？

**A**: 优化建议：

1. **选择合适模型**: 日常任务用 qwen3.5-plus
2. **减少上下文**: 只提供必要信息
3. **使用缓存**: 启用技能缓存
4. **优化配置**: 调整超时和重试

---

## 📈 最佳实践

### 1. 项目管理

**工作区组织**:
```
workspace/
├── memory/          # 记忆文件
├── skills/          # 自定义技能
├── projects/        # 项目文件
└── SOUL.md         # AI 人格定义
```

**记忆管理**:
```markdown
# 每天创建记忆文件
memory/2026-03-15.md

# 记录重要内容
- 决策
- 上下文
- 待办事项
```

### 2. 安全建议

**权限管理**:
```yaml
# 最小权限原则
security:
  exec: allowlist  # 命令白名单
  network: restricted  # 网络限制
  file: workspace_only  # 仅工作区
```

**敏感信息**:
```markdown
# 不要硬编码敏感信息
❌ API_KEY = "xxx"

# 使用环境变量
✅ API_KEY = os.getenv("API_KEY")
```

### 3. 性能优化

**配置优化**:
```yaml
# 连接池配置
pool:
  max_size: 10
  timeout: 30

# 缓存配置
cache:
  enabled: true
  ttl: 3600
```

**资源管理**:
```bash
# 定期清理
openclaw cleanup

# 查看资源使用
openclaw status --verbose
```

---

## 🔗 相关资源

### 官方文档
- [OpenClaw 文档](https://docs.openclaw.ai)
- [GitHub 仓库](https://github.com/openclaw/openclaw)
- [Discord 社区](https://discord.com/invite/clawd)

### 技能市场
- [ClawHub](https://clawhub.com) - 技能市场
- [官方技能](https://github.com/openclaw/skills)

### 学习资源
- [快速开始指南](https://docs.openclaw.ai/quickstart)
- [配置参考](https://docs.openclaw.ai/config)
- [技能开发](https://docs.openclaw.ai/skills)

---

## 📊 持续改进

### 反馈渠道

1. **GitHub Issues** - 报告问题
2. **Discord** - 社区讨论
3. **文档 PR** - 改进文档

### 版本更新

```bash
# 检查更新
openclaw version --check

# 更新 OpenClaw
pip install --upgrade openclaw

# 查看变更
openclaw changelog
```

---

**维护者**: NRI Beijing Development Team  
**创建日期**: 2026-03-15  
**最后更新**: 2026-03-15  
**版本**: v1.0
