+++
date = '2026-10-21T09:01:46+08:00'
draft = false
title = 'About'
ShowToc = false
ShowBreadCrumbs = false
+++

Welcome to my blog, where we explore FreeBSD together.

I first started using FreeBSD 13.0 (around March 2021, when the RC1 was released). At the time, my main system was Fedora/CentOS Stream, and I ran FreeBSD on KVM, and also used GhostBSD for a while. It wasn't until shortly after the release of FreeBSD 15.0 in 2025 that I decided to switch my main system to FreeBSD.

There are two main reasons I switched so late:

* GPU passthrough
* Filesystem switch

When FreeBSD 13.0 came out, Bhyve already supported PCI passthrough, but did not support GPU passthrough. At the time I was running FreeBSD on KVM, passing my Nvidia GTX 1060 straight through to FreeBSD. I occasionally also used [OSX-KVM](https://github.com/kholia/OSX-KVM) to run macOS 10.13.6, also passing the Nvidia GTX 1060 through — but it wouldn't start once and for all, as newer macOS versions don't have the graphics driver for it, so I gave up.

In 2025, with AI becoming popular, I passed my Nvidia GTX 1060 to CentOS to run [Ollama](https://ollama.com/), and tried the local DeepSeek (around April). Because this card only has 6GB of VRAM, the experience was poor (the generation was too slow), so I didn't stick with it.

Early in 2026, I borrowed my roommate's computer and shuffled the data across multiple hard disks. Although FreeBSD supports reading ext4, it doesn't feel native, so I ran Fedora Linux on my roommate's computer and synced with rsync.

I hadn't run AI locally for about a year. By chance I came across [llama.cpp](https://github.com/ggml-org/llama.cpp), which was available in the FreeBSD repository. I installed it and gave it a try — hey, it actually supports GPU acceleration (Vulkan, a bit slower than CUDA), and Qwen3.5 4B Q4_K_M can run at 30 t/s, which is at a usable level.

Because FreeBSD's desktop software support is limited, I don't install the desktop anymore. Bhyve passes the iGPU to Fedora for use (I don't like systemd and GNU, and I'll be leaving them eventually).
