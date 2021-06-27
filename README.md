Based on 
https://github.com/openwrt/openwrt
commit 85cef1cf221a4786fb8b1531748a5a0790101a3a (HEAD -> openwrt-21.02, origin/openwrt-21.02)

Requires running riscv machine

```
sudo apt install build-essential gcc binutils bzip2 flex python3 perl make grep unzip gawk subversion zlib1g-dev libc6-dev rsync libncurses5-dev libncursesw5-dev
git clone https://github.com/openwrt/openwrt
git checkout 85cef1cf221a4786fb8b1531748a5a0790101a3a
# apply patch 
make menuconfig
make
```
