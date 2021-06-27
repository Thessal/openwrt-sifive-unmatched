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
make -j4
```

```
~/openwrt$ make 
time: target/linux/prereq#0.60#0.31#0.86
 make[1] world
 make[2] tools/compile
 make[3] -C tools/flock compile
 make[3] -C tools/xz compile
 make[3] -C tools/sed compile
...
 make[3] -C toolchain/kernel-headers compile
    ERROR: toolchain/kernel-headers failed to build.
make -r world: build failed. Please re-run make with -j1 V=s or V=sc for a higher verbosity level to see what's going on
make: *** [/home/ubuntu/openwrt/include/toplevel.mk:230: world] Error 1
```

```
SHELL= flock /home/ubuntu/openwrt/tmp/.linux-5.11.22.tar.xz.flock -c '          /home/ubuntu/openwrt/scripts/download.pl "/home/ubuntu/openwrt/dl" "linux-5.11.22.tar.xz" "x" "" "@KERNEL/linux/kernel/v5.x"    '
Cannot find appropriate hash command, ensure the provided hash is either a MD5 or SHA256 checksum.
make[3]: *** [Makefile:113: /home/ubuntu/openwrt/dl/linux-5.11.22.tar.xz] Error 255
make[3]: Leaving directory '/home/ubuntu/openwrt/toolchain/kernel-headers'
time: toolchain/kernel-headers/compile#0.22#0.08#0.32
    ERROR: toolchain/kernel-headers failed to build.
make[2]: *** [toolchain/Makefile:97: toolchain/kernel-headers/compile] Error 1
make[2]: Leaving directory '/home/ubuntu/openwrt'
make[1]: *** [toolchain/Makefile:93: /home/ubuntu/openwrt/staging_dir/toolchain-riscv64_riscv64_gcc-8.4.0_glibc/stamp/.toolchain_compile] Error 2
make[1]: Leaving directory '/home/ubuntu/openwrt'
make: *** [/home/ubuntu/openwrt/include/toplevel.mk:230: world] Error 2
```

```
vim ./include/kernel-version.mk
# LINUX_KERNEL_HASH-5.11.22 
# shasum -a 256 of tar.gz of source files goes here
wget https://cdn.kernel.org/pub/linux/kernel/v5.x/linux-5.11.21.tar.xz
shasum -a 256 linux-5.11.21.tar.xz 
366ba5bb00be28b604aac630c4f64301063892f27353b299177c396af0ad877f  linux-5.11.21.tar.xz
#PKG_SOURCE_URL:=https://cdn.kernel.org/pub/
```

## Note
```
git diff HEAD > ../patch.diff
scp user@ip:~/patch.diff
```
