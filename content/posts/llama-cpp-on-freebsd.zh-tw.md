+++
date = '2026-10-21T09:45:45+08:00'
description = 'FreeBSD本地AI'
draft = false
title = '在FreeBSD跑llama.cpp'
tags = ['AI', 'Llama.cpp']
ShowToc = true
ShowBreadCrumbs = true
+++
## 背景
Nvidia的FreeBSD驅動程式不支援CUDA。FreeBSD的llama.cpp支援Vulkan。

## 安裝
```sh
doas pkg install llama-cpp
```

### 下載模型

* 從[Hugging Face](https://huggingface.co/)下載
* 從[hf-mirror](https://hf-mirror.com/)下載
* 從[ModelScope](https://www.modelscope.cn/home)下載

### 配置服務

列出程式的所有文件：
```sh
pkg info -l llama-cpp
```
有我們需要的服務文件 `/usr/local/etc/rc.d/llama-server`：
```
# Add the following lines to /etc/rc.conf to enable llama_server
# llama_server_enable="YES"
#
# llama_server_enable (bool):   Set to YES to enable llama_server
#                               Default: NO
# llama_server_user (str):      llama_server daemon user
#                               Default: nobody
# llama_server_model (str):     AI model that llama-server will use
#                               Default: "" (not required)
# llama_server_args (str):      Additional arguments for llama-server
#                               Default: "" (optional)
# llama_server_log (str):       Log file that llama-server will write log to
#                               Default: "/var/log/llama-server.log" (optional)
# llama_server_pidfile (str):   Pidfile file that llama-server's pid will be written to
#                               Default: "/var/run/llama_server.pid" (optional)
```

按照需要進行設定：
```sh
doas sysrc llama_server_enable="YES"
doas sysrc llama_server_model="/zroot/llama/Gemma-4-E4B-Q4_K_M.gguf"
doas sysrc llama_server_args="-mm /zroot/llama/mmproj-Gemma-4-E4B-f16.gguf --host 0.0.0.0 --reasoning off"
```

啓動
```sh
doas service llama-server start
```

### 開始聊天

訪問llama.cpp的[WebUI](http://localhost:9931)

