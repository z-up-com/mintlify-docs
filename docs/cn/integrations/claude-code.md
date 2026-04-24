---
title: "Claude Code 配置指南"
---
Source: https://docs.toapis.com/docs/cn/integrations/claude-code

在 Claude Code 中使用 ToAPIs 作为 API 后端

## 准备工作

在开始之前，请确保：

1. **已安装 Claude Code**

   通过 npm 或 pnpm 安装 Claude Code CLI：

   ```bash theme={null}
   # npm
   npm install -g @anthropic-ai/claude-code

   # pnpm
   pnpm install -g @anthropic-ai/claude-code
   ```

   或访问 [Claude Code 官方文档](https://docs.anthropic.com/claude-code) 获取最新安装方式

2. **已获取 ToAPIs API 密钥**

   登录 [ToAPIs 控制台](https://toapis.com/console/token) 获取您的 API 密钥（以 `sk-` 开头）

**提示：** 如果还没有 ToAPIs 账户，请先在 [ToAPIs](https://toapis.com) 注册并获取 API 密钥。

## 配置方式

Claude Code 支持多种方式配置自定义 API 端点，您可以将 ToAPIs 作为 Anthropic API 的替代后端。

Claude Code 使用两个不同的 JSON 文件：**onboarding 状态** 与 **环境变量** 需分别写入对应文件才能生效。

### 先确认 onboarding 已完成

在用户主目录下的 `~/.claude.json` 中设置已完成引导（若文件不存在可创建）：

```json theme={null}
{
  "hasCompletedOnboarding": true
}
```

该文件仅用于 CLI 状态位；API 密钥与 base URL 请写在下面的 `settings.json` 中。

### 方式一：通过 settings.json 配置（推荐）

在 `~/.claude/settings.json`（全局）或项目根目录的 `.claude/settings.json`（项目级）中配置环境变量，无需修改 shell 配置文件：

```json theme={null}
{
  "env": {
    "ANTHROPIC_AUTH_TOKEN": "sk-xxxxxxxxxxxx",
    "ANTHROPIC_BASE_URL": "https://toapis.com"
  }
}
```

**全局配置**（推荐，对所有项目生效）：

```bash theme={null}
