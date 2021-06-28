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

SHELL= flock /home/ubuntu/openwrt/tmp/.linux-5.11.22.tar.xz.flock -c '          /home/ubuntu/openwrt/scripts/download.pl "/home/ubuntu/openwrt/dl" "linux-5.11.22.tar.xz" "366ba5bb00be28b604aac630c4f64301063892f27353b299177c396af0ad877f" "" "@KERNEL/linux/kernel/v5.x"    '
+ curl -f --connect-timeout 20 --retry 5 --location --insecure https://cdn.kernel.org/pub/linux/kernel/v5.x/linux-5.11.22.tar.xz
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100  112M  100  112M    0     0  3618k      0  0:00:31  0:00:31 --:--:-- 5305k
Hash of the downloaded file does not match (file: 11027c6114eb916edbcc37897226fb6263b2931911d2d5093550473ce1a57600, requested: 366ba5bb00be28b604aac630c4f64301063892f27353b299177c396af0ad877f) - deleting download.

go with 11027c6114eb916edbcc37897226fb6263b2931911d2d5093550473ce1a57600
```

getting library related problem
```
make -C /home/ubuntu/openwrt/build_dir/target-riscv64_riscv64_glibc/linux-riscv64/linux-5.11.22 KCFLAGS="-fmacro-prefix-map=/home/ubuntu/openwrt/build_dir/target-riscv64_riscv64_glibc=target-riscv64_riscv64_glibc" HOSTCFLAGS="-O2 -I/home/ubuntu/openwrt/staging_dir/host/include  -Wall -Wmissing-prototypes -Wstrict-prototypes" CROSS_COMPILE="riscv64-openwrt-linux-gnu-" ARCH="riscv" KBUILD_HAVE_NLS=no KBUILD_BUILD_USER="" KBUILD_BUILD_HOST="" KBUILD_BUILD_TIMESTAMP="Sat Jun 26 22:58:50 2021" KBUILD_BUILD_VERSION="0" HOST_LOADLIBES="-L/home/ubuntu/openwrt/staging_dir/host/lib" KBUILD_HOSTLDLIBS="-L/home/ubuntu/openwrt/staging_dir/host/lib" CONFIG_SHELL="bash" V=1  cmd_syscalls=  KERNELRELEASE=5.11.22 CC="riscv64-openwrt-linux-gnu-gcc" modules 
make[5]: Entering directory '/home/ubuntu/openwrt/build_dir/target-riscv64_riscv64_glibc/linux-riscv64/linux-5.11.22' 
make -f ./scripts/Makefile.build obj=scripts/basic 
rm -f .tmp_quiet_recordmcount
make -f ./scripts/Makefile.build obj=scripts/dtc 
make -f ./scripts/Makefile.build obj=scripts                            
make -f ./scripts/Makefile.build obj=scripts/gcc-plugins \  
need-builtin= \   
need-modorder=   
gcc -Wp,-MMD,scripts/.extract-cert.d -Wall -Wmissing-prototypes -Wstrict-prototypes -O2 -fomit-frame-pointer -std=gnu89  -O2 -I/home/ubuntu/openwrt/staging_dir/host/include  -Wall -Wmissing-prototypes -Wstrict-prototypes   -I/home/ubuntu/openwrt/staging_dir/host/include -I/home/ubuntu/openwrt/staging_dir/host/include    -o scripts/extract-cert scripts/extract-cert.c -L/home/ubuntu/openwrt/staging_dir/host/lib -L/home/ubuntu/openwrt/staging_dir/host/lib -lcrypto   
/usr/bin/ld: /home/ubuntu/openwrt/staging_dir/host/lib/libcrypto.a(libcrypto_la-eng_all.o): in function `.L0 ':      
eng_all.c:(.text+0x24): undefined reference to `pthread_once' 
/usr/bin/ld: /home/ubuntu/openwrt/staging_dir/host/lib/libcrypto.a(libcrypto_la-err.o): in function `.L0 ':    
err.c:(.text+0x9b8): undefined reference to `pthread_once'  
/usr/bin/ld: /home/ubuntu/openwrt/staging_dir/host/lib/libcrypto.a(libcrypto_la-err_all.o): in function `ERR_load_crypto_strings_internal':   
err_all.c:(.text+0x88): undefined reference to `pthread_once'            

/usr/bin/ld: /home/ubuntu/openwrt/staging_dir/host/lib/libcrypto.a(libcrypto_la-c_all.o): in function `.L0 ':    
c_all.c:(.text+0x794): undefined reference to `pthread_once'            
/usr/bin/ld: c_all.c:(.text+0x7a8): undefined reference to `pthread_once'    

/usr/bin/ld: /home/ubuntu/openwrt/staging_dir/host/lib/libcrypto.a(libcrypto_la-crypto_init.o):crypto_init.c:(.text+0x4c): more undefined references to `pthread_once' follow 
collect2: error: ld returned 1 exit status          

make[6]: *** [scripts/Makefile.host:95: scripts/extract-cert] Error 1
make[5]: *** [Makefile:1197: scripts] Error 2 
make[5]: Leaving directory '/home/ubuntu/openwrt/build_dir/target-riscv64_riscv64_glibc/linux-riscv64/linux-5.11.22'  
make[4]: *** [Makefile:24: /home/ubuntu/openwrt/build_dir/target-riscv64_riscv64_glibc/linux-riscv64/linux-5.11.22/.modules] Error 2    
make[4]: Leaving directory '/home/ubuntu/openwrt/target/linux/riscv64'     
make[3]: *** [Makefile:11: compile] Error 2  
make[3]: Leaving directory '/home/ubuntu/openwrt/target/linux' 
time: target/linux/compile#5.92#2.68#8.52  
ERROR: target/linux failed to build. 
make[2]: *** [target/Makefile:25: target/linux/compile] Error 1  
make[2]: Leaving directory '/home/ubuntu/openwrt' 
make[1]: *** [target/Makefile:18: /home/ubuntu/openwrt/staging_dir/target-riscv64_riscv64_glibc/stamp/.target_compile] Error 2    
..
```
## Note
```
git diff HEAD > ../patch.diff
scp user@ip:~/patch.diff
```
