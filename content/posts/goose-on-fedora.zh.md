+++
date = '2026-11-15T16:50:01+08:00'
description = '使用llama.cpp配合goose'
draft = false
title = '在Fedora上使用goose'
tags = ['goose', 'AI']
ShowToc = true
ShowBreadCrumbs = true
+++

## 安装

```bash
sudo dnf install goose
```

## 配置

如果想用它配合 llama.cpp 使用，在运行 `goose` 或 `goose configure` 之前，先设置环境变量：

```bash
# 设置环境变量
export OPENAI_API_KEY="st-..."
export OPENAI_HOST="http://localhost:9931"
```

1. 运行 `goose configure`
2. 选择 `Configure Providers`
3. 选择 `OpenAI` 作为提供方
4. 按提示输入你的配置：
   - API key
   - Host URL（如果使用自定义端点）
   - Organization ID（如果使用组织追踪）
   - Project identifier（如果使用项目管理）

## [ACP](https://agentclientprotocol.com/get-started/introduction)

对于 Windows 上的 IntelliJ IDEA：`~/.jetbrains/acp.json`
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

> goose 没有像 Claude 那样 `NODE_EXTRA_CA_CERTS` 的环境变量，因此无法直接使用 https 连接，需要先把证书导入到 OS 中。

> goose 目前在 FreeBSD 的仓库中还没有。
