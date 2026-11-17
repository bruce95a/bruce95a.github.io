+++
date = '2026-11-15T16:15:54+08:00'
description = '使用Claude Code与llama.cpp'
draft = false
title = '在FreeBSD上使用Claude Code'
tags = ['Claude', 'ACP', 'AI']
ShowToc = true
ShowBreadCrumbs = true
+++

## 安装

```sh
doas pkg install claude-code
```

## 配置

`~/.claude/settings.json`
```json
{
  "env": {
    "ANTHROPIC_BASE_URL": "https://api.provider.com",
    "ANTHROPIC_AUTH_TOKEN": "sk-...",
    "API_TIMEOUT_MS": "3000000",
    "ANTHROPIC_DEFAULT_OPUS_MODEL": "Spark-X2.6-4B",
    "ANTHROPIC_DEFAULT_FABLE_MODEL": "Spark-X2.6-4B",
    "ANTHROPIC_DEFAULT_SONNET_MODEL": "Spark-X2.6-4B",
    "ANTHROPIC_DEFAULT_HAIKU_MODEL": "Spark-X2.6-4B",
    "ANTHROPIC_MODEL": "Spark-X2.6-4B",
    "ANTHROPIC_SMALL_FAST_MODEL": "Spark-X2.6-4B",
    "CLAUDE_CODE_DISABLE_1M_CONTEXT": "1",
    "CLAUDE_CODE_DISABLE_NONESSENTIAL_TRAFFIC": "1",
    "CLAUDE_CODE_MAX_CONTEXT_TOKENS": 115200,
    "NODE_EXTRA_CA_CERTS": "/path/to/ca_cert.pem"
  },
  "permissions": {
    "allow": [
      "WebFetch",
      "Bash(curl:*)",
      "Bash(python *)"
    ],
    "deny": ["WebSearch"]
  },
  "skipWebFetchPreflight": true,
  "theme": "dark"
}
```

在 Windows 上
```env
"CLAUDE_CODE_GIT_BASH_PATH": "C:\\Program Files\\Git\\bin\\bash.exe",
```

## [ACP](https://agentclientprotocol.com/get-started/introduction)

```bash
npm install @agentclientprotocol/claude-agent-acp
```

对于 Windows 上的 IntelliJ IDEA：`~/.jetbrains/acp.json`
```json
{
    "default_mcp_settings":{},
    "agent_servers": {
        "Claude Code": {
            "command": "path\\to\\node.exe",
            "args": [
                "path\\to\\node_modules\\@agentclientprotocol\\claude-agent-acp\\dist\\index.js"
            ],
            "env": {}
        }
    }
}
```

> 也可以使用 Codex。但没找到修改其 shell 的方法，https 也未能正常连接（虽然有 `CODEX_CA_CERTIFICATE` 或 `SSL_CERT_FILE` 环境变量）。
