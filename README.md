My openwrt build log.

It may not work.

```
Source:
https://github.com/carlosedp/riscv-bringup
https://github.com/xfguo/riscv-openwrt
https://github.com/openwrt/openwrt  (HEAD -> openwrt-21.02, origin/openwrt-21.02)
```

## Usage

Requires riscv machine

```
sudo apt install build-essential gcc binutils bzip2 flex python3 perl make grep unzip gawk subversion zlib1g-dev libc6-dev rsync libncurses5-dev libncursesw5-dev
git clone https://github.com/openwrt/openwrt
git checkout 85cef1cf221a4786fb8b1531748a5a0790101a3a
# apply patch 
git apply diff.patch # untested
make menuconfig
# Select Target → RISC-V HiFive Unleashed / QEMU
# Advanced configuration options → Toolchain options → C Library implementation, select Use glibc
make
```

```
~/openwrt$ make 
time: target/linux/prereq#0.60#0.31#0.86
 make[1] world
 make[2] tools/compile
 make[3] -C tools/flock compile
 make[3] -C tools/xz compile
 make[3] -C tools/sed compile
```

## Note
```
git diff HEAD > ../patch.diff
scp user@ip:~/patch.diff
```
