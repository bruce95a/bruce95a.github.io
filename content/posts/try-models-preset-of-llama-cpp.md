+++
date = '2026-10-31T15:03:59+08:00'
description = 'Configure llama.cpp model presets'
draft = false
title = 'Try model preset of llama.cpp'
tags = ['Llama.cpp']
ShowToc = true
ShowBreadCrumbs = true
+++

## Background

Each model has its strength. I want to use the right model for the right job. But switching models is a pain. Until I found llama.cpp's Model presets.

```sh
llama-server --models-preset /path/to/models.ini
```

## The models.ini
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
llama_server_args="--models-preset /path/to/models.ini --models-max 1 --host 0.0.0.0 -ctk q8_0 -ctv q8_0 -ctkd q8_0 -ctvd q8_0"
```

## Try

```sh
doas service llama-server restart
```

Visit llama.cpp's [WebUI](http://localhost:9931), models won't load as expected.

## Fallback
```conf
llama_server_model="/path/to/Spark-X2.6-4B-GGUF/Spark-X2.6-4B-Q4_K_M.gguf"
llama_server_args="-a Spark-X2.6-4B-Q4_K_M --host 0.0.0.0 --jinja --temp 1.0 --top-k 0 --top-p 0.95 -ctk q8_0 -ctv q8_0 -ctkd q8_0 -ctvd q8_0"
```

