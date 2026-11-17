+++
date = '2026-11-15T16:15:54+08:00'
description = 'Using Claude Code with llama.cpp'
draft = false
title = 'Claude Code on FreeBSD'
tags = ['Claude', 'ACP', 'AI']
ShowToc = true
ShowBreadCrumbs = true
+++

## Install
```sh
doas pkg install claude-code
```

## Config

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

if on Windows
```env
"CLAUDE_CODE_GIT_BASH_PATH": "C:\\Program Files\\Git\\bin\\bash.exe",
```

## [ACP](https://agentclientprotocol.com/get-started/introduction)

```bash
npm install @agentclientprotocol/claude-agent-acp
```

For IntelliJ IDEA on Windows: `~/.jetbrains/acp.json`
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

> Codex is available too. But I didn't find a way to change its shell. I didn't get https to work either (though it has an env `CODEX_CA_CERTIFICATE` or `SSL_CERT_FILE`).
