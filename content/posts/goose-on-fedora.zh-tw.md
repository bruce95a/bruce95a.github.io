+++
date = '2026-11-15T16:50:01+08:00'
description = '使用llama.cpp配合goose'
draft = false
title = '在Fedora上使用goose'
tags = ['goose', 'AI']
ShowToc = true
ShowBreadCrumbs = true
+++

## 安裝

```bash
sudo dnf install goose
```

## 設定

如果想用它配合 llama.cpp 使用，在運行 `goose` 或 `goose configure` 之前，先設定環境變數：

```bash
# 設定環境變數
export OPENAI_API_KEY="st-..."
export OPENAI_HOST="http://localhost:9931"
```

1. 執行 `goose configure`
2. 選擇 `Configure Providers`
3. 選擇 `OpenAI` 作為提供方
4. 按提示輸入你的設定：
   - API key
   - Host URL（如果使用自定义端点）
   - Organization ID（如果使用組織追踪）
   - Project identifier（如果使用項目管理）

## [ACP](https://agentclientprotocol.com/get-started/introduction)

對 Windows 上的 IntelliJ IDEA：`~/.jetbrains/acp.json`
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

> goose 沒有像 Claude 那樣 `NODE_EXTRA_CA_CERTS` 的環境變數，因此無法直接使用 https 連接，需要先把證書導入到 OS 中。

> goose 目前在 FreeBSD 的倉庫中還沒有。
