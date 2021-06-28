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

-> vim /home/ubuntu/openwrt/scripts/ipkg-build
mkdir -p $tmp_dir

still getting errors...

-> Runnning it manually
pushd ~/openwrt/build_dir/target-riscv64_riscv64_glibc/toolchain/ipkg-riscv64_riscv64/libgcc
/usr/bin/tar -X ~/openwrt/bin/targets/riscv64/generic-glibc/packages/IPKG_BUILD.622634/tarX --format=gnu --sort=name -cpf - --mtime="Mon Jun 28 19:44:47 UTC 2021" . | /usr/bin/gzip -n - > ~/openwrt/bin/targets/riscv64/generic-glibc/packages/IPKG_BUILD.622634/data.tar.gz
popd
```

## Note
```
git diff HEAD > ../patch.diff
scp user@ip:~/patch.diff
```
