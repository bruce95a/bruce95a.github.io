+++
date = '2026-10-21T09:45:45+08:00'
description = 'FreeBSD上的本地AI'
draft = false
title = 'FreeBSD上的Llama.cpp'
tags = ['AI', 'Llama.cpp']
ShowToc = true
ShowBreadCrumbs = true
+++
## 背景
Nvidia提供FreeBSD驱动，但是不支持CUDA。幸运的是llama.cpp支持Vulkan，有显卡加速总比CPU跑强吧！

## 安装
```sh
doas pkg install llama-cpp
```

### 下载GGUF模型

* 从[Hugging Face](https://huggingface.co/)下载
* 从[hf-mirror](https://hf-mirror.com/)下载
* 从[ModelScope](https://www.modelscope.cn/home)下载

### 配置服务

看软件包包含的文件：
```sh
pkg info -l llama-cpp
```
其中 `/usr/local/etc/rc.d/llama-server` 是服务文件，有下列内容：
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

根据它设置：
```sh
doas sysrc llama_server_enable="YES"
doas sysrc llama_server_model="/zroot/llama/Gemma-4-E4B-Q4_K_M.gguf"
doas sysrc llama_server_args="-mm /zroot/llama/mmproj-Gemma-4-E4B-f16.gguf --host 0.0.0.0 --reasoning off"
```

启动服务
```sh
doas service llama-server start
```

### 开始聊天

访问llama.cpp的[WebUI](http://localhost:9931)

