# AUR Skills

> **[English](README.md) · [中文](README.zh.md)**

一组面向 Arch Linux AUR 软件包开发的 [Agent Skills](https://agentskills.io)——涵盖 PKGBUILD 编写、审查、构建、提交和维护。可与任何支持读取 Markdown skill 文件的 LLM agent 配合使用（GitHub Copilot、Cursor、Claude Code、Codex、OpenCode、Gemini CLI 等）。

> 派生自 [pahheb-skills](https://github.com/Pahheb/pahheb-skills) —— 针对 Arch Linux AUR 软件包开发进行了适配。

## Skills

目录遵循官方 Agent Skills / `gh skill` 约定（`skills/*/SKILL.md`）。

### aur-guides (Master Dispatcher)

将请求路由到专门的 sub-skill 以处理各类 AUR 任务。

| Sub-skill | Purpose |
|-----------|---------|
| **aur-pkgbuild** | PKGBUILD 创建与语法 |
| **aur-package-guidelines** | Arch Linux 打包规范 |
| **aur-submission** | AUR 提交与维护 |
| **aur-audit** | 包审查与验证 |
| **aur-makepkg** | 构建流程配置 |
| **aur-vcs-packages** | 版本控制系统（-git/-svn/-hg）软件包 |
| **aur-pacman** | Pacman 使用指南 |
| **aur-helpers** | AUR helper 工具（yay、paru） |
| **aur-rpc** | AUR Web RPC 接口（搜索、信息查询、元数据存档） |

## Installation

### 推荐：`gh skill`（官方）

需要 [GitHub CLI](https://cli.github.com/) v2.90.0+（含 public preview 的 `gh skill`）。

```bash
# 发现
gh skill search aur --owner enihcam
gh skill preview enihcam/aur-skills aur-guides

# 安装全部（推荐：dispatcher + 全部子 skill）
gh skill install enihcam/aur-skills --all --agent cursor --scope user

# 或只装 dispatcher / 单个 skill
gh skill install enihcam/aur-skills aur-guides --agent cursor --scope user
gh skill install enihcam/aur-skills aur-pkgbuild --agent claude-code --scope user

# 钉到某个 release
gh skill install enihcam/aur-skills --all --agent cursor --scope user --pin v2.0.0

# 之后更新
gh skill update --all
```

按你的 agent 改 `--agent`（`github-copilot`、`claude-code`、`cursor`、`codex`、`opencode`、`gemini-cli` 等）。`--scope project` 会装到当前仓库共享的 `.agents/skills`。

### One-step（任意 agent）

复制仓库 URL 并粘贴给你的 agent：

```
https://github.com/enihcam/aur-skills
```

让它安装 `skills/` 下的内容（建议全装，或至少 `aur-guides` 加上你需要的子 skill）。

### Manual（symlink）

克隆到**持久目录**（不要用 `/tmp`——很多环境是 tmpfs，重启后清空），再把 skills 软链到 agent 的 skills 目录：

```bash
git clone https://github.com/enihcam/aur-skills.git ~/.local/share/aur-skills
git -C ~/.local/share/aur-skills checkout v2.0.0   # 或 main
mkdir -p <install-path>
# dispatcher + 全部子 skill 平铺（等同 gh skill --all）
for s in ~/.local/share/aur-skills/skills/*; do
  ln -sfn "$s" "<install-path>/$(basename "$s")"
done
```

| Tool | Install Path（存放各 skill 目录的父目录） |
| :--- | :--- |
| OpenCode | `~/.config/opencode/skills/` |
| Claude Code | `~/.claude/skills/` |
| Gemini CLI | `~/.gemini/skills/` |
| Codex CLI | `~/.agents/skills/` |
| Cursor | `~/.cursor/skills/` |
| Windsurf | `~/.codeium/windsurf/skills/` |
| Hermes | `~/.hermes/skills/` |

之后更新：`git -C ~/.local/share/aur-skills fetch --tags && git -C ~/.local/share/aur-skills checkout v2.0.0`（或 `gh skill update --all`）。

**从 v1.x 迁移：** 树从仓库根 `aur-guides/` 挪到 `skills/*/`。旧的 `…/aur-skills/aur-guides` 软链会失效；改为从 `…/aur-skills/skills/…` 重链，或用 `gh skill` 重装。

**OpenCode 注意：**

- 优先把软链放到 `~/.config/opencode/skills/` 下。若在 `skills` 数组里直接指向该目录之外的 clone，可能每次加载 skill 都弹出访问权限确认。
- **不要**把本仓库写成 OpenCode `plugin`（`aur-skills@git+…`）。plugin 按 npm 包安装，本仓库没有 `package.json`，启动会失败。

Per-project（OpenCode）：

```bash
git clone https://github.com/enihcam/aur-skills.git ~/.local/share/aur-skills
mkdir -p .opencode/skills
for s in ~/.local/share/aur-skills/skills/*; do
  ln -sfn "$s" ".opencode/skills/$(basename "$s")"
done
```

## Usage

```
用 @aur-guides 帮我为项目创建 PKGBUILD
用 @aur-pkgbuild 为 my-app 编写 PKGBUILD
用 @aur-submission 将我的包提交到 AUR
```

`aur-guides` 是 master dispatcher——会按任务路由到对应 sub-skill。请一并安装相关子 skill（或使用 `--all`），否则路由目标不存在。

## Publishing（维护者）

```bash
gh skill publish --dry-run
gh skill publish --tag vX.Y.Z
```

会按 Agent Skills 规范校验、确保仓库有 `agent-skills` topic，并创建 GitHub Release。

## Requirements

- 支持读取 skill 文件的 LLM agent，或带 `gh skill` 的 GitHub CLI
- 提交到 AUR 需要 [AUR 账户](https://aur.archlinux.org) 并上传 SSH 密钥

## License

MIT
