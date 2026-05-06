# SKILLS

个人跨机器同步的 AI 工具配置仓库，主要承载 Agent Skills，并预留 Cursor 专属 Slash Commands、Hooks 的位置。

## 目录结构

```
SKILLS/
├── skills/         # 通用 Agent Skills（遵循 agentskills.io 开放标准，跨工具复用）
│   └── <skill-name>/
│       └── SKILL.md
├── commands/       # Cursor 专属 Slash Commands（预留；优先把命令做成带 disable-model-invocation 的 skill）
├── hooks/          # Hooks 脚本（预留）
└── README.md
```

## 当前 Skills 列表

| 名称 | 触发方式 | 说明 |
| ---- | -------- | ---- |
| `init-repo` | 显式输入 `/init-repo`（带 `disable-model-invocation: true`，模型不会自动调用） | 初始化项目仓库：创建 `.gitignore` / `.cursorignore` 与 `docs/` `ref/` `data/` `output/` 等默认目录 |

## 当前 Commands 列表

> 暂无。一般场景请优先把命令做成带 `disable-model-invocation: true` 的 Skill —— 这样**既能像 slash command 那样显式调用**，又能跨工具复用（Cursor / Claude Code / Codex 等）。

只有在 Skill 形态实在表达不了、且只服务于 Cursor 的命令，才放进 `commands/`。

## 在新机器上恢复（Windows）

> 前提：已安装 Git，并已配置好 GitHub 凭证（PAT 或 SSH）。

```powershell
# 1. 克隆到任意位置（这里以 E:\AI\SKILLS\SKILLS 为例）
git clone https://github.com/Zifeng-Jiang/SKILLS.git E:\AI\SKILLS\SKILLS

# 2. 用 Junction 把仓库内容挂载到 Cursor 个人配置目录
#    注意：~/.cursor/skills 和 ~/.cursor/commands 必须不存在或为空
mklink /J "%USERPROFILE%\.cursor\skills"   "E:\AI\SKILLS\SKILLS\skills"
mklink /J "%USERPROFILE%\.cursor\commands" "E:\AI\SKILLS\SKILLS\commands"
```

> 如果 `~/.cursor/commands` 已经存在并含有文件，先把里面的内容**移到仓库的 `commands/` 下**（手动 `git add / commit / push`），删除空目录后再创建 Junction。

## 在新机器上恢复（macOS / Linux）

```bash
git clone https://github.com/Zifeng-Jiang/SKILLS.git ~/code/SKILLS

ln -s ~/code/SKILLS/skills   ~/.cursor/skills
ln -s ~/code/SKILLS/commands ~/.cursor/commands
```

## 工作机制

通过 Junction（Windows）/ 符号链接（Unix），Cursor 看到的 `~/.cursor/skills/` 和 `~/.cursor/commands/` 实际指向本仓库。

- 在 Cursor 内编辑 SKILL.md / 命令文件 → 等同于编辑 git 仓库 → `git push` 即同步至 GitHub
- 其他机器 `git pull` 即可拉取最新内容
- 一份源、多机共享，无需手动复制

## 注意事项

- **不要**用本仓库覆盖 `~/.cursor/skills-cursor/`（Cursor 内置 skills 由系统管理）
- **不要**把含密钥的文件（如 `mcp.json` 中的 token）提交进来
- 仓库为私有仓库，仅限同账号访问
