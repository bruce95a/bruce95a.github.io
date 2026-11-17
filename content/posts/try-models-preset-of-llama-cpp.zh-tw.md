+++
date = '2026-10-31T15:03:59+08:00'
description = '配置llama.cpp的模型預設'
draft = false
title = '試用llama.cpp的模型預設'
tags = ['Llama.cpp']
ShowToc = true
ShowBreadCrumbs = true
+++

## 背景

每個模型都有其強項。我想為不同的任務選用合適的模型，但切換模型很麻煩。直到找到了 llama.cpp 的模型預設。

```sh
llama-server --models-preset /path/to/models.ini
```

## 模型預設
```ini
[*]
jinja = true

[gemma-4-E4B]
model = /path/to/Gemma-4-E4B/Gemma-4-E4B-Q4_K_M.gguf
mmproj = /path/to/Gemma-4-E4B/mmproj-Gemma-4-E4B-f16.gguf
temp = 1.0
top-p = 0.95
top-k = 64

[Spark-X2.6-4B]
model = /path/to/Spark-X2.6-4B-GGUF/Spark-X2.6-4B-Q4_K_M.gguf

[Qwen3.8-4B-Distill-GGUF]
model = /path/to/Qwen3.8-4B-Distill-GGUF/Qwen3.8-4B-Q4_K_M.gguf
temp = 1.0
top-p = 0.95
top-k = 20

```

## rc.conf
```conf
llama_server_model=""
llama_server_args="--models-preset /path/to/models.ini --host 0.0.0.0 -ctk q8_0 -ctv q8_0 -ctkd q8_0 -ctvd q8_0"
```

## 試用

```sh
doas service llama-server restart
```

訪問 llama.cpp 的 [WebUI](http://localhost:9931)，模型無法按預期載入。

## 回退方案
```conf
llama_server_model="/path/to/Spark-X2.6-4B-GGUF/Spark-X2.6-4B-Q4_K_M.gguf"
llama_server_args="-a Spark-X2.6-4B-Q4_K_M --host 0.0.0.0 --jinja --temp 1.0 --top-k 0 --top-p 0.95 -ctk q8_0 -ctv q8_0 -ctkd q8_0 -ctvd q8_0"
```
