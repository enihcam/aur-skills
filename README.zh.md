# AUR Skills

一组面向 Arch Linux AUR 软件包开发的指令集——涵盖 PKGBUILD 编写、审查、构建、提交和维护。可与任何支持读取 Markdown 技能文件的 LLM 代理配合使用。

> 派生自 [pahheb-skills](https://github.com/Pahheb/pahheb-skills) —— 针对 Arch Linux AUR 软件包开发进行了适配。

## 技能

### aur-guides（主调度器）

将请求路由到专门的子技能以处理各类 AUR 任务。

| 子技能 | 用途 |
|--------|------|
| **aur-pkgbuild** | PKGBUILD 创建与语法 |
| **aur-package-guidelines** | Arch Linux 打包规范 |
| **aur-submission** | AUR 提交与维护 |
| **aur-audit** | 包审查与验证 |
| **aur-makepkg** | 构建流程配置 |
| **aur-vcs-packages** | 版本控制系统（-git/-svn/-hg）软件包 |
| **aur-pacman** | Pacman 使用指南 |
| **aur-helpers** | AUR 助手工具（yay、paru） |
| **aur-rpc** | AUR Web RPC 接口（搜索、信息查询、元数据存档） |

## 安装

### 一步完成（任意代理）

复制仓库 URL 并粘贴给你的代理：

```
https://github.com/enihcam/aur-skills
```

然后让代理从该 URL 安装 `aur-guides` 技能。大多数代理会直接从 GitHub 读取 `aur-guides/` 目录，无需克隆或符号链接。

### 手动安装（符号链接）

如果你的代理要求本地安装技能：

```bash
git clone https://github.com/enihcam/aur-skills.git /tmp/aur-skills
ln -s /tmp/aur-skills/aur-guides <install-path>/aur-guides
```

| 工具 | 安装路径 |
| :--- | :--- |
| OpenCode | `~/.config/opencode/skills/aur-guides` |
| Claude Code | `~/.claude/skills/aur-guides` |
| Gemini CLI | `~/.gemini/skills/aur-guides` |
| Codex CLI | `~/.agents/skills/aur-guides` |
| Cursor | `~/.cursor/skills/aur-guides` |
| Windsurf | `~/.codeium/windsurf/skills/aur-guides` |

项目本地安装（OpenCode）：

```bash
git clone https://github.com/enihcam/aur-skills.git /tmp/aur-skills
mkdir -p .opencode/skills
ln -s /tmp/aur-skills/aur-guides .opencode/skills/aur-guides
```

## 使用方法

```
用 @aur-guides 帮我为项目创建 PKGBUILD
用 @aur-pkgbuild 为 my-app 编写 PKGBUILD
用 @aur-submission 将我的包提交到 AUR
```

`aur-guides` 是主调度技能——它会根据你的任务自动路由到相应的子技能。

## 前提条件

- 支持读取技能文件的 LLM 代理（OpenCode、Claude Code、Gemini CLI、Codex 等）
- 提交到 AUR 需要 [AUR 账户](https://aur.archlinux.org) 并上传 SSH 密钥

## 许可证

MIT
