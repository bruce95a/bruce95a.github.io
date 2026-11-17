+++
date = '2026-10-21T09:01:46+08:00'
draft = false
title = '关于'
ShowToc = false
ShowBreadCrumbs = false
+++

欢迎来到我的博客，一起探索FreeBSD。

我是从FreeBSD 13.0开始使用的（大概是2021年3月，RC1发布的时候）。当时我的主系统是Fedora/CentOS Stream，在KVM上体验FreeBSD，也体验过一段时间的GhostBSD。直到2025年，FreeBSD 15.0发布不久，才下定决心将主系统切换过来。

这么久才换，主要原因有2个：

* GPU直通
* 文件系统更换

FreeBSD 13.0时Bhyve已经支持PCI直通，但是还不支持GPU直通。当时在KVM上体验FreeBSD，是将Nvidia GTX 1060直通给FreeBSD的。偶尔也通过[OSX-KVM](https://github.com/kholia/OSX-KVM)体验macOS 10.13.6，也是将Nvidia GTX 1060直通过来（用着用着，启动不了，新版本macOS没有显卡驱动，放弃了）。

2025年AI流行起来，将Nvidia GTX 1060直通给CentOS运行[Ollama](https://ollama.com/)，感受了下本地的DeepSeek（好像是4月左右），因为这个显卡只有6G显存，体验不好（吐字太慢）没有坚持下来。

2026年初借了室友的电脑，数据在多块硬盘间捣腾。虽然FreeBSD支持读取ext4，但是感觉不原生，于是用室友的电脑跑Fedora Linux，用rsync同步。

差不多1年没有在本地跑AI了，偶然发现了[llama.cpp](https://github.com/ggml-org/llama.cpp)，并且FreeBSD仓库有，安装跑了下，嘿，居然支持GPU加速（Vulkan，比CUDA慢一点），Qwen3.5 4B Q4_K_M能跑 30 t/s，到了能用的水平。

因FreeBSD的桌面软件支持有限，现在就不安装桌面了。Bhyve将iGPU直通给Fedora使用（不喜欢systemd和GNU，早晚会离开它的）。

