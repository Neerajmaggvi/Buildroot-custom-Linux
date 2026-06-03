# Custom Embedded Linux with Buildroot

A custom Embedded Linux system built from source using Buildroot and booted on a QEMU ARM VExpress platform.

The project generates a complete Linux image including the Linux kernel, BusyBox user space, root filesystem, custom startup scripts, and SSH access through Dropbear.

---

## What it does

- Builds a complete Linux system from source using Buildroot
- Cross-compiles for ARM on an x86-64 Ubuntu host
- Boots Linux on the QEMU VExpress-A9 emulator
- Executes a custom init script during startup
- Provides remote login through Dropbear SSH
- Generates a bootable root filesystem image

---

## Output

```text
Buildroot 2024.02

Hello from my custom init script

buildroot login: root
Password:

# uname -a
Linux buildroot 6.1.44 #1 SMP ARMv7 GNU/Linux

# hostname
buildroot

# ssh root@localhost -p 2222
Login successful
```

---

## Project Structure

```text
buildroot/
├── .config
└── board/
    └── raspberrypi/
        └── rootfs_overlay/
            └── etc/
                └── init.d/
                    └── S99hello
```

---

## How it works

### Boot Flow

```text
QEMU ARM VExpress
      ↓
Linux Kernel (zImage)
      ↓
Device Tree (DTB)
      ↓
Root Filesystem (rootfs.ext2)
      ↓
BusyBox init (PID 1)
      ↓
S99hello custom script
      ↓
Dropbear SSH server
      ↓
User Login Shell
```

### Custom Init Script

```sh
#!/bin/sh

case "$1" in
  start)
    echo "Hello from my custom init script" > /dev/console
    ;;
  stop)
    echo "Stopping" > /dev/console
    ;;
esac
```

---

## System Configuration

| Component | Value |
|-----------|--------|
| Build System | Buildroot 2024.02 |
| Architecture | ARMv7 |
| Emulator | QEMU VExpress-A9 |
| Kernel | Linux 6.1.44 |
| Init System | BusyBox |
| SSH Server | Dropbear |
| Host OS | Ubuntu 22.04 |

---

## Build and Run

### Build

```bash
make qemu_arm_vexpress_defconfig
make menuconfig
make -j$(nproc)
```

### Run

```bash
qemu-system-arm -M vexpress-a9 -smp 1 -m 256 \
-kernel zImage \
-dtb vexpress-v2p-ca9.dtb \
-drive file=rootfs.ext2,if=sd,format=raw \
-append "console=ttyAMA0,115200 rootwait root=/dev/mmcblk0" \
-nographic \
-netdev user,id=net0,hostfwd=tcp::2222-:22 \
-device virtio-net-device,netdev=net0
```

### SSH Login

```bash
ssh root@localhost -p 2222
```

---

## Key Concepts Demonstrated

- Cross Compilation
- Linux Boot Process
- BusyBox Init System
- Root Filesystem Creation
- Device Tree Usage
- Embedded Linux Development
- QEMU Virtual Platforms
- SSH Service Integration

---

## Screenshots

### Boot Process

![image alt](screenshots/system_info.png)

### SSH Login

![image alt](screenshots/ssh_working.png) 

---

## Development Environment

- OS: Ubuntu 22.04 LTS
- Build System: Buildroot 2024.02
- Emulator: QEMU
- Architecture: ARMv7
- Shell: BusyBox
- SSH: Dropbear

---

## Author

**Neeraj Maggavi**
