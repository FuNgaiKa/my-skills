# 文档索引

## 快速开始

- [README.md](../README.md) - 项目主页和快速开始
- [SKILLS.md](../SKILLS.md) - 所有 skill 的详细文档

## 贡献指南

- [CONTRIBUTING.md](../CONTRIBUTING.md) - 如何贡献代码和 skill

## 使用示例

- [examples/](../examples/) - 各个 skill 的实际使用示例

## Skill 开发

### 创建新 Skill

#### 1. 内容转换类 Skill

适合：单步转换，输入→输出

**位置**：`.claude/commands/your-skill.md`

**模板**：
```markdown
---
name: your-skill
description: 简短描述这个skill做什么
---

# Skill 说明

## 风格要求
- 要求1
- 要求2

## 禁止
- 禁止项1
- 禁止项2

## 输出
输出格式说明

## 用户输入
$ARGUMENTS
```

#### 2. 工作流类 Skill

适合：多步骤，包含数据获取、处理、输出

**位置**：`.claude/skills/your-skill/SKILL.md`

**模板**：
```markdown
---
name: your-skill
description: 描述
user-invocable: true
allowed-tools:
  - WebSearch
  - Read
  - Write
---

# /your-skill — 工作流说明

触发后，自动完成以下全流程。

Arguments passed: `$ARGUMENTS`

---

## Step 1: 第一步

说明...

## Step 2: 第二步

说明...

## Step 3: 输出

说明...
```

#### 3. 思维视角类 Skill

适合：蒸馏某个人的思维方式

**位置**：`.claude/skills/person-name-perspective/SKILL.md`

**使用女娲 skill 生成**：
```
/huashu-nuwa 蒸馏[人名]
```

### Skill 质量检查清单

提交前检查：

- [ ] YAML frontmatter 格式正确
- [ ] description 清晰描述功能
- [ ] 有具体的使用示例
- [ ] 有错误处理说明（工作流类）
- [ ] 有输出格式说明
- [ ] 在 Claude Code 中测试通过
- [ ] 更新了 README.md 和 SKILLS.md

## 技术参考

### Agent Skills 协议

- [官方文档](https://github.com/vercel-labs/skills)
- [协议规范](https://github.com/vercel-labs/skills/blob/main/PROTOCOL.md)

### 兼容的 Runtime

- Claude Code
- Codex
- Cursor
- OpenClaw
- Hermes Agent
- CodeBuddy
- Workbuddy
- Gemini CLI
- OpenCode
- [完整列表](https://github.com/vercel-labs/skills#compatible-runtimes)

## 常见问题

### Q: Skill 不生效？

A: 检查：
1. 文件是否在 `.claude/` 目录下
2. YAML frontmatter 格式是否正确
3. 调用方式是否匹配

### Q: 如何调试 Skill？

A:
1. 在 skill 中添加输出步骤
2. 使用 `Read` 工具查看生成的文件
3. 检查 allowed-tools 是否包含需要的工具

### Q: 可以在其他 AI agent 中使用吗？

A: 可以。基于 Agent Skills 协议，支持 50+ runtime。

## 相关资源

- [女娲 Nuwa](https://github.com/alchaincyf/nuwa-skill) - 蒸馏任何人的思维方式
- [同事.skill](https://github.com/alchaincyf/colleague-skill) - 蒸馏离职同事的工作习惯
- [Agent Skills](https://github.com/vercel-labs/skills) - 开放的 skill 协议
