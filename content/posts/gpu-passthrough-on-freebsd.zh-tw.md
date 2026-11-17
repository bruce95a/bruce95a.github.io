+++
date = '2026-10-22T16:07:07+08:00'
description = '将iGPU直通给bhyve客户機'
draft = false
title = 'FreeBSD直通iGPU'
tags = ['bhyve']
ShowToc = true
ShowBreadCrumbs = true
+++

## 準備

### 安裝EFI firmware
```sh
doas pkg install bhyve-firmware
```

### 虛擬網卡
```sh
doas sysrc cloned_interfaces="bridge0 tap0 tap1"
doas sysrc ifconfig_bridge0="addm em0 addm tap0 addm tap1 up"
```

### 確定PCIe設備ID
```sh
pciconf -lv
```

輸出如下：
```
vgapci0@pci0:0:2:0:        class=0x038000 rev=0x06 hdr=0x00 vendor=0x8086 device=0x1912 subvendor=0x1043 subdevice=0x8694
    vendor     = 'Intel Corporation'
    device     = 'Skylake-S GT2 [HD Graphics 530]'
    class      = display
xhci0@pci0:0:20:0:       class=0x0c0330 rev=0x31 hdr=0x00 vendor=0x8086 device=0xa12f subvendor=0x1043 subdevice=0x8694
    vendor     = 'Intel Corporation'
    device     = '100 Series/C230 Series Chipset Family USB 3.0 xHCI Controller'
    class      = serial bus
    subclass   = USB
...
hdac0@pci0:0:31:3:       class=0x040300 rev=0x31 hdr=0x00 vendor=0x8086 device=0xa170 subvendor=0x1043 subdevice=0x8699
    vendor     = 'Intel Corporation'
    device     = '100 Series/C230 Series Chipset Family HD Audio Controller'
    class      = multimedia
    subclass   = HDA

```

添加設備ID到 `/boot/loader.conf`
```conf
vmm_load="YES"
pptdevs="0/2/0 0/20/0 0/31/3"
```

重新開機后：
```
ppt0@pci0:0:2:0:        class=0x038000 rev=0x06 hdr=0x00 vendor=0x8086 device=0x1912 subvendor=0x1043 subdevice=0x8694
    vendor     = 'Intel Corporation'
    device     = 'Skylake-S GT2 [HD Graphics 530]'
    class      = display
ppt1@pci0:0:20:0:       class=0x0c0330 rev=0x31 hdr=0x00 vendor=0x8086 device=0xa12f subvendor=0x1043 subdevice=0x8694
    vendor     = 'Intel Corporation'
    device     = '100 Series/C230 Series Chipset Family USB 3.0 xHCI Controller'
    class      = serial bus
    subclass   = USB
...
ppt2@pci0:0:31:3:       class=0x040300 rev=0x31 hdr=0x00 vendor=0x8086 device=0xa170 subvendor=0x1043 subdevice=0x8699
    vendor     = 'Intel Corporation'
    device     = '100 Series/C230 Series Chipset Family HD Audio Controller'
    class      = multimedia
    subclass   = HDA
```

## 配置bhyve客戶機

```sh
bhyve \
-c sockets=1,cores=4,threads=1 \
-m 8G \
-l bootrom,/usr/local/share/edk2-bhyve/BHYVE_UEFI.fd \
-s 0:0,hostbridge \
-s 2:0,passthru,0/2/0 \
-s 20:0,passthru,0/20/0 \
-s 23:0,nvme,/zroot/bhyve/fedora/disk0.img \
-s 31:0,lpc \
-s 31:3,passthru,0/31/3 \
-s 31:6,virtio-net,tap1 \
-o pci.0.31.0.pcireg.vendor=host \
-o pci.0.31.0.pcireg.device=host \
-o pci.0.31.0.pcireg.revid=host \
-o pci.0.31.0.pcireg.subvendor=host \
-o pci.0.31.0.pcireg.subdevice=host \
-uDHPS \
fedora
```

## 系統服務

创建 `/usr/local/etc/rc.d/vmsrv`

```sh
#!/bin/sh

# PROVIDE: vmsrv
# REQUIRE: LOGIN FILESYSTEMS nfsd
# KEYWORD: shutdown

. /etc/rc.subr

name="vmsrv"
rcvar="vmsrv_enable"
start_cmd="${name}_start"
stop_cmd="${name}_stop"
status_cmd="${name}_status"
pidfile="/var/run/${name}.pid"

vm_path="/zroot/bhyve/fbsd"
vm_name="fbsd15"

load_rc_config $name

: ${vmsrv_enable:="NO"}

vmsrv_start()
{
    daemon \
        -o /var/log/${name}.log \
        -p "${pidfile}" \
        bhyve \
        -c sockets=1,cores=4,threads=1 \
        -m 8G \
        -l bootrom,/usr/local/share/edk2-bhyve/BHYVE_UEFI.fd \
        -s 0:0,hostbridge \
        -s 2:0,passthru,0/2/0 \
        -s 20:0,passthru,0/20/0 \
        -s 23:0,nvme,/zroot/bhyve/fedora/disk0.img \
        -s 31:0,lpc \
        -s 31:3,passthru,0/31/3 \
        -s 31:6,virtio-net,tap1 \
        -o pci.0.31.0.pcireg.vendor=host \
        -o pci.0.31.0.pcireg.device=host \
        -o pci.0.31.0.pcireg.revid=host \
        -o pci.0.31.0.pcireg.subvendor=host \
        -o pci.0.31.0.pcireg.subdevice=host \
        ${vm_name}
}

vmsrv_stop()
{
    if [ ! -f "${pidfile}" ]; then
        echo "the server vm isn't running!"
        return
    fi

    pid="$(cat ${pidfile})"
    kill "${pid}"

    # Wait a bit for the guest to shutdown properly before
    # we continue shutting down the host.
    wait_for_pids "${pid}"
}

vmsrv_status()
{
    if [ -f "${pidfile}" ]; then
        echo "$(cat ${pidfile})"
    fi
}

run_rc_command "$1"
```

開機啓動
```sh
doas service enable vmsrv
```

## 參考

* [https://wiki.freebsd.org/bhyve](https://wiki.freebsd.org/bhyve)
* [https://wiki.freebsd.org/bhyve/pci_passthru](https://wiki.freebsd.org/bhyve/pci_passthru)
* [https://xyinn.org/blog/freebsd/freebsd_bhyve_gpu_passthrough_amd](https://xyinn.org/blog/freebsd/freebsd_bhyve_gpu_passthrough_amd)
