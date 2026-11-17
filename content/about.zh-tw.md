+++
date = '2026-10-21T09:01:46+08:00'
draft = false
title = '關於'
ShowToc = false
ShowBreadCrumbs = false
+++

歡迎來到我的部落格，一起探索FreeBSD。

我是從FreeBSD 13.0開始使用的（大概是2021年3月，RC1發布的時候）。當時我的主系統是Fedora/CentOS Stream，在KVM上體驗FreeBSD，也體驗過一段時間的GhostBSD。直到2025年，FreeBSD 15.0發布不久，才下定決心將主系統切換過來。

這樣長久才換，主要原因有2個：

* GPU直通
* 檔案系統更換

FreeBSD 13.0時Bhyve已經支援PCI直通，但是還不支持GPU直通。當時在KVM上體驗FreeBSD，是将Nvidia GTX 1060直通給FreeBSD的。偶爾也通過[OSX-KVM](https://github.com/kholia/OSX-KVM)體驗macOS 10.13.6，也是將Nvidia GTX 1060直通過來（用著用著，啟動不了，新版本macOS沒有顯卡驅動，放棄了）。

2025年AI流行起來，將Nvidia GTX 1060直通給CentOS運行[Ollama](https://ollama.com/)，感受了下本地的DeepSeek（好像是4月左右），因這個顯卡只有6G顯存，體驗不好（吐字太慢），沒有堅持下來。

2026年初借了室友的電腦，數據在多塊硬盤間捣騰。雖然FreeBSD支援讀取ext4，但是感覺不原生，於是用室友的電腦跑Fedora Linux，用rsync同步。

差不多1年沒有在本地跑AI了，偶然發現了llama.cpp，並且FreeBSD倉庫有，安裝跑了下，嘿，居然支援GPU加速（Vulkan，比CUDA慢一點），Qwen3.5 4B Q4_K_M能跑 30 t/s，到了能用的水平。

因FreeBSD的桌面向軟件支援有限，現在就不安裝桌面了。Bhyve將iGPU直通給Fedora使用（不喜歡systemd和GNU，早晚会離開它的）。
