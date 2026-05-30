# Contributing to My Skills

感谢你考虑为这个项目做贡献！

## 如何贡献

### 报告问题

如果你发现了 bug 或有功能建议：

1. 先搜索 [Issues](https://github.com/FuNgaiKa/my-skills/issues) 看是否已有相关讨论
2. 如果没有，创建新 issue，清楚描述：
   - 问题现象或功能需求
   - 复现步骤（如果是 bug）
   - 期望行为
   - 你的环境（runtime、版本等）

### 提交 Skill

欢迎提交新的 skill 或改进现有 skill！

#### Skill 质量标准

**内容转换类 skill**（`.claude/commands/`）：
- [ ] 有清晰的 YAML frontmatter（name、description）
- [ ] 风格要求具体可执行
- [ ] 有输出格式说明
- [ ] 有禁止项列表

**工作流类 skill**（`.claude/skills/`）：
- [ ] 有完整的 YAML frontmatter（name、description、user-invocable、allowed-tools）
- [ ] 工作流分步骤清晰（Step 1、Step 2...）
- [ ] 每个步骤有明确的输入输出
- [ ] 有错误处理说明
- [ ] 有示例用法

**思维视角类 skill**（`.claude/skills/*-perspective/`）：
- [ ] 有调研来源（`references/research/`）
- [ ] 心智模型有证据支撑
- [ ] 有诚实边界说明
- [ ] 有表达 DNA 定义
- [ ] 有质量验证测试

#### 提交流程

1. **Fork 这个仓库**

2. **创建分支**
   ```bash
   git checkout -b feature/your-skill-name
   ```

3. **添加你的 skill**
   - 内容转换类：`.claude/commands/your-skill.md`
   - 工作流类：`.claude/skills/your-skill/SKILL.md`

4. **更新文档**
   - 在 `README.md` 的 Skill 列表中添加你的 skill
   - 在 `SKILLS.md` 中添加详细说明

5. **测试**
   ```bash
   # 在 Claude Code 或其他 runtime 中测试
   /your-skill 测试内容
   ```

6. **提交**
   ```bash
   git add .
   git commit -m "feat: add [skill-name] skill"
   ```

7. **推送并创建 Pull Request**
   ```bash
   git push origin feature/your-skill-name
   ```

### Commit 规范

使用语义化提交信息：

- `feat:` 新功能
- `fix:` Bug 修复
- `docs:` 文档更新
- `style:` 代码格式（不影响功能）
- `refactor:` 重构
- `test:` 测试相关
- `chore:` 构建/工具相关

示例：
```
feat: add naval-ravikant perspective skill
fix: xiaohongshu skill output format
docs: update installation guide
```

### 代码风格

**Markdown 文件**：
- 使用中文时，中英文之间加空格
- 代码块指定语言
- 链接使用相对路径（仓库内）或完整 URL（外部）

**YAML frontmatter**：
- 使用 2 空格缩进
- description 使用 `|` 多行格式
- allowed-tools 按字母排序

**Skill 内容**：
- 段落之间空一行
- 使用 `##` 作为主要标题
- 表格对齐
- 代码示例使用 ` ``` ` 包裹

## 行为准则

- 尊重所有贡献者
- 建设性反馈
- 专注于问题本身，不针对个人
- 欢迎新手提问

## 许可证

提交代码即表示你同意将代码以 MIT 许可证开源。

## 问题？

有任何问题可以：
- 创建 [Issue](https://github.com/FuNgaiKa/my-skills/issues)
- 在 Pull Request 中讨论

感谢你的贡献！🎉
