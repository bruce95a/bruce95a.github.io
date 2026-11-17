+++
date = '2026-10-21T09:45:45+08:00'
description = 'Local AI on FreeBSD.'
draft = false
title = 'Llama.cpp on FreeBSD'
tags = ['AI', 'Llama.cpp']
ShowToc = true
ShowBreadCrumbs = true
+++
## Introduction

Nvidia doesn't support CUDA on FreeBSD. Luckly llama.cpp supports Vulkan, better than CPU.

```sh
doas pkg install llama-cpp
```

### Download models

Download some GGUFs from Hugging Face.

### Config service

Look at the package's content using this command:
```sh
pkg info -l llama-cpp
```
There's a service file `/usr/local/etc/rc.d/llama-server`. Something we can set:
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

So we set what we need:
```sh
doas sysrc llama_server_enable="YES"
doas sysrc llama_server_model="/zroot/llama/Gemma-4-E4B-Q4_K_M.gguf"
doas sysrc llama_server_args="-mm /zroot/llama/mmproj-Gemma-4-E4B-f16.gguf --host 0.0.0.0 --reasoning off"
```

Generate a api-key with:
```sh
openssl rand -base64 32
```

Start it:
```sh
doas service llama-server start
```

### Start to chat

Visit llama.cpp's [WebUI](http://localhost:9931)

