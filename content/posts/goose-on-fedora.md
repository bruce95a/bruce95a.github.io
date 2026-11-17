+++
date = '2026-11-15T16:50:01+08:00'
description = 'Using goose with llama.cpp'
draft = false
title = 'Goose on Fedora'
tags = ['goose', 'AI']
ShowToc = true
ShowBreadCrumbs = true
+++

## Install

```bash
sudo dnf install goose
```

## Config

If you want to use it with llama.cpp, before running `goose` or `goose configure`

```bash
# set the env
export OPENAI_API_KEY="st-..."
export OPENAI_HOST="http://localhost:9931"
```

1. Run `goose configure`
2. Select `Configure Providers`
3. Choose `OpenAI` as the provider
4. Enter your configuration when prompted:
   - API key
   - Host URL (if using custom endpoint)
   - Organization ID (if using organization tracking)
   - Project identifier (if using project management)

## [ACP](https://agentclientprotocol.com/get-started/introduction)

For IntelliJ IDEA on Windows: `~/.jetbrains/acp.json`
```json
{
    "default_mcp_settings":{},
    "agent_servers": {
        "goose": {
            "command": "E:\\opt\\goose.exe",
            "args": [
                "acp"
            ],
            "env": {
                "GOOSE_DOCS_ROOT": "E:\\goose-docs",
                "GOOSE_SHELL": "C:\\Program Files\\Git\\bin\\bash.exe",
                "HTTP_PROXY": "http://localhost:1080",
                "HTTPS_PROXY": "http://localhost:1080",
                "NO_PROXY": "localhost,127.0.0.1,.cn,.apple.com,.bing.com,.microsoft.com,.nvidia.com"
            }
        }
    }
}
```

> goose doesn't have an env like Claude's `NODE_EXTRA_CA_CERTS`, so it can't use https without importing the cert to OS.

> goose is not in FreeBSD's repo at this time.
