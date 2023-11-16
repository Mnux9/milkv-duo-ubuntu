# Ubuntu on the Milk-V Duo
![F-MbY2FXUAAk21P](https://github.com/bassusteur/milkv-duo-ubuntu/assets/42449683/3dcdbd84-12a6-4c86-969c-92a2e9e92496)

## Setup 
1. Ubuntu 22.04 LTS installed on a virtual machine
2. Setup ![duo-buildroot-sdk](https://github.com/milkv-duo/duo-buildroot-sdk#prepare-the-compilation-environment) on your machine

## Before anything else
```bash
#We need to enable a few modules in the kernel configuration before we can continue, so:
nano ~/duo-buildroot-sdk/build/boards/cv180x/cv1800b_milkv_duo_sd/linux/cvitek_cv1800b_milkv_duo_sd_defconfig

# and add at the end:
CONFIG_CGROUPS=y
CONFIG_CGROUP_FREEZER=y
CONFIG_CGROUP_PIDS=y
CONFIG_CGROUP_DEVICE=y
CONFIG_CPUSETS=y
CONFIG_PROC_PID_CPUSET=y
CONFIG_CGROUP_CPUACCT=y
CONFIG_PAGE_COUNTER=y
CONFIG_MEMCG=y
CONFIG_CGROUP_SCHED=y
CONFIG_NAMESPACES=y
CONFIG_OVERLAY_FS=y
CONFIG_AUTOFS4_FS=y
CONFIG_SIGNALFD=y
CONFIG_TIMERFD=y
CONFIG_EPOLL=y
CONFIG_IPV6=y
CONFIG_FANOTIFY

# optional (enable zram):
CONFIG_ZSMALLOC=y
CONFIG_ZRAM=y
```

## Creating the rootfs
```bash
# install prerequisites
sudo apt install debootstrap qemu qemu-user-static binfmt-support dpkg-cross --no-install-recommends
# generate minimal bootstrap rootfs
sudo debootstrap --arch=riscv64 --foreign jammy ./temp-rootfs http://ports.ubuntu.com/ubuntu-ports
# chroot into the rootfs we just created
sudo chroot temp-rootfs /bin/bash
# run 2nd stage of deboostrap
/debootstrap/debootstrap --second-stage
# add package sources
cat >/etc/apt/sources.list <<EOF
deb http://ports.ubuntu.com/ubuntu-ports jammy main restricted

deb http://ports.ubuntu.com/ubuntu-ports jammy-updates main restricted

deb http://ports.ubuntu.com/ubuntu-ports jammy universe
deb http://ports.ubuntu.com/ubuntu-ports jammy-updates universe

deb http://ports.ubuntu.com/ubuntu-ports jammy multiverse
deb http://ports.ubuntu.com/ubuntu-ports jammy-updates multiverse

deb http://ports.ubuntu.com/ubuntu-ports jammy-backports main restricted universe multiverse

deb http://ports.ubuntu.com/ubuntu-ports jammy-security main restricted
deb http://ports.ubuntu.com/ubuntu-ports jammy-security universe
deb http://ports.ubuntu.com/ubuntu-ports jammy-security multiverse
EOF
```
