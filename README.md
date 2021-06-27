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
make -r world: build failed. Please re-run make with -j1 V=s or V=sc for a higher verbosity level to see what's going on
make: *** [/home/ubuntu/openwrt/include/toplevel.mk:230: world] Error 1
ubuntu@ubuntu:~/openwrt$ make -j1 V=sc
make[1]: Entering directory '/home/ubuntu/openwrt'
make[2]: Entering directory '/home/ubuntu/openwrt'
+ mkdir -p /home/ubuntu/openwrt/staging_dir/target-riscv64_riscv64_glibc
+ cd /home/ubuntu/openwrt/staging_dir/target-riscv64_riscv64_glibc
+ mkdir -p bin lib stamp usr/include usr/lib
mkdir -p /home/ubuntu/openwrt/build_dir/target-riscv64_riscv64_glibc/stamp
touch /home/ubuntu/openwrt/staging_dir/target-riscv64_riscv64_glibc/.prepared
make[3]: Entering directory '/home/ubuntu/openwrt/toolchain/gdb'
make[3]: Nothing to be done for 'compile'.
make[3]: Leaving directory '/home/ubuntu/openwrt/toolchain/gdb'
time: toolchain/gdb/compile#0.03#0.00#0.04
make[3]: Entering directory '/home/ubuntu/openwrt/toolchain/binutils'
make[3]: Nothing to be done for 'compile'.
make[3]: Leaving directory '/home/ubuntu/openwrt/toolchain/binutils'
time: toolchain/binutils/compile#0.03#0.00#0.05
make[3]: Entering directory '/home/ubuntu/openwrt/toolchain/gcc/minimal'
make[3]: Nothing to be done for 'compile'.
make[3]: Leaving directory '/home/ubuntu/openwrt/toolchain/gcc/minimal'
time: toolchain/gcc/minimal/compile#0.03#0.02#0.06
make[3]: Entering directory '/home/ubuntu/openwrt/toolchain/kernel-headers'
mkdir -p /home/ubuntu/openwrt/dl
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

## Note
```
git diff HEAD > ../patch.diff
scp user@ip:~/patch.diff
```
