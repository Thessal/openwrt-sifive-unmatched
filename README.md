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
cd openwrt
git checkout a75928d1259e52e52b1991a4dc39df61ba3c9206 
# apply patch 
git apply ../patch.diff # untested
make menuconfig
# Select Target → RISC-V HiFive Unleashed / QEMU
# Advanced configuration options → Toolchain options → C Library implementation, select Use glibc
make -j4
```

```
~/openwrt$ make 
```

```
SHELL= flock /home/ubuntu/openwrt/tmp/.linux-5.11.22.tar.xz.flock -c '          /home/ubuntu/openwrt/scripts/download.pl "/home/ubuntu/openwrt/dl" "linux-5.11.22.tar.xz" "x" "" "@KERNEL/linux/kernel/v5.x"    '
Cannot find appropriate hash command, ensure the provided hash is either a MD5 or SHA256 checksum.

-> vim ./include/kernel-version.mk
LINUX_KERNEL_HASH-5.11.22 = 11027c6114eb916edbcc37897226fb6263b2931911d2d5093550473ce1a57600
```

```
gcc -Wp,-MMD,scripts/.extract-cert.d -Wall -Wmissing-prototypes -Wstrict-prototypes -O2 -fomit-frame-pointer -std=gnu89  -O2 -I/home/ubuntu/openwrt/staging_dir/host/include  -Wall -Wmissing-prototypes -Wstrict-prototypes   -I/home/ubuntu/openwrt/staging_dir/host/include -I/home/ubuntu/openwrt/staging_dir/host/include    -o scripts/extract-cert scripts/extract-cert.c -L/home/ubuntu/openwrt/staging_dir/host/lib -L/home/ubuntu/openwrt/staging_dir/host/lib -lcrypto   
/usr/bin/ld: /home/ubuntu/openwrt/staging_dir/host/lib/libcrypto.a(libcrypto_la-eng_all.o): in function `.L0 ':      
eng_all.c:(.text+0x24): undefined reference to `pthread_once' 
/usr/bin/ld: /home/ubuntu/openwrt/staging_dir/host/lib/libcrypto.a(libcrypto_la-err.o): in function `.L0 ':    

-> vim build_dir/target-riscv64_riscv64_glibc/linux-riscv64/linux-5.11.22/scripts/Makefile
HOSTLDLIBS_extract-cert = $(CRYPTO_LIBS) -lpthread
```

```
/home/ubuntu/openwrt/staging_dir/host/bin/fakeroot /home/ubuntu/openwrt/scripts/ipkg-build -m "" /home/ubuntu/openwrt/build_dir/target-riscv64_riscv64_glibc/toolchain/ipkg-riscv64_riscv64/libgcc /home/ubuntu/openwrt/bin/targets/riscv64/generic-glibc/packages
mkdir: cannot create directory '/home/ubuntu/openwrt/bin/targets/riscv64/generic-glibc/packages/IPKG_BUILD.622457': Invalid argument

libfakeroot requires patch.
see : https://salsa.debian.org/clint/fakeroot/-/commit/57731fb5071b86d70d5a3e207addaabdde23111e

vim ./tools/fakeroot/patches/500-riscv.patch
--- a/libfakeroot.c
+++ b/libfakeroot.c
@@ -95,6 +95,8 @@
 #ifndef _STAT_VER
  #if defined (__aarch64__)
   #define _STAT_VER 0
+ #elif defined (__riscv) && __riscv_xlen==64
+  #define _STAT_VER 0
  #elif defined (__x86_64__)
   #define _STAT_VER 1
  #else


rm ./staging_dir/host/lib/libfakeroot*
rm ./staging_dir/host/bin/faked
pushd ./build_dir/host/fakeroot-1.25.3/
make clean
popd
make tools/fakeroot -j1 V=s

install -d -m0755 /home/ubuntu/openwrt/bin/targets/riscv64/generic-glibc/packages
/home/ubuntu/openwrt/staging_dir/host/bin/fakeroot /home/ubuntu/openwrt/scripts/ipkg-build -m "" /home/ubuntu/openwrt/build_dir/target-riscv64_riscv64_glibc/toolchain/ipkg-riscv64_riscv64/libgcc /home/ubuntu/openwrt/bin/targets/riscv64/generic-glibc/packages
fakeroot: preload library `/home/ubuntu/openwrt/staging_dir/host/lib/libfakeroot.so' not found, aborting.

# test:
# pushd ~/openwrt/build_dir/target-riscv64_riscv64_glibc/toolchain/ipkg-riscv64_riscv64/libgcc
## /home/ubuntu/openwrt/staging_dir/host/bin/tar -X ~/openwrt/bin/targets/riscv64/generic-glibc/packages/IPKG_BUILD.622634/tarX --format=gnu --sort=name -cpf - --mtime="Mon Jun 28 19:44:47 UTC 2021" . | /usr/bin/gzip -n - > ~/openwrt/bin/targets/riscv64/generic-glibc/packages/IPKG_BUILD.622634/data.tar.gz
# /home/ubuntu/openwrt/staging_dir/host/bin/tar -X ~/openwrt/bin/targets/riscv64/generic-glibc/packages/IPKG_BUILD.622634/tarX --format=gnu --sort=name -cpf ~/openwrt/bin/targets/riscv64/generic-glibc/packages/IPKG_BUILD.622634/data.tar --mtime="Mon Jun 28 19:44:47 UTC 2021" . 
# /usr/bin/gzip -n ~/openwrt/bin/targets/riscv64/generic-glibc/packages/IPKG_BUILD.622634/data.tar
# ls  /home/ubuntu/openwrt/bin/targets/riscv64/generic-glibc/packages/IPKG_BUILD.622634/ -lhart
# rm ~/openwrt/bin/targets/riscv64/generic-glibc/packages/IPKG_BUILD.622634/data.*
# popd

```

## Note
```
git diff HEAD > ../patch.diff
scp user@ip:~/patch.diff
```
