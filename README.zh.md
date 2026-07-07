# AUR Skills

> **[English](README.md) · [中文](README.zh.md)**

一组面向 Arch Linux AUR 软件包开发的 instruction sets——涵盖 PKGBUILD 编写、审查、构建、提交和维护。可与任何支持读取 Markdown skill 文件的 LLM agent 配合使用。

> 派生自 [pahheb-skills](https://github.com/Pahheb/pahheb-skills) —— 针对 Arch Linux AUR 软件包开发进行了适配。

## Skills

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

### One-step（任意 agent）

复制仓库 URL 并粘贴给你的 agent：

```
https://github.com/enihcam/aur-skills
```

然后让 agent 从该 URL 安装 `aur-guides` skill。大多数 agent 会直接从 GitHub 读取 `aur-guides/` 目录，无需克隆或符号链接。

### Manual（symlink）

如果你的 agent 要求本地安装 skill：

```bash
git clone https://github.com/enihcam/aur-skills.git /tmp/aur-skills
ln -s /tmp/aur-skills/aur-guides <install-path>/aur-guides
```

| Tool | Install Path |
| :--- | :--- |
| OpenCode | `~/.config/opencode/skills/aur-guides` |
| Claude Code | `~/.claude/skills/aur-guides` |
| Gemini CLI | `~/.gemini/skills/aur-guides` |
| Codex CLI | `~/.agents/skills/aur-guides` |
| Cursor | `~/.cursor/skills/aur-guides` |
| Windsurf | `~/.codeium/windsurf/skills/aur-guides` |

Per-project（OpenCode）：

```bash
git clone https://github.com/enihcam/aur-skills.git /tmp/aur-skills
mkdir -p .opencode/skills
ln -s /tmp/aur-skills/aur-guides .opencode/skills/aur-guides
```

## Usage

```
用 @aur-guides 帮我为项目创建 PKGBUILD
用 @aur-pkgbuild 为 my-app 编写 PKGBUILD
用 @aur-submission 将我的包提交到 AUR
```

`aur-guides` 是 master dispatcher skill——它会根据你的任务自动路由到相应的 sub-skill。

## Requirements

- 支持读取 skill 文件的 LLM agent（OpenCode、Claude Code、Gemini CLI、Codex 等）
- 提交到 AUR 需要 [AUR 账户](https://aur.archlinux.org) 并上传 SSH 密钥

## License

MIT
