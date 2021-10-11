[TOC]

# Linux常用命令





# VSCode SSH 到 CentOS6.10

```shell
# 需要更新glibc和libstdc++
# Update glibc and static libs
wget http://copr-be.cloud.fedoraproject.org/results/mosquito/myrepo-el6/epel-6-x86_64/glibc-2.17-55.fc20/glibc-2.17-55.el6.x86_64.rpm
wget http://copr-be.cloud.fedoraproject.org/results/mosquito/myrepo-el6/epel-6-x86_64/glibc-2.17-55.fc20/glibc-common-2.17-55.el6.x86_64.rpm
wget http://copr-be.cloud.fedoraproject.org/results/mosquito/myrepo-el6/epel-6-x86_64/glibc-2.17-55.fc20/glibc-devel-2.17-55.el6.x86_64.rpm
wget http://copr-be.cloud.fedoraproject.org/results/mosquito/myrepo-el6/epel-6-x86_64/glibc-2.17-55.fc20/glibc-headers-2.17-55.el6.x86_64.rpm
wget https://copr-be.cloud.fedoraproject.org/results/mosquito/myrepo-el6/epel-6-x86_64/glibc-2.17-55.fc20/glibc-utils-2.17-55.el6.x86_64.rpm
wget https://copr-be.cloud.fedoraproject.org/results/mosquito/myrepo-el6/epel-6-x86_64/glibc-2.17-55.fc20/glibc-static-2.17-55.el6.x86_64.rpm
wget https://copr-be.cloud.fedoraproject.org/results/mosquito/myrepo-el6/epel-6-x86_64/glibc-2.17-55.fc20/glibc-debuginfo-2.17-55.el6.x86_64.rpm
wget https://copr-be.cloud.fedoraproject.org/results/mosquito/myrepo-el6/epel-6-x86_64/glibc-2.17-55.fc20/glibc-debuginfo-common-2.17-55.el6.x86_64.rpm
sudo rpm -Uh --force --nodeps \
    glibc-2.17-55.el6.x86_64.rpm \
    glibc-common-2.17-55.el6.x86_64.rpm \
    glibc-devel-2.17-55.el6.x86_64.rpm \
    glibc-headers-2.17-55.el6.x86_64.rpm \
    glibc-static-2.17-55.el6.x86_64.rpm \
    glibc-utils-2.17-55.el6.x86_64.rpm \
    glibc-debuginfo-2.17-55.el6.x86_64.rpm \
    glibc-debuginfo-common-2.17-55.el6.x86_64.rpm

# Update libstdc++
wget https://copr-be.cloud.fedoraproject.org/results/mosquito/myrepo-el6/epel-6-x86_64/gcc-4.8.2-16.3.fc20/libstdc++-4.8.2-16.3.el6.x86_64.rpm
wget  https://copr-be.cloud.fedoraproject.org/results/mosquito/myrepo-el6/epel-6-x86_64/gcc-4.8.2-16.3.fc20/libstdc++-devel-4.8.2-16.3.el6.x86_64.rpm
wget https://copr-be.cloud.fedoraproject.org/results/mosquito/myrepo-el6/epel-6-x86_64/gcc-4.8.2-16.3.fc20/libstdc++-static-4.8.2-16.3.el6.x86_64.rpm
sudo rpm -Uh \
    libstdc++-4.8.2-16.3.el6.x86_64.rpm \
    libstdc++-devel-4.8.2-16.3.el6.x86_64.rpm \
    libstdc++-static-4.8.2-16.3.el6.x86_64.rpm
```



此时g++就不能用了，需要安装对应版本的g++

```shell
# 由于安装g++也需要对应版本的gcc，所以gcc也要升级一下
# 经过尝试，升级gcc版本失败，依赖的较多，还是自己编译高版本吧
wget https://copr-be.cloud.fedoraproject.org/results/mosquito/myrepo-el6/epel-6-x86_64/gcc-4.8.2-16.3.fc20/gcc-4.8.2-16.3.el6.x86_64.rpm
wget https://copr-be.cloud.fedoraproject.org/results/mosquito/myrepo-el6/epel-6-x86_64/gcc-4.8.2-16.3.fc20/gcc-c++-4.8.2-16.3.el6.x86_64.rpm
wget https://copr-be.cloud.fedoraproject.org/results/mosquito/myrepo-el6/epel-6-x86_64/gcc-4.8.2-16.3.fc20/cpp-4.8.2-16.3.el6.x86_64.rpm

sudo rpm -Uh \
    gcc-4.8.2-16.3.el6.x86_64.rpm \
    gcc-c++-4.8.2-16.3.el6.x86_64.rpm \
    cpp-4.8.2-16.3.el6.x86_64.rpm
```



# 驱动开发环境

## Debian安装驱动开发环境

```shell
# 安装开发所需的头文件
apt-get install linux-headers-`uname -r`
```



## Ubuntu安装驱动开发环境

```shell
sudo apt-get install linux-kernel-headers kernel-package
sudo apt-get install build-essential

apt-get install linux-image												// 内核版本
apt-get install linux-source											// 内核源码
apt-get install linux-image-`uname -r`-dbgsym							// 调试符号
```

dbgsym地址：http://ddebs.ubuntu.com/



[（转载）ubuntu 安装 dbgsym (debug-info) - 云+社区 - 腾讯云 (tencent.com)](https://cloud.tencent.com/developer/article/1637887)

1. ubuntu的默认仓库上是没有`dbgsym`的，所以需要添加额外的软件源`/etc/apt/sources.list`：

```shell
deb http://ddebs.ubuntu.com/ <版本代号> main
deb http://ddebs.ubuntu.com/ <版本代号>-updates main
```



2. 添加源公钥key

首次`update`后，可能会报错

```shell
正在读取软件包列表... 完成                                                                                                         
W: GPG 错误：http://ddebs.ubuntu.com precise Release: 由于没有公钥，无法验证下列签名： NO_PUBKEY ECDCAD72428D7C01
W: GPG 错误：http://ddebs.ubuntu.com precise-updates Release: 由于没有公钥，无法验证下列签名： NO_PUBKEY ECDCAD72428D7C01
```

这是因为源公钥没有添加，可使用如下命令从 ubuntu keyserver 添加该源的公钥.

```shell
sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-keys ECDCAD72428D7C01
```



3. 更新源信息

```shell
sudo apt-get update
```



4. 查找和安装dbgsym软件包

更新源后即可像安装普通软件包一样查找和安装 dbgsym，查找时可带上 "-dbgsym" 后缀. 如要查找内核相关 dbgsym，可使用如下命令搜索:

```shell
sudo aptitude search 'linux-image-.*-dbgsym'
```

也可带上版本号直接搜索当前内核的 dbgsym：

```shell
sudo aptitude search 'linux-image-'$(uname -r)'-dbgsym'
```

由于 linux 内核版本也经常迭代更新，可能当前内核版本的 dbgsym 已经在仓库上下线，不能找到和安装对应的 dbgsym， 此时建议系统重装一个存在对应 dbgsym 的内核版本. 如果对最新版本内核没有强需求，建议从 dbgsym 列表中选择一个版本较旧但相对稳定未下线的版本.

```shell
sudo aptitude install linux-{image,headers}-3.13.0-32-generic linux-image-3.13.0-32-generic-dbgsym
```

linux-image 建议 （suggests) 安装 linux-headers，但不是推荐 (recommends)， 使用 aptitude 安装软件包时默认会安装推荐的软件包, 但是对建议的软件包不会管, 建议的软件包通常有很多是不需要的, 如果需要安装可以手动指定。



## CentOS安装驱动开发环境

需要打开`/etc/yum.repos.d/CentOS-Debuginfo.repo`

| 安装包                  | 安装目录                                                     |
| ----------------------- | ------------------------------------------------------------ |
| kernel-devel            | release版驱动开发头文件				\usr\src\kernels\3.10.0-693.el7.x86_64\ |
| kernel-debuginfo        | release版内核调试符号					\usr\lib\debug\lib\modules\3.10.0-693.el7.x86_64\ |
| kernel-debuginfo-common | 内核源码											\usr\src\debug\kernel-3.10.0-693.el7\linux-3.10.0-693.el7.x86_64\ |
| kernel-debug            | debug版内核                                                  |
| kernel-debug-devel      | debug版驱动开发头文件					\usr\src\kernels\3.10.0-693.el7.x86_64.debug\ |
| kernel-debug-debuginfo  | debug版内核调试符号						\usr\lib\debug\lib\modules\3.10.0-693.el7.x86_64.debug\ |

```shell
yum install gcc cmake
```



## 查看内核导出函数

方式1:

```shell
[root@localhost ~]# cat /proc/kallsyms | grep kallsyms_lookup_name
ffffffff811017c0 T module_kallsyms_lookup_name
ffffffff81102330 T kallsyms_lookup_name
ffffffff819a9a40 r __ksymtab_kallsyms_lookup_name
ffffffff819bb580 r __kcrctab_kallsyms_lookup_name
ffffffff819c2d0a r __kstrtab_kallsyms_lookup_name
```



方式2:

> 如果有安装对应的内核源码的话，可以使用方式2

```shell
[root@localhost 3.10.0-693.el7.x86_64]# cat /usr/src/kernels/3.10.0-693.el7.x86_64/System.map | grep kallsyms_lookup_name
ffffffff811017c0 T module_kallsyms_lookup_name
ffffffff81102330 T kallsyms_lookup_name
ffffffff819a9a40 r __ksymtab_kallsyms_lookup_name
ffffffff819bb580 r __kcrctab_kallsyms_lookup_name
ffffffff819c2d0a r __kstrtab_kallsyms_lookup_name
```



方式3:

```shell
[root@localhost 3.10.0-693.el7.x86_64]# cat /boot/System.map-3.10.0-693.el7.x86_64 | grep kallsyms_lookup_name
ffffffff811017c0 T module_kallsyms_lookup_name
ffffffff81102330 T kallsyms_lookup_name
ffffffff819a9a40 r __ksymtab_kallsyms_lookup_name
ffffffff819bb580 r __kcrctab_kallsyms_lookup_name
ffffffff819c2d0a r __kstrtab_kallsyms_lookup_name
```



方式4:

通过`kallsyms_lookup_name`函数去找，但要保证`kallsyms_lookup_name`有被导出



方式5:

> 如果有安装对应版本的debuginfo的话，可以使用方式5

```shell
[root@localhost 3.10.0-693.el7.x86_64]# nm /usr/lib/debug/usr/lib/modules/3.10.0-693.el7.x86_64/vmlinux | grep kallsyms_lookup_name
00000000e007de41 A __crc_kallsyms_lookup_name
ffffffff81102330 T kallsyms_lookup_name
ffffffff819bb580 r __kcrctab_kallsyms_lookup_name
ffffffff819c2d0a r __kstrtab_kallsyms_lookup_name
ffffffff819a9a40 r __ksymtab_kallsyms_lookup_name
ffffffff811017c0 T module_kallsyms_lookup_name
```





# 编译内核所需

## 内核版本号规则

```shell
# Makefile
VERSION = 2
MAJOR REVISION = 6
MINOR REVISION = 18
BUG FIX = 8
EXTRAVERSION = -0.1-default
```

> this is the new versioning system officially adopted since kernel 2.6.11. Looking at the value automatically set in the Makefile, it seems the extraversion must include the bug fix, that is .8-0.1-default.



- Ubuntu:

  ```shell
  apt-get install libncurses5-dev libncursesw5-dev
  apt-get install libssl-dev
  
  # 安装源码文件
  apt-get install linux-source
  ```

- CentOS 编译所需：

	```shell
	yum install ncurses-devel bc
	yum install elfutils-libelf-devel
	```



## CentOS6.10 内核编译

```shell
# 下载源码包
wget https://mirrors.tuna.tsinghua.edu.cn/centos-vault/6.10/os/Source/SPackages/kernel-2.6.32-754.el6.src.rpm

# 安装
rpm -ivh kernel-2.6.32-754.el6.src.rpm
# warning: user mockbuild does not exist - using root
# warning: group mockbuild does not exist - using root

useradd mockbuild

su mockbuild

rpm -ivh kernel-2.6.32-754.el6.src.rpm

cd rpmbuild/SOURCES/

# 解压源码
tar -jvxf linux-2.6.32-754.el6.tar.bz2

cd linux-2.6.32-754.el6

make menuconfig
# Unable to find the ncurses libraries

yum install ncurses-devel

make menuconfig
```

![image-20210910102412830](images/Linux常用命令/image-20210910102412830.png)



编译选项：

```shell
Kernel hacking--->  
       -*- Magic SysRq key  
       [*] Kernel debugging  
       [*] Compile the kernel with debug info  
       [*] KGDB: kernel debugging with remote gdb --->  
              <*> KGDB: use kgdb over the serial console  
```



**把ext4等文件系统编译进内核，不然不好挂载文件系统。**



修改此目录下的`Makefile`中的`EXTRAVERSION`，如下：

```shell
VERSION = 2
PATCHLEVEL = 6
SUBLEVEL = 32
EXTRAVERSION =-754.el6.x86_64
NAME = Man-Eating Seals of Antiquity
RHEL_MAJOR = 6
RHEL_MINOR = 9
RHEL_RELEASE = 753
RHEL_DRM_VERSION = 4
RHEL_DRM_PATCHLEVEL = 4
RHEL_DRM_SUBLEVEL = 0
```

> 如果不修改此内容的话，内核加载后，会通过内核版本`uname -r`去`/lib/modules/`目录下找相同名的驱动目录，此属性值会影响`uname -r`的显示。



[鸟哥的 Linux 私房菜 -- Linux 核心编译 (vbird.org)](http://cn.linux.vbird.org/linux_basic/0540kernel_3.php)

1. 直接make的话，会把内核和模块全部编译，耗时长
2. 使用`make help`查看支持的编译参数
3. 常见如下：

```shell
[root@www linux-2.6.30.3]# make vmlinux  <==未经压缩的核心
[root@www linux-2.6.30.3]# make modules  <==仅核心模块
[root@www linux-2.6.30.3]# make bzImage  <==经压缩过的核心(默认)
[root@www linux-2.6.30.3]# make all      <==进行上述的三个动作
```



> 我们常见的在 /boot/ 底下的核心文件，都是经过压缩过的核心文件，因此，上述的动作中比较常用的是 modules 与 bzImage 这两个，其中 bzImage 第三个字母是英文大写的 I 喔！bzImage 可以制作出压缩过后的核心， 也就是一般我们拿来进行系统启动的资讯罗！所以，基本上我们会进行的动作是：
>
> ```shell
> [root@www linux-2.6.30.3]# make clean    <==先清除缓存档
> [root@www linux-2.6.30.3]# make bzImage  <==先编译核心
> [root@www linux-2.6.30.3]# make modules  <==再编译模块
> ```



一般调试的时候可以只编译`bzImage`

```shell
make bzImage

# crypto/signature/ksign-publickey.c:2:17: error: key.h: No such file or directory
# crypto/signature/ksign-publickey.c: In function ‘ksign_init’:
# crypto/signature/ksign-publickey.c:10: error: ‘ksign_def_public_key’ undeclared (first use in this function)
# crypto/signature/ksign-publickey.c:10: error: (Each undeclared identifier is reported only once
# crypto/signature/ksign-publickey.c:10: error: for each function it appears in.)
# crypto/signature/ksign-publickey.c:11: error: ‘ksign_def_public_key_size’ undeclared (first use in this function)
# make[2]: *** [crypto/signature/ksign-publickey.o] Error 1
# make[1]: *** [crypto/signature] Error 2
# make: *** [crypto] Error 2

make menuconfig
# Select "Enable loadable module support" Disable "Module signature verification (EXPERIMENTAL)"
# Select "Cryptographic API" Scroll down and Disable "In-kernel signature checker (EXPERIMENTAL)"

make bzImage
```



成功后，生成vmlinux和bzImage

```shell
[root@localhost linux-2.6.32-754.el6]# ll vmlinux
-rwxr-xr-x. 1 root root 135043524 Sep 10 03:57 vmlinux

[root@localhost linux-2.6.32-754.el6]# ll arch/x86/boot/bzImage 
-rw-r--r--. 1 root root 4309072 Sep 10 03:57 arch/x86/boot/bzImage
```



## 驱动/代码集成到内核

例，将一个文件系统代码`testfs`添加到内核

内核源码目录：`/root/rpmbuild/SOURCES/linux-2.6.32-754.el6`

```shell
# 将testfs拷贝到内核fs目录
cp testfs /root/rpmbuild/SOURCES/linux-2.6.32-754.el6/fs -r

cd  /root/rpmbuild/SOURCES/linux-2.6.32-754.el6/fs/testfs
```



给`testfs`添加配置文件`Kconfig`，例如：

```shell
config INFOSEC_FS
        tristate "The Infosecfs filesystem"
        default y
        help
          This is the next generation of the infosecfs filesystem.
```

修改`Makefile`文件如下：

```shell
obj-$(CONFIG_INFOSEC_FS) += infosecfs.o

infosecfs-y     := crypto.o dentry.o file.o inode.o main.o message.o \
                                        mmap.o process_ctrl.o read_write.o sm4.o \
                                        super.o tools.o
```



额外编译选项：

```shell
EXTRA_CFLAGS += -Wno-error
```





```shell
# 切换到fs目录
cd ..

vim Makefile
```

添加内容如下：

```shell
obj-$(CONFIG_INFOSEC_FS)                += testfs/
```



```shell
vim Kconfig
```

添加内容如下：

```shell
source "fs/testfs/Kconfig"
```

![image-20211011141236642](images/Linux常用命令/image-20211011141236642.png)








## 交叉编译内核

- mips

```shell
apt-get install gcc-mips64el-linux-gnuabi64
export ARCH=mips
export CROSS_COMPILE=mips64el-linux-gnuabi64-
```



- arm

```shell
apt-get install gcc-arm-linux-gnueabi gcc-aarch64-linux-gnu

export ARCH=arm
export CROSS_COMPILE=arm-linux-gnueabi-

export ARCH=arm64
export CROSS_COMPILE= aarch64-linux-gnu-
```



## 编译命令

编译步骤(不能乱)：

```shell
make menuconfig
make
make modules
make modules_install
make install
make headers_install
```

​			

如果遇到不能给驱动下断点(用`hbreak`命令可下断点，但还是不太好调试)的情况，需要修改编译选项：

```shell
CONFIG_DEBUG_SET_MODULE_RONX
CONFIG_DEBUG_RODATA
[CONFIG_RANDOMIZE_BASE] 						// 这个可以用启动参数 nokaslr 替代
```

这两个选项关闭后再编译一次内核



## 卸载多余的内核

```shell
dpkg --get-selections | grep linux
apt-get remove linux-image-xxxx
[apt-get purge linux-image-xxxx]
或
rm /boot/config-xxx /boot/System.map-xxx /boot/initrd.img-xxx /boot/vmlinuz-xxx
sudo update-grub
```



## 根文件系统

### CentOS/Fedora

```shell
mkinitrd /boot/initrd.img 2.6.32
```



### Debian/Ubuntu

```shell
mkinitramfs 2.6.32 -o /boot/initramfs.img
```



其中`2.6.32`为`/lib/modules`下的目录名称。





# qemu使用

```shell
apt-get install qemu
```

## CentOS6.10 编译qemu

[CentOS 7下Qemu编译安装 - 华为云 (huaweicloud.com)](https://www.huaweicloud.com/articles/023bb022255569c81600e6e372fa06c0.html)



1. 下载源码并配置

```shell
wget https://download.qemu.org/qemu-6.1.0.tar.xz
tar xvJf qemu-6.1.0.tar.xz
cd qemu-6.1.0
./configure
make
```



2. 安装python3.6

在`./configure`时提示需要`python3.6`

```shell
# 下载源码
wget https://www.python.org/ftp/python/3.6.15/Python-3.6.15.tgz

# 解压
tar -zvxf Python-3.6.15.tgz

# 创建安装目录
mkdir /usr/local/python3

cd Python-3.6.15

# 配置安装目录
./configure  --prefix=/usr/local/python3

# 编译
make

# 安装
make install
```

**验证yum是否受到影响，原因是yum是python写的脚本，如受影响需要修改它使用的python**

使用`yum --help`看看还能不能用

为了不影响其它的软件，就不把python3替换到/usr/bin/python了



3. 编译qemu

```shell
# 由于上面没有替换python，所以需要指定使用的python路径
./configure --python=/usr/local/python3/bin/python3

# ERROR: Cannot find Ninja
# /usr/local/python3/bin/pip3 install ninja
yum install ninja-build

# ERROR: "cc" either does not exist or does not work
# 到这失败
```



1. 降低版本编译

[CentOS 7下Qemu编译安装 - 华为云 (huaweicloud.com)](https://www.huaweicloud.com/articles/023bb022255569c81600e6e372fa06c0.html)

```shell
yum install git

git clone https://gitlab.com/qemu-project/qemu.git

cd qemu

# 选择2.10版本
git checkout stable-2.10

# 说明:
# ./configure -h 查看详细
# -- target-list 设置生成CPU兼容的编译结果,默认兼容所有,编译时间太长
# --audio-drv-list="oss,sdl"  是必须的，否则用生成的 QEMU 创建的虚拟机没有画面。启动虚拟机时只会显示一行: gvncviewer 127.0.0.1::5900 
./configure --target-list="x86_64-softmmu i386-softmmu x86_64-linux-user i386-linux-user" --audio-drv-list="oss,sdl"

# 提示没有SDL库
cd ..
wget http://www.libsdl.org/release/SDL2-2.0.16.tar.gz
tar zvxf SDL2-2.0.16.tar.gz 
cd SDL2-2.0.16
./configure
make
make install

cd ../qemu
# 继续编译qemu
./configure --target-list="x86_64-softmmu i386-softmmu x86_64-linux-user i386-linux-user" --audio-drv-list="oss,sdl"

# glib-2.22 gthread-2.0 is required to compile QEMU
yum install glib2
yum install glib2-devel

# ERROR: pixman >= 0.21.8 not present
yum install pixman-devel

make

make install
```



上面的不能显示

尝试使用v2.11.1版本编译

yum install SDL SDL-devel

编译通过，可以使用



## CentOS rootfs(由于找不到分区，qemu启动此fs失败，使用方式3)

为什么方式1和2都会失败，感觉可能是由于编译内核时，有的驱动是以模块的形式放到initramfs中的，导致rootfs中没有对应的驱动，故不能挂载硬盘，也不能识别分区。



### 方式1:

```shell
dd if=/dev/zero of=CentOSRootfs.img bs=1M seek=2047 count=1

mkfs.ext4 -F CentOSRootfs.img

mkdir -p /mnt/rootfs

mount -o loop CentOSRootfs.img /mnt/rootfs/

wget https://raw.githubusercontent.com/CentOS/sig-cloud-instance-images/CentOS-6.10/docker/centos-6-docker.tar.xz

tar xvf centos-6-docker.tar.xz -C /mnt/rootfs/

minorver=6.10
sudo sed -e "s|^mirrorlist=|#mirrorlist=|g" \
		-e "s|^#baseurl=http://mirror.centos.org/centos/\$releasever|baseurl=https://mirrors.tuna.tsinghua.edu.cn/centos-vault/$minorver|g" \
		-i.bak \
		/mnt/rootfs/etc/yum.repos.d/CentOS-*.repo


chroot /mnt/rootfs/ /bin/bash

yum update -y

# 获取到 kernel
yum install -y yum-utils
rpm -Uvh --nodeps $(repoquery --location kernel-core)

exit

umount /mnt/rootfs
```



### 方式2:

```shell
# 需要 epel 源
yum install debootstrap

1. 安装Ubuntu虚拟机
2. 安装qemu
	apt-get install qemu-system-x86
3. 安装debootstrap
	apt-get install debootstrap
	apt-get install debian-keyring
	apt-get install debian-archive-keyring
4. 下载img构建脚本
	cd $IMAGE/
	wget https://raw.githubusercontent.com/google/syzkaller/master/tools/create-image.sh -O create-image.sh
	chmod +x create-image.sh
	./create-image.sh
	
	4.1 默认生成的是 ubuntu 的 stretch 版本
	4.2 修改 ubuntu 版本
		./create-image.sh --distribution buster
	4.3 默认情况下不是所有的feature都安装，可全部安装
		./create-image.sh --feature full
```



### 方式3(建议使用):

建议在ubuntu系统上使用此方式，创建好的根文件系统再拿到对应的机器上。

```shell
# 创建一个足够大小的安装盘
qemu-img create -f qcow2 centos.qcow2 5G

# 通过安装镜像安装到硬盘
qemu-system-x86_64 -cdrom CentOS-6.10-x86_64-minimal.iso -drive file=centos.qcow2,format=qcow2

# 安装完成后，进入系统查看/etc/fstab根目录挂载的设备，如/dev/sda，并从/boot/grub/grub.cfg中拿到启动参数
# 并将/boot/initramfs拿出来
qemu-system-x86_64 -drive file=centos.qcow2,format=qcow2 -m 2G -smp 2

# 使用自编译内核启动
qemu-system-x86_64 -kernel centos6.10/bzImage -hda centos6.10/centos.qcow2 -append "root=/dev/mapper/vg_test-lv_root LANG=en_US.UTF-8 crashkernel=auto  KEYBOARDTYPE=pc KEYTABLE=us rhgb quiet" -m 2G -initrd /root/initramfs-2.6.32-754.el6.x86_64.img
```

相关链接：

[linux - How to use custom image kernel for ubuntu in qemu? - Stack Overflow](https://stackoverflow.com/questions/65951475/how-to-use-custom-image-kernel-for-ubuntu-in-qemu)



## qemu编译

```shell
# 下载源码
wget https://download.qemu.org/qemu-5.2.0.tar.xz

# 解压源码
tar Jvxf qemu-5.2.0.tar.xz

# 安装依赖
apt install python3-pip
pip3 install ninja
apt-get install pkg-config libglib2.0-dev libpixman-1-dev

# 编译
cd qemu-5.2.0
./configure
make
make install
```

## qemu 安装debian mips虚拟机

[qemu 安装debian mips虚拟机 - 简书 (jianshu.com)](https://www.jianshu.com/p/c9dd8e708550)

[Building a Debian Stretch QEMU image for MIPSel - Blah Cats](https://blahcat.github.io/2017/07/14/building-a-debian-stretch-qemu-image-for-mipsel/)

### 1.下载必备程序

```shell
# 发行版安装程序
wget http://ftp.debian.org/debian/dists/stretch/main/installer-mipsel/current/images/malta/netboot/initrd.gz

# 内核
wget http://ftp.debian.org/debian/dists/stretch/main/installer-mipsel/current/images/malta/netboot/vmlinux-4.9.0-13-4kc-malta
```

### 2.创建硬盘

```shell
# 硬盘驱动器
qemu-img create -f qcow2 disk.qcow2 15G
```

### 3.开始安装

```shell
# 运行qemu安装mips debian
$ qemu-system-mipsel -M malta -m 1G \
  -hda ./disk.qcow2 \
  -initrd ./initrd.gz \
  -kernel ./vmlinux-4.9.0-13-4kc-malta -append "nokaslr" \
  -nographic
```



## qemu 安装debian mips 龙芯虚拟机(未成功)

需要编译最新的6.0版本

```shell
wget https://download.qemu.org/qemu-6.0.0-rc1.tar.xz
tar xvJf qemu-6.0.0-rc1.tar.xz
cd qemu-6.0.0-rc1
./configure
make
```



### 1.下载必备程序

```shell
# 发行版安装程序
wget http://ftp.debian.org/debian/dists/stretch/main/installer-mips64el/current/images/loongson-3/netboot/initrd.gz

# 内核
wget http://ftp.debian.org/debian/dists/stretch/main/installer-mips64el/current/images/loongson-3/netboot/vmlinux-4.9.0-13-loongson-3
```

### 2.创建硬盘

```shell
# 硬盘驱动器
qemu-img create -f qcow2 disk.qcow2 15G
```

### 3.查看CPU支持列表

```shell
# 显示支持的machine 类型
qemu-system-mips64el -machine help
```

### 4.开始安装

```shell
# 运行qemu安装mips debian 启动不起来
qemu-system-mips64el -M loongson3-virt -cpu Loongson-3A1000 -m 2G \
-hda ./disk.qcow2 \
-initrd ./initrd.gz \
-kernel ./vmlinux-4.9.0-13-loongson-3 -append "nokaslr" \
-nographic
```



```shell
# 启动不起来
qemu-system-mips64el -M loongson3-virt \
-device qemu-xhci,id=xhci -device usb-tablet,bus=xhci.0 \
-kernel ./vmlinux-4.9.0-13-loongson-3 \
-initrd ./initrd.gz \
-append "console=ttyS0,115200" \
-drive file=./disk.qcow2,if=virtio \
-nographic
```



```shell
# 启动不起来
qemu-system-mips64el -M malta -m 2G \
-hda ./disk.qcow2 \
-initrd ./initrd.gz \
-kernel ./vmlinux-4.9.0-13-5kc-malta -append "nokaslr" \
-nographic
```



```shell
$ qemu-system-mips64el -M malta -cpu MIPS64R2-generic -m 2G \
  -hda ./disk.qcow2 \
  -initrd ./initrd.gz \
  -kernel ./vmlinux-4.9.0-13-5kc-malta -append "nokaslr" \
  -nographic
```



## vnc

由于CentOS6.10上编译的qemu总是提示不支持SDL，故使用vnc显示

```shell
# 绑定本机ip : 5900 + prot
-vnc :5
```



## 调试

```shell
# gdbserver 在 1234 端口
-s

# 等待gdb连接
-S
```



```shell
# qemu-start.sh

#!/bin/sh
qemu-system-x86_64 \
       -kernel /home/superman/Desktop/kernelsrc/kernel-2.6.32-754.src/arch/x86/boot/bzImage \
       -hda centos.qcow2 \
       -append "root=/dev/mapper/vg_test-lv_root nokaslr" \
       -m 2G \
       -initrd initramfs-2.6.32-754.el6.x86_64.img \
       -s \
       -S
```





# 编译

## 安装编译工具链

1. Ubuntu

```shell
apt install build-essential
apt install cmake
```



2. CentOS

```shell
yum install gcc gcc-c++ cmake
```



## CMake 开发环境

### 重命名

```cmake
# 修改字首
set_target_properties(new_thing PROPERTIES PREFIX "")

# 修改输出名称
set_target_properties(new_thing PROPERTIES OUTPUT_NAME "better_name")

# 修改后缀
set_target_properties(new_thing PROPERTIES SUFFIX ".so.1")
```



### 生成compile_commands.json

这个文件一般是vim的clangd(LSP)插件使用。

```cmake
set(CMAKE_EXPORT_COMPILE_COMMANDS 1)
```



### 指定生成目录

```cmake
# 设置输出目录
set(EXECUTABLE_OUTPUT_PATH ${PROJECT_SOURCE_DIR}/bin)
message(${EXECUTABLE_OUTPUT_PATH})

set(LIBRARY_OUTPUT_PATH ${PROJECT_SOURCE_DIR}/bin/lib)
message(${LIBRARY_OUTPUT_PATH})
```



### 添加宏定义

```cmake
ADD_DEFINITIONS(-DMACRO)
```



### 编译库

```cmake
cmake_minimum_required(VERSION 3.10)

project(plugin1)

include_directories(../../include)

add_library(plugin1 [SHARED | STATIC]  plugin1.cpp)
```





### 添加链接库

```cmake
target_link_libraries(${PROJECT_NAME} -ldl </lib/test.so> </lib/static.a>)
```



### 判断编译平台

方式1:

```cmake
MESSAGE(STATUS "operation system is ${CMAKE_SYSTEM}")
 
IF (CMAKE_SYSTEM_NAME MATCHES "Linux")
	MESSAGE(STATUS "current platform: Linux ")
ELSEIF (CMAKE_SYSTEM_NAME MATCHES "Windows")
	MESSAGE(STATUS "current platform: Windows")
ELSEIF (CMAKE_SYSTEM_NAME MATCHES "FreeBSD")
	MESSAGE(STATUS "current platform: FreeBSD")
ELSE ()
	MESSAGE(STATUS "other platform: ${CMAKE_SYSTEM_NAME}")
ENDIF (CMAKE_SYSTEM_NAME MATCHES "Linux")
 
MESSAGE(STSTUS "###################################")
```

方式2:

```cmake
IF (WIN32)
	MESSAGE(STATUS "Now is windows")
ELSEIF (APPLE)
	MESSAGE(STATUS "Now is Apple systens.")
ELSEIF (UNIX)
	MESSAGE(STATUS "Now is UNIX-like OS's.")
ENDIF ()
```







## Makefile

### make指定Makefile

```shell
# make 默认会找当前目录下的Makefile，也可通过 -f 参数指定文件
make -f <Makefile>
```



## 交叉编译openssl

1. 下载openssl源码
2. 安装交叉编译工具链
3. 配置好交叉编译链的环境变量：`export PATH=$PATH:/usr/local/arm-build/bin`
4. 修改环境变量`CC`：`export CC=arm-linux-gnueabihf-gcc`
5. `.config`
6. 手动修改生成的Makefile，找到有`-m64`的地方删除
7. make & make install





# 调试



## gdb

详细见：[awesome-cheatsheets/gdb.txt at master · supermanc88/awesome-cheatsheets (github.com)](https://github.com/supermanc88/awesome-cheatsheets/blob/master/tools/gdb.txt)

### 常用命令

```shell
```



### 设置源码目录

正常情况下，二进制文件会根据调试编译的调试信息去找源码路径，但如果源码路径移动了，就需要以下代码调整

```shell
# set substitute-path 现路径 原路径
set substitute-path /root/rpmbuild/SOURCES/linux-2.6.32-754.el6/ /home/superman/Desktop/kernelsrc/kernel-2.6.32-754.src
```



### vscode 中 使用 gdb

```shell
# launch.json

{
    // 使用 IntelliSense 了解相关属性。 
    // 悬停以查看现有属性的描述。
    // 欲了解更多信息，请访问: https://go.microsoft.com/fwlink/?linkid=830387
    "version": "0.2.0",
    "configurations": [
        {
            "name": "(gdb) 启动",
            "type": "cppdbg",
            "request": "launch",
            "program": "${workspaceFolder}/vmlinux",
            "args": [],
            "stopAtEntry": false,
            "cwd": "${fileDirname}",
            "environment": [],
            "externalConsole": false,
            "MIMode": "gdb",
            "miDebuggerServerAddress": "127.0.0.1:1234",
            "sourceFileMap": {
                "/root/rpmbuild/SOURCES/linux-2.6.32-754.el6/" : "/home/superman/Desktop/kernelsrc/kernel-2.6.32-754.src"
            },
            "setupCommands": [
                {
                    "description": "为 gdb 启用整齐打印",
                    "text": "-enable-pretty-printing",
                    "ignoreFailures": true
                }
            ]
        }
    ]
}
```



## vscode

### gdb

见gdb章节。



## 内核调试

### 双机调试

- 在启动参数后加入[`/boot/grub/grub.cfg`]：

  - 被调试机：
    `kgdboc=ttyS0,115200 nokaslr kgdbwait`
    虚拟机设置
    串口：`//./pipe/com_1`
    该端是客户端
    另一端是虚拟机

  - 主机：
    `kgdboc=ttyS0,115200`
    虚拟机设置
    串口：`//./pipe/com_1`
    该端是服务端
    另一端是虚拟机

    驱动调试
    `dmesg`		查看驱动打印
    其实日志是写到这个文件：`/var/log/message`

- 驱动操作命令：

```shell
insmod / modprobe						加载驱动
rmmod									卸载驱动
lsmod									查看系统中所有已经被加载了的所有的模块以及模块间的依赖关系
modinfo									获得模块的信息
```



#### 设置被调试机

环境：CentOS7

```shell
vim /boot/grub2/grub.cfg 
```

![image-20201128150726724](images/Linux常用命令/image-20201128150726724.png)

> kgdboc=ttyS0,115200	设置串口
>
> nokaslr	禁用基址随机化
>
> kgdbwait	开启直接等待

![image-20201128150908892](images/Linux常用命令/image-20201128150908892.png)

> 添加串行端口，使用命名管道：
>
> //./pipe/com_1
>
> 该端是客户端
>
> 另一端是虚拟机



#### 设置主机

```shell
vim /boot/grub2/grub.cfg 
```

![image-20201128152144204](images/Linux常用命令/image-20201128152144204.png)

![image-20201128151057937](images/Linux常用命令/image-20201128151057937.png)

> 添加串行端口，使用命名管道：
>
> //./pipe/com_1
>
> 该端是服务端
>
> 另一端是虚拟机



### qemu调试

#### qemu文件系统

见qemu使用章节。

#### qemu脚本

```shell
#!/bin/sh
qemu-system-x86_64 \
       -kernel /home/superman/Desktop/kernelsrc/kernel-2.6.32-754.src/arch/x86/boot/bzImage \
       -hda centos.qcow2 \
       -append "root=/dev/mapper/vg_test-lv_root nokaslr" \
       -m 2G \
       -initrd initramfs-2.6.32-754.el6.x86_64.img \
       -s \
       -S
```



### 打印调试信息

```shell
# 会一直打印，需要Ctrl－C手动终止
cat /proc/kmsg 

# N为一数字，表示显示最后N行
dmesg 或 dmesg | tail -N 

# 清空dmesg缓存
# 会打印原来的数据
dmesg -c
# 不打印直接清空
dmesg -C
```



### 模块调试：

#### 安装卸载

```shell
# 安装
insmod module_name.ko

# 卸载
rmmod module_name
```



#### 查看模块信息

```shell
modinfo module_name
# 如下：
root@superman-PC:~/linuxkbd# modinfo LinuxKbd.ko
filename:       /root/linuxkbd/LinuxKbd.ko
license:        GPL
version:        v1.0.1
srcversion:     56D00E9624437478DB00C95
depends:        
retpoline:      Y
name:           LinuxKbd
vermagic:       5.4.50-amd64-desktop SMP mod_unload 
```



#### 获取模块基址

```shell
# 方式1
cat /proc/modules | grep module_name

# 方式2
cat /sys/module/module_name/section/.text
```

#### 加载符号

```shell
add-symbol-file /path/module_name.ko module_base_address
```

1. 被调试机：
   	echo g > /proc/sysrq-trigger

> 中断不生效，这是因为sysrq在系统未开启：
> echo 1 > /proc/sys/kernel/sysrq

> 单步时间过长会导致被调试机卡死，使用以下命令禁用时长
> echo 0 > /proc/sys/kernel/hung_task_timeout_secs

2. 主机使用gdb vmlinux 后命令：

```shell
add-symbol-file /home/superman/Desktop/drivers/linuxkdb/LinuxKbd.ko 0xffffffffc0114000

# 进入等待状态，直到连接被调试机
target remote /dev/ttyS0

#后续调试见gdb命令
```

> Warning:
> Cannot insert breakpoint 1.
> Error accessing memory address 0xfa4581ae: Unknown error 4294967295
> hbreak [breakpoints]
> 详见：[https://www.linuxquestions.org/questions/programming-9/kgdb-module-debugging-question-906503/]

#### 驱动build目录

```shell
/lib/modules/$(shell uname -r)/build
```

#### 安装的带符号的vmlinux路径

```shell
/lib/debug/lib/modules/$(shell uname -r)/
```



### crash

```shell
/var/crash/

# Ubuntu安装：
apt install linux-crashdump

# CentOS安装：
yum install crash

# 配置：
kdump-config show
```





# 软件安装/卸载

## dpkg

### 是否安装

```shell
# 列出安装的软件包
dpkg -l

dpkg -l | grep gcc
```

### *.deb安装

```shell
dpkg -i *.deb
```



## yum

```shell
# 清空软件源缓存
yum clean all

# 更新软件源
yum makecache
```



```shell
# 显示已安装的软件包
yum list installed

# 查找可安装的软件包
yum search xxx

# 安装软件包
yum install java-1.8.0-openjdk-devel
```



## rpm

### 安装

```shell
rpm -iv xxx.rpm

# 升级
rpm -Uh xxx.rpm
```



### 卸载

```shell
# 查询到包名再卸载
rpm -qa | grep epel
# epel-release-7-13.noarch
rpm -e epel-release-7-13.noarch
```





### 是否安装

```shell
rpm -qa | grep nginx
```



### 安装位置

```shell
# 查看程序隶属的包
rpm -qf /usr/sbin/nginx

# 通过包名查看包中的所有文件及路径
rpm -ql nginx-1.12.1-1.el7.ngx.x86_64
```



## apt

```shell
apt list --installed																		列出安装的软件包
apt remove [python3-apt 包名]
```



## 通用查找软件路径

### 查看安装路径

```shell
whereis nginx
# /usr/sbin/nginx ---执行路径
# /usr/lib64/nginx ---安装路径
# /etc/nginx/ --- 配置路径
# /usr/share/nginx ---默认站点目录
```



### 查看执行路径

```shell
which nginx
# /usr/sbin/nginx
```



# 图形界面

## xfce4

```shell
$sudo apt-get install xfce4

# 启动
startx
```

```shell
# 以下命令未测试

# 可用以命令显示当前系统安装的窗口管理器：
$sudo update-alternatives --display x-window-manager

# 用以下命令设置默认的启动项目：
$sudo update-alternatives --config x-window-manager
```



## gnome

```shell
# 以下命令测试未成功，暂时用不上

# 然后我们安装图形界面, 执行这句的时候目测会报错，不过别慌：
apt-get install gnome

apt-get update

apt-get install x-window-system-core gnome-core
```





# 系统

## 修改root密码

在启动时修改kernel启动参数，在后面添加`single`或数字`1`，会直接进入系统，之后使用命令`passwd root`

## 关闭ASLR 基址随机

方式1:

```shell
echo 0 > /proc/sys/kernel/randomize_va_space
```



方式2:

在`/boot/grub/grub.conf`的启动参数后加`nokaslr`



## 定时任务

`crond`命令每分钟会定期检查是否有要执行的工作，如果有要执行的工作便会自动执行该工作。

注意：新创建的`cron`任务，不会马上执行，至少要过2分钟后才可以，当然可以重启cron来马上执行。

```shell
crontab [-u user] file
```

或

```shell
crontab [-u user] {-l | -r | -e}
```



**说明：**

crontab 是用来让使用者在固定时间或固定间隔执行程序之用，换句话说，也就是类似使用者的时程表。

-u user 是指设定指定 user 的时程表，这个前提是你必须要有其权限(比如说是 root)才能够指定他人的时程表。如果不使用 -u user 的话，就是表示设定自己的时程表。

**参数说明**：

- -e : 执行文字编辑器来设定时程表，内定的文字编辑器是 VI，如果你想用别的文字编辑器，则请先设定 VISUAL 环境变数来指定使用那个文字编辑器(比如说 setenv VISUAL joe)
- -r : 删除目前的时程表
- -l : 列出目前的时程表



时间格式如下：

```
f1 f2 f3 f4 f5 program
```

- 其中 f1 是表示分钟，f2 表示小时，f3 表示一个月份中的第几日，f4 表示月份，f5 表示一个星期中的第几天。program 表示要执行的程序。
- 当 f1 为 * 时表示每分钟都要执行 program，f2 为 * 时表示每小时都要执行程序，其馀类推
- 当 f1 为 a-b 时表示从第 a 分钟到第 b 分钟这段时间内要执行，f2 为 a-b 时表示从第 a 到第 b 小时都要执行，其馀类推
- 当 f1 为 */n 时表示每 n 分钟个时间间隔执行一次，f2 为 */n 表示每 n 小时个时间间隔执行一次，其馀类推
- 当 f1 为 a, b, c,... 时表示第 a, b, c,... 分钟要执行，f2 为 a, b, c,... 时表示第 a, b, c...个小时要执行，其馀类推

```
*    *    *    *    *
-    -    -    -    -
|    |    |    |    |
|    |    |    |    +----- 星期中星期几 (0 - 6) (星期天 为0)
|    |    |    +---------- 月份 (1 - 12) 
|    |    +--------------- 一个月中的第几天 (1 - 31)
|    +-------------------- 小时 (0 - 23)
+------------------------- 分钟 (0 - 59)
```

使用者也可以将所有的设定先存放在文件中，用 crontab file 的方式来设定执行时间。



## 虚拟机工具

```shell
apt-get install open-vm-tools open-vm-tools-desktop
```



## 内核版本

```shell
uname -r 														// 内核版本
uname -a 														// 内核及系统版本
```



## 平台

```shell
# cpu架构
root@kyv10:~# uname -m
x86_64

# 系统位数
[root@localhost ~]# uname -i
x86_64

# 通过long的长度获取倍数
[root@localhost ~]# getconf LONG_BIT
64
```



## 系统信息

```shell
# redhat 系查看版本
cat /etc/redhat-release 										

# centos 系
cat /etc/centos-release

# Ubuntu系
root@superman-PC:~# lsb_release -a
No LSB modules are available.
Distributor ID: Uos
Description:    UnionTech OS Desktop 20 Home
Release:        20 Home
Codename:       n/a

# 银河麒麟
cat /etc/os-release



root@superman-PC:~# cat /proc/version
Linux version 5.4.50-amd64-desktop (deepin@deepin-PC) (gcc version 8.3.0 (Uos 8.3.0.3-3+rebuild)) #3000 SMP Fri Dec 18 18:10:42 CST 2020

# 查看cpu相关信息
cat /proc/cpuinfo
```



## swap 交换分区 虚拟内存

```shell
dd if=/dev/zero of=/data/swap bs=512 count=8388616		//将zero设备作为输出，写入swap，count写入多少次，这里一共写入4G，如果没有swap会自动创建
mkswap /data/swap	//设置swap为交换区
swapon /data/swap		//启用
echo "/data/swap swap swap defaults    0  0" >> /etc/fstab	//开机引导时会自动启动
```

查看是否生效:

```shell
free -m		//可以看到swap分区
```

关闭：

```shell
swapoff /data/swap
swapoff -a >/dev/null
rm -rf /data/swap
```



## 查看glibc版本

```shell
root@kyv10:~# strings /lib/x86_64-linux-gnu/libc.so.6 | grep GLIBC
GLIBC_2.2.5
GLIBC_2.2.6
...
GLIBC_2.23
GLIBC_PRIVATE

root@kyv10:~# strings /usr/lib/gcc/x86_64-linux-gnu/5/libstdc++.so | grep GLIBCXX
GLIBCXX_3.4
GLIBCXX_3.4.1
...
GLIBCXX_3.4.21
GLIBCXX_DEBUG_MESSAGE_LENGTH
```

怎样查看`libc.so`和`libstdc++.so`库的位置：

```shell
# 可以找到库所在位置
gcc -v
g++ -v
```



**CentOS 5.6位置**

```shell
[root@localhost ~]# strings /lib64/libc.so.6 | grep GLIBC
GLIBC_2.2.5
GLIBC_2.2.6
GLIBC_2.3
GLIBC_2.3.2
GLIBC_2.3.3
GLIBC_2.3.4
GLIBC_2.4
GLIBC_2.5
GLIBC_PRIVATE
```





## 自启动

### 查看有哪些开机自启服务

1. CentOS6

```shell
[root@kernel-dev yum.repos.d]# chkconfig --list
auditd          0:off   1:off   2:on    3:on    4:on    5:on    6:off
blk-availability        0:off   1:on    2:on    3:on    4:on    5:on    6:off
crond           0:off   1:off   2:on    3:on    4:on    5:on    6:off
ip6tables       0:off   1:off   2:on    3:on    4:on    5:on    6:off
iptables        0:off   1:off   2:on    3:on    4:on    5:on    6:off
kdump           0:off   1:off   2:off   3:on    4:on    5:on    6:off
lvm2-monitor    0:off   1:on    2:on    3:on    4:on    5:on    6:off
mdmonitor       0:off   1:off   2:on    3:on    4:on    5:on    6:off
netconsole      0:off   1:off   2:off   3:off   4:off   5:off   6:off
netfs           0:off   1:off   2:off   3:on    4:on    5:on    6:off
network         0:off   1:off   2:on    3:on    4:on    5:on    6:off
nfs-rdma        0:off   1:off   2:off   3:off   4:off   5:off   6:off
postfix         0:off   1:off   2:on    3:on    4:on    5:on    6:off
rdisc           0:off   1:off   2:off   3:off   4:off   5:off   6:off
rdma            0:off   1:on    2:on    3:on    4:on    5:on    6:off
restorecond     0:off   1:off   2:off   3:off   4:off   5:off   6:off
rsyslog         0:off   1:off   2:on    3:on    4:on    5:on    6:off
saslauthd       0:off   1:off   2:off   3:off   4:off   5:off   6:off
sshd            0:off   1:off   2:on    3:on    4:on    5:on    6:off
udev-post       0:off   1:on    2:on    3:on    4:on    5:on    6:off
```



### 服务自启

https://www.jianshu.com/p/c999eefbc197

https://www.huaweicloud.com/articles/d57fcb08c079cace334188a8aad2b8e9.html

### 脚本自启

以`/mnt/shell/autostart.sh`为例：

```shell
#!/bin/bash
#description:开机自启脚本
#启动nginx
```

- 方法1

  1. 赋予脚本可执行权限（/mnt/shell/autostart.sh是你的脚本路径）

  ```shell
  chmod +x /mnt/shell/autostart.sh 
  ```

  2. 打开/etc/rc.d/rc/local文件，在末尾增加如下内容

  ```shell
  /mnt/shell/autostart.sh
  ```

  3. 在centos7中，/etc/rc.d/rc.local的权限被降低了，所以需要执行如下命令赋予其可执行权限

  ```shell
  chmod +x /etc/rc.d/rc.local
  ```

- 方法2

  1. 将脚本移动到/etc/rc.d/init.d目录下

  ```shell
  mv  /mnt/shell/autostart.sh /etc/rc.d/init.d
  ```

  2. 增加脚本的可执行权限

  ```shell
  chmod +x  /etc/rc.d/init.d/autostart.sh
  ```

  3. 添加脚本到开机自动启动项目中

  ```shell
  cd /etc/rc.d/init.d
  chkconfig --add autostart.sh
  chkconfig autostart.sh on
  ```



## 驱动自启

例：

1. 将驱动`ko`文件拷贝到 目录：`/lib/modules/5.4.50-amd64-desktop/kernel/drivers/input/keyboard`
2. 执行命令 "`depmod -a`" 更新索引
3. 编辑文件 `/etc/modules-load.d/modules.conf` 添加 驱动名(**不加后缀**，如`LinuxKbd`),**如没有modules.conf文件，自己创建任意名称文件也可**



## 程序的多版本管理

以`gcc`和`g++`为例：

step1.安装多版本

```shell
sudo apt-get install gcc-5 gcc-5-multilib
sudo apt-get install g++-5 g++-5-multilib
sudo apt-get install gcc-6 gcc-6-multilib
sudo apt-get install g++-6 g++-6-multilib
sudo apt-get install gcc-7 gcc-7-multilib
sudo apt-get install g++-7 g++-7-multilib
sudo update-alternatives --install /usr/bin/gcc gcc /usr/bin/gcc-5 50
sudo update-alternatives --install /usr/bin/gcc gcc /usr/bin/gcc-6 60
sudo update-alternatives --install /usr/bin/gcc gcc /usr/bin/gcc-7 70
sudo update-alternatives --install /usr/bin/g++ g++ /usr/bin/g++-5 50
sudo update-alternatives --install /usr/bin/g++ g++ /usr/bin/g++-6 60
sudo update-alternatives --install /usr/bin/g++ g++ /usr/bin/g++-7 70
```

step2.然后选择gcc和g++版本

```shell
sudo update-alternatives --config gcc
sudo update-alternatives --config g++
```



## 更新源

常用软件源地址：

清华源： https://mirrors.tuna.tsinghua.edu.cn/

中科大源： http://mirrors.ustc.edu.cn/



CentOS系的可以再加上epel源

```shell
yum -y install epel-release
```



### CentOS5.6

```shell
# CentOS-Base.repo
#
# The mirror system uses the connecting IP address of the client and the
# update status of each mirror to pick mirrors that are updated to and
# geographically close to the client.  You should use this for CentOS updates
# unless you are manually picking other mirrors.
#
# If the mirrorlist= does not work for you, as a fall back you can try the 
# remarked out baseurl= line instead.
#
#

[base]
name=CentOS-$releasever - Base
#mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=os
baseurl=https://mirrors.tuna.tsinghua.edu.cn/centos-vault/5.6/os/$basearch/
gpgcheck=1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-5

#released updates 
[updates]
name=CentOS-$releasever - Updates
#mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=updates
baseurl=https://mirrors.tuna.tsinghua.edu.cn/centos-vault/5.6/updates/$basearch/
gpgcheck=1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-5

#additional packages that may be useful
[extras]
name=CentOS-$releasever - Extras
#mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=extras
baseurl=https://mirrors.tuna.tsinghua.edu.cn/centos-vault/5.6/extras/$basearch/
gpgcheck=1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-5

#additional packages that extend functionality of existing packages
[centosplus]
name=CentOS-$releasever - Plus
#mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=centosplus
baseurl=https://mirrors.tuna.tsinghua.edu.cn/centos-vault/5.6/centosplus/$basearch/
gpgcheck=1
enabled=0
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-5

#contrib - packages by Centos Users
[contrib]
name=CentOS-$releasever - Contrib
#mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=contrib
baseurl=https://mirrors.tuna.tsinghua.edu.cn/centos-vault/5.6/contrib/$basearch/
gpgcheck=1
enabled=0
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-5
```



### CentOS6.10

该文件夹提供较早版本的 CentOS，例如 CentOS 6；同时提供当前 CentOS 大版本的历史小版本的归档； 还提供 CentOS 各个版本的源代码和调试符号。

建议先备份 `/etc/yum.repos.d/` 内的文件。

需要确定您所需要的小版本，如无特殊需要则使用该大版本的最后一个小版本，比如 6.10，5.11，我们将其标记为 `$minorver`，需要您在之后的命令中替换。

然后编辑 `/etc/yum.repos.d/` 中的相应文件，在 `mirrorlist=` 开头行前面加 `#` 注释掉；并将 `baseurl=` 开头行取消注释（如果被注释的话），把该行内的域名及路径（例如`mirror.centos.org/centos/$releasever`）替换为 `mirrors.tuna.tsinghua.edu.cn/centos-vault/$minorver`。

以上步骤可以被下方的命令完成

```shell
minorver=6.10
sudo sed -e "s|^mirrorlist=|#mirrorlist=|g" \
         -e "s|^#baseurl=http://mirror.centos.org/centos/\$releasever|baseurl=https://mirrors.tuna.tsinghua.edu.cn/centos-vault/$minorver|g" \
         -i.bak \
         /etc/yum.repos.d/CentOS-*.repo
```

注意其中的`*`通配符，如果只需要替换一些文件中的源，请自行增删。

注意，如果需要启用其中一些 repo，需要将其中的 `enabled=0` 改为 `enabled=1`。

最后，更新软件包缓存

```shell
sudo yum makecache
```



### CentOS7/8

该文件夹只提供 CentOS 7 与 8，架构仅为 `x86_64` ，如果需要较早版本的 CentOS，请参考 centos-vault 的帮助，若需要其他架构，请参考 centos-altarch 的帮助。

建议先备份 `/etc/yum.repos.d/` 内的文件（CentOS 7 及之前为 `CentOS-Base.repo`，CentOS 8 为`CentOS-Linux-*.repo`）

然后编辑 `/etc/yum.repos.d/` 中的相应文件，在 `mirrorlist=` 开头行前面加 `#` 注释掉；并将 `baseurl=` 开头行取消注释（如果被注释的话），把该行内的域名（例如`mirror.centos.org`）替换为 `mirrors.tuna.tsinghua.edu.cn`。

以上步骤可以被下方的命令一步完成

```shell
sudo sed -e 's|^mirrorlist=|#mirrorlist=|g' \
         -e 's|^#baseurl=http://mirror.centos.org|baseurl=https://mirrors.tuna.tsinghua.edu.cn|g' \
         -i.bak \
         /etc/yum.repos.d/CentOS-*.repo
```

注意其中的`*`通配符，如果只需要替换一些文件中的源，请自行增删。

注意，如果需要启用其中一些 repo，需要将其中的 `enabled=0` 改为 `enabled=1`。

最后，更新软件包缓存

```shell
sudo yum makecache
```



## 安全

### pam







# 权限

## chmod

用来修改文件的`mode bits`，权限

修改时可以使用符号，也可以使用8进制数

```shell
chmod [OPTION]... MODE[,MODE]... FILE...
chmod [OPTION]... OCTAL-MODE FILE...
chmod [OPTION]... --reference=RFILE FILE...
```



更通俗易懂的语法形式：

```shell
chmod [who] [+ | - | =] [mode] 文件名
```



命令中各选项的含义为：

操作对象who可是下述字母中的任一个或者它们的组合：

- `u` 表示“用户（user）”，即文件或目录的所有者。
- `g` 表示“同组（group）用户”，即与文件属主有相同组ID的所有用户。
- `o` 表示“其他（others）用户”。
- `a` 表示“所有（all）用户”。它是系统默认值。



操作符号可以是：

- `+`添加某个权限。
- `-` 取消某个权限。
- `=` 赋予给定权限并取消其他所有权限（如果有的话）。



设置 mode 所表示的权限可用下述字母的任意组合：

- `r` 可读。
- `w` 可写。
- `x` 可执行。
- `X` 只有目标文件对某些用户是可执行的或该目标文件是目录时才追加x 属性。
- `s` 在文件执行时把进程的属主或组ID置为该文件的文件属主。方式“u＋s”设置文件的用户ID位，“g＋s”设置组ID位。
- `t` 保存程序的文本到交换设备上。
- `u` 与文件属主拥有一样的权限。
- `g` 与和文件属主同组的用户拥有一样的权限。
- `o` 与其他用户拥有一样的权限。



> `s`:set user or group ID on execution
>
> UNIX访问权限标志setuid和setGID（“设置用户ID”和“SET组ID”）。允许用户分别使用可执行文件所有者或组的文件系统权限运行可执行文件，并在目录中更改行为 。 它们通常用于允许计算机系统上的用户运行具有临时提升权限的程序，以便执行特定任务。
>
> [setuid - Wikipedia](https://en.wikipedia.org/wiki/Setuid)



文件名：以空格分开的要改变权限的文件列表，支持通配符。　



在一个命令行中可给出多个权限方式，其间用逗号隔开。例如：

chmod g+r，o+r example  % 使同组和其他用户对文件example 有读权限。



## adduser 添加用户

#### adduser

> 在使用adduser命令时,它会添加这个用户名,并创建和用户名名称相同的组名,并把这个用户名添加到自己的组里去,并在/home目录想创建和用户名同名的目录,并拷贝/etc/skel目录下的内容到/home/用户名/的目录下,并提示输入密码,并提示填写相关这个用户名的信息。
>
> 用adduser这个命令创建的账号是普通账号,可以用来登陆系统.



```shell
adduser testuser

# 删除创建用户
# -r参数删除用户testuser的同时，将其宿主目录和系统内与其相关的内容删除。
userdel -r testuser
```



### useradd

> 命令说明: 在使用命令useradd时，它会添加这个用户名，并创建和用户名相同的组名，但它并不在/home目录下创建基于用户名的目录,也不提示创建新的密码。也就是说使用useradd mongo 创建出来的用户,将是默认的"三无"用户,无家目录,无密码,无系统shell,换句话说,它创建的是系统用户,无法用它来登陆系统.

```shell
root@silkPC:/home# useradd mongotest
```

### 如何用useradd创建的用户登陆系统呢?

1. 首先为新用户设置密码

```shell
root@silkPC:/home# passwd mongotest

Enter new UNIX password: 

Retype new UNIX password: 

passwd: password updated successfully
```

2. 为新用户创建家目录/home/mongotest

```shell
root@silkPC:/home# mkdir mongotest
```

3. 更改家目录归属

```shell
root@silkPC:/home# chown -R mongotest:mongotest /home/mongotest/

root@silkPC:/home# ll

total 16

drwxr-xr-x  4 root      root      4096  6月 28 15:51 ./

drwxr-xr-x 24 root      root      4096  6月 28 10:16 ../

drwxr-xr-x 46 denson    denson    4096  6月 28 15:18 denson/

drwxr-xr-x  2 mongotest mongotest 4096  6月 28 15:51 mongotest/
```

4. 修改mongotest的登陆Shell为/bin/bash

```shell
root@silkPC:/home# usermod -s /bin/bash mongotest

root@silkPC:/home# tail -1 /etc/passwd  //查看命令

mongotest:x:1001:1001::/home/mongotest:/bin/bash
```

5. 进入home目录,再将/etc/skel目录下的文件复制到该用户目录下,就大功告成了.

```shell
root@silkPC:/home# cp -r /etc/skel /mongotest
```

上述方法,是用useradd mongotest 没有指定任何参数的情况下的修改.如果在用useradd创建用户时加上参数,例如:"-d"--指定家目录,"-s"--指定shell,即可以让该用户正常登陆系统.

例如:

```shell
root@silkPC:/home# useradd -d /home/mongotest -m -s /bin/bash mongotest
```

删除用户

```shell
root@silkPC:/home# userdel mongotest  //-r参数表示删除家目录
```

#### 参数说明:

-c 备注 加上备注。并会将此备注文字加在/etc/passwd中的第5项字段中;

-d 用户主文件夹。指定用户登录所进入的目录，并赋予用户对该目录的的完全控制权，一般与 "-m" 一起使用,让用户自定义家目录路径;

-e 有效期限。指定帐号的有效期限。格式为YYYY-MM-DD，将存储在/etc/shadow;

-f 缓冲天数。限定密码过期后多少天，将该用户帐号停用;

-g 主要组。设置用户所属的主要组;

-G 次要组。设置用户所属的次要组，可设置多组;

-M 强制不创建用户主文件夹;

-m 强制建立用户主文件夹，并将/etc/skel/当中的文件复制到用户的根目录下;

-p 密码。输入该帐号的密码;

-s shell。用户登录所使用的shell;

-u uid。指定帐号的标志符user id，简称uid;



## userdel 删除用户

```shell
# 删除用户user2
userdel user2

# 删除用户user3，同时删除他的工作目录
userdel -r user3
```



## 用户组

### groupadd 创建用户组

```shell
# 创建一个users组，其gid为888
groupadd -g 888 users
```



### gpasswd 为组添加用户

**只有root和组管理员能够改变组成员**

```shell
# 把user1加入到users组
gpasswd -a user1 users

# 把user1退出users组
gpasswd -d user1 users
```



### groupmod 修改组

```shell
# 修改组名users为usersgroup
groupmod -n users usersgroup
```



### groupdel 删除组

```shell
# 删除组users
groupdel users
```



### chown

Linux chown（英文全拼：**change owner**）命令用于设置文件所有者和文件关联组的命令。

Linux/Unix 是多人多工操作系统，所有的文件皆有拥有者。利用 chown 将指定文件的拥有者改为指定的用户或组，用户可以是用户名或者用户 ID，组可以是组名或者组 ID，文件是以空格分开的要改变权限的文件列表，支持通配符。 。

chown 需要超级用户 **root** 的权限才能执行此命令。

**使用权限** : root

```shell
chown [-cfhvR] [--help] [--version] user[:group] file...
```

**参数** :

- user : 新的文件拥有者的使用者 ID
- group : 新的文件拥有者的使用者组(group)
- -c : 显示更改的部分的信息
- -f : 忽略错误信息
- -h :修复符号链接
- -v : 显示详细的处理信息
- -R : 处理指定目录以及其子目录下的所有文件
- --help : 显示辅助说明
- --version : 显示版本

#### 实例

把 /var/run/httpd.pid 的所有者设置 root：

```shell
chown root /var/run/httpd.pid
```

将文件 file1.txt 的拥有者设为 runoob，群体的使用者 runoobgroup :

```shell
chown runoob:runoobgroup file1.txt
```

将当前前目录下的所有文件与子目录的拥有者皆设为 runoob，群体的使用者 runoobgroup:

```shell
chown -R runoob:runoobgroup *
```

把 /home/runoob 的关联组设置为 512 （关联组ID），不改变所有者：

```shell
chown :512 /home/runoob
```



# 解压缩

## *.rpm

```shell
# 在debian系中解压rpm
apt install rpm2cpio
rpm2cpio xxx.rpm | cpio -div
```



## *.zip
```shell
# 压缩：
# -q 不显示指令执行过程
# -r 递归处理，将指定目录下的所有文件和子目录一并处理。
zip -q -r xxx.zip ./*

# 解压 -d 解压到指定目录：
unzip -d ./tempdir xxx.zip
```

## *.tar.gz
```shell
#压缩：
tar -zcvf xxx.tar.gz ./*

#解压 -C 指定解压目录：
tar -C /usr/local -zxvf xxx.tar.gz		
```

## *.tar.bz2
```shell
# 解压：
tar -jxvf ×××.tar.bz2
```

## *.tar.xz
```shell
#解压：
tar xvJf ×××.tar.xz
```

## *.tgz
```shell
#解压：
tar -zxvf xxx.tgz
```

# 文件

## mount

```shell
man mount

NAME
       mount - mount a filesystem

SYNOPSIS
       mount [-h|-V]

       mount [-l] [-t fstype]

       mount -a [-fFnrsvw] [-t fstype] [-O optlist]

       mount [-fnrsvw] [-o options] device|mountpoint

       mount [-fnrsvw] [-t fstype] [-o options] device mountpoint

       mount --bind|--rbind|--move olddir newdir

       mount      --make-{shared|slave|private|unbindable|rshared|rslave|rpri‐
       vate|runbindable} mountpoint
```

参数：

- -t :指定文件系统类型，通常不必指定。mount会自动选择正确的类型
- -o remount :将一个已经挂载的文件系统用不同的方式重新挂载
- -o ro :用只读模式挂载
- -o rw :用可读写模式挂载
- -o loop :使用loop模式用来将一个img当成硬盘挂载在系统上

例：

```shell
mount -t ext4 /dev/sda /

mount -o ro /dev/hda1 /mnt

mount -o loop /tmp/image.iso /mnt/cdrom
```



卸载：

```shell
umount /mnt/cdrom
```



### 重新挂载

```shell
mount -o rw,remount /
```



### 文件系统变只读

1. 首先可以尝试以`rw`模式重新挂载
2. 如果失败的话，可以尝试使用`fsck`命令对设备进行修复

例：

```shell
# root @ OpenWrt in ~ [14:40:52] C:16
$ fsck.ext4 /dev/mmcblk0p2 
e2fsck 1.45.6 (20-Mar-2020)
rootfs contains a file system with errors, check forced.
Pass 1: Checking inodes, blocks, and sizes
Inode 7, i_size is 264302592, should be 268521472.  Fix<y>? yes
Pass 2: Checking directory structure

Pass 3: Checking directory connectivity
Pass 4: Checking reference counts
Pass 5: Checking group summary information
Block bitmap differences:  -1837056 +(1838593--1838594)
Fix<y>? yes
Free blocks count wrong for group #4 (9776, counted=9775).
Fix<y>? yes
Free blocks count wrong for group #56 (32260, counted=32259).
Fix<y>? yes
Free blocks count wrong (3321770, counted=3331543).
Fix<y>? yes
Inode bitmap differences:  +17934
Fix<y>? yes
Free inodes count wrong for group #2 (2, counted=1).
Fix<y>? yes
Free inodes count wrong (821366, counted=821365).
Fix<y>? yes

rootfs: ***** FILE SYSTEM WAS MODIFIED *****
rootfs: ***** REBOOT SYSTEM *****
rootfs: 23435/844800 files (0.0% non-contiguous), 252457/3584000 blocks
```





## tree命令

```shell
# 列出当前目录结构
tree
```

常用参数：

- -a 显示所有文件和目录
- -d 显示目录名称而非内容
- -L level 限制目录显示层级。

例：

```shell
[root@VM-4-16-centos www.xxx.com]# tree -L 1
.
|-- CHANGELOG.md
|-- composer.json
|-- composer.lock
|-- config.php
|-- extend.php
|-- flarum
|-- LICENSE
|-- public
|-- README.md
|-- site.php
|-- storage
`-- vendor

3 directories, 9 files
```





## sshfs

```shell
# 在开发机上安装sshfs包
yum install sshfs

# 挂载
sshfs root@192.168.1.1:/root/test/ test

# 卸载
fusermount -u test
```



## Linux共享文件夹

```shell
# 安装
yum install samba

# 查看状态
[root@fstest infosecfs]# service smb status
smbd is stopped

# 配置文件目录
/etc/samba/smb.conf 

# 假如Linux下想共享的目录为：/home/chm/Desktop/infosecfs

sudo gedit /etc/samba/smb.conf 编辑smb.conf 文件

在配置文件末尾添加

[ShareFiles]
comment = Shared Folder require password
path = /home/magic/share
public = yes

writable = yes
valid users = magic
create mask = 0777
directory mask = 0777
force user = nobody
force group = nogroup
available = yes
browseable = yes


保存后重新启动samba，sudo /etc/init.d/samba restart
```



## 远程拷贝 scp 命令

```shell
# 从远程拷贝到本地
# 有文件夹需要 -r 参数
scp -r root@192.168.231.163:/root/go/src/github.com/DWBC-ConPeer/crypto-gm/* ./

# 从本地拷贝到远程
scp -r ./* root@192.168.231.163:/root/go/src/github.com/DWBC-ConPeer/crypto-gm/
```



## 查看文件

```shell
tail -f -n 100 <文件名>
```

## 查找文件
```shell
find <指定目录> <指定条件> <指定动作>
	- <指定目录>： 所要搜索的目录及其所有子目录。默认为当前目录。
　- <指定条件>： 所要搜索的文件的特征。
　- <指定动作>： 对搜索结果进行特定的处理。
find /usr/include -name "ioctl.h" -ls
```

## 查看包含指定内容的文件
```shell
grep -r "被查找的字符串" /dir
```

## 链接

- 软链接

```shell
ln -s TARGET_PATH LINK_NAME
```

- 硬链接

```shell
```





## 磁盘占用

```shell
df -h
Filesystem      Size  Used Avail Use% Mounted on
tmpfs           793M  1.9M  792M   1% /run
/dev/sda3       118G   14G   98G  13% /
tmpfs           3.9G     0  3.9G   0% /dev/shm
tmpfs           5.0M  4.0K  5.0M   1% /run/lock
tmpfs           4.0M     0  4.0M   0% /sys/fs/cgroup
/dev/sda2       512M  7.9M  505M   2% /boot/efi
tmpfs           793M  120K  793M   1% /run/user/1000
tmpfs           793M   72K  793M   1% /run/user/0
```

- 查看inode使用量

  ```shell
  df -i
  ```

- 目录占用大小

  ```shell
  du -d 1 -h
  	-d 表示深度
  
  # 快速查看文件夹大小
  du -d 0 -h ./
  ```



## 查看分区类型

```shell
df -T
$ df -T
Filesystem           Type       1K-blocks      Used Available Use% Mounted on
/dev/root            ext4          967576    796380    154812  84% /
tmpfs                tmpfs         470280     18664    451616   4% /tmp
/dev/mmcblk0p1       vfat           65390     18054     47336  28% /boot
tmpfs                tmpfs            512         0       512   0% /dev
/dev/root            ext4          967576    796380    154812  84% /opt/docker
```



# 进程

## 查看系统中所有的进程

```shell
ps aux
```



|USER|PID|%CPU|%MEM|VSZ|RSS|TTY|STAT|START|TIME|COMMAND|
|----|---|----|----|---|---|---|----|-----|----|-------|
|由哪个用户产生	|进程ID		|进程占用CPU资源|进程占用内存资源 |占用虚拟内存大小(KB)| 占用实际物理内存(KB)| 在哪个终端运行|	进程状态 |	进程启动时间		|占用CPU运行时间|		产生进程的命令|
|USER        		|PID 			|			%CPU 				|		%MEM  				|	  VSZ  						|	 RSS 								|TTY      			|		STAT 		|START   					|	TIME 					|	COMMAND         |
|root        		|  1 			|			 0.0 				|		 0.0  				|	19232  						|	1508 								|?        			|		Ss   		|06:32   					|	0:01 					|	/sbin/init      |
|root        		|  2 			|			 0.0 				|		 0.0  				|	    0  						|	   0 								|?        			|		S    		|06:32   					|	0:00 					|	[kthreadd]      |

- a：显示一个终端的所有进程，除会话引线外；
- u：显示进程的归属用户及内存的使用情况；
- x：显示没有控制终端的进程；
- -l：长格式显示更加详细的信息；
- -e：显示所有进程；

## 查看系统所有的进程，而且可以看到父进程和进程优先级

```shell
ps -le
```



## 查看当前shell产生的进程

```shell
ps -l
```



## 定时任务

crontab

我们经常使用的是`crontab`命令是`cron table`的简写，它是`cron`的配置文件，也可以叫它作业列表，我们可以在以下文件夹内找到相关配置文件。

- `/var/spool/cron/` 目录下存放的是每个用户包括root的crontab任务，每个任务以创建者的名字命名
- `/etc/crontab` 这个文件负责调度各种管理和维护任务。
- `/etc/cron.d/` 这个目录用来存放任何要执行的crontab文件或脚本。
- 我们还可以把脚本放在`/etc/cron.hourly`、/etc/cron.daily、`/etc/cron.weekly`、`/etc/cron.monthly`目录中，让它每小时/天/星期、月执行一次。



```shell
crontab [-u username]　　　　//省略用户表表示操作当前用户的crontab
    -e      (编辑工作表)
    -l      (列出工作表里的命令)
    -r      (删除工作作)
```

crontab的命令构成为 时间+动作，其时间有**分、时、日、月、周**五种，操作符有

- ***** 取值范围内的所有数字
- **/** 每过多少个数字
- **-** 从X到Z
- **，**散列数字

### 实例

**实例1：每1分钟执行一次myCommand**

```shell
* * * * * myCommand
```

**实例2：每小时的第3和第15分钟执行**

```shell
3,15 * * * * myCommand
```

**实例3：在上午8点到11点的第3和第15分钟执行**

```shell
3,15 8-11 * * * myCommand
```

**实例4：每隔两天的上午8点到11点的第3和第15分钟执行**

```shell
3,15 8-11 */2  *  * myCommand
```

**实例5：每周一上午8点到11点的第3和第15分钟执行**

```shell
3,15 8-11 * * 1 myCommand
```

**实例6：每晚的21:30重启smb**

```shell
30 21 * * * /etc/init.d/smb restart
```

**实例7：每月1、10、22日的4 : 45重启smb**

```shell
45 4 1,10,22 * * /etc/init.d/smb restart
```

**实例8：每周六、周日的1 : 10重启smb**

```shell
10 1 * * 6,0 /etc/init.d/smb restart
```

**实例9：每天18 : 00至23 : 00之间每隔30分钟重启smb**

```shell
0,30 18-23 * * * /etc/init.d/smb restart
```

**实例10：每星期六的晚上11 : 00 pm重启smb**

```shell
0 23 * * 6 /etc/init.d/smb restart
```

**实例11：每一小时重启smb**

```shell
0 */1 * * * /etc/init.d/smb restart
```

**实例12：晚上11点到早上7点之间，每隔一小时重启smb**

```shell
0 23-7/1 * * * /etc/init.d/smb restart
```





# 网络
```shell
ifconfig
```



## CentOS 修改IP地址

修改文件`/etc/sysconfig/network-scripts/ifcfg-eth0`

原内容

```shell
DEVICE="eth0"
BOOTPROTO="dhcp"
HWADDR="00:0C:29:F0:16:E9"
IPV6INIT="yes"
NM_CONTROLLED="yes"
ONBOOT="yes"
TYPE="Ethernet"
UUID="bddae969-3080-4107-be9f-e5b2b37012fc"
```

主要修改`BOOTPROTO`并添加相关ip、网关、子网掩码及DNS

如下：

```shell
DEVICE="eth0"
BOOTPROTO="static"
HWADDR="00:0C:29:F0:16:E9"
IPV6INIT="yes"
NM_CONTROLLED="yes"
ONBOOT="yes"
TYPE="Ethernet"
UUID="bddae969-3080-4107-be9f-e5b2b37012fc"
IPADDR=192.168.231.249
NETMASK=255.255.255.0
GATEWAY=192.168.231.2
DNS1=8.8.8.8
DNS2=8.8.4.4
```

修改后，重启服务：`service network restart`

`/etc/resolv.conf`文件会自动改变为如下：

```shell
# Generated by NetworkManager
nameserver 8.8.8.8
nameserver 8.8.4.4
```



## 设置代理

在`/etc/profile.d`文件夹下创建文件`proxy.sh`

```shell
export proxy="http://10.20.62.74:15678"
export http_proxy=$proxy
export https_proxy=$proxy
export ftp_proxy=$proxy
export no_proxy="localhost, 127.0.0.1, ::1"
```

reboot或 `source /etc/profile.d/proxy.sh`生效

也可在当前shell中直接输入，但这样在关闭shell之后就失效了



## 查看登录用户

```shell
# 显示当前登录系统的用户
who
root   tty7     2014-05-13 12:12 (:0)
root   pts/0    2014-05-14 17:09 (:0.0)
root   pts/1    2014-05-14 18:51 (192.168.1.17)
root   pts/2    2014-05-14 19:48 (192.168.1.17)
```



## SSH命令

```shell
# 在服务器上安装ssh
apt install openssh-server

# 如果ssh一段时间后会自动注销，在/etc/ssh/sshd_config 中加入以下内容
TCPKeepAlive yes
# 每60秒向client发包
ClientAliveInterval 60
# 最多发360次
ClientAliveCountMax 360
```

## SSH超时断开

环境变量 $TMOUT=0 设置不超时

```shell
echo "export TMOUT=0" >> /etc/profile
```





## 防火墙

### CentOS6
```shell
# 查看防火墙状态：
service iptables status

# 关闭防火墙：
service iptables stop

# 打开防火墙：
service iptables start
```

### CentOS7
```shell
# 查看防火墙状态：
firewall-cmd --state

# 停止firewall：
systemctl stop firewalld.service

# 禁止firewall开机启动：
systemctl disable firewalld.service 
```



### iptables 待补

**iptables默认是filter表**

#### iptables命令参数

| 参数 | 含义                                         |
| ---- | -------------------------------------------- |
| -L   | 显示表中的所有规则                           |
| -n   | 不要把端口或ip反向解析为名字                 |
| -t   | 指定表 不指定默认是filter表                  |
| -A   | append 追加 准许 ACCEPT 加入准许类的规则使用 |
| -D   | delete 删除                                  |
| -I   | insert 拒绝的规则放在最上面 拒绝类的规则使用 |



| 参数    | 含义                           |
| ------- | ------------------------------ |
| -p      | protocal 协议 tcp/udp/icmp/all |
| --dport | 目标端口                       |
| --sport | 源端口                         |
| -s      | --source 源ip                  |
| -d      | --destination 目标ip           |
| -m      | 指定模块                       |



#### 清空规则

```shell
# -F flush 清除所有规则，不会处理默认的规则 清除当前表的规则
iptables -F

# 删除用户自定义的链
iptables -X

# 链的计数器清零(数据包计数器与数据包字节计数器)
iptables -Z

```



#### 查看规则

```shell
iptables -nL
```



#### 禁用访问22端口

```shell
# -A 链名 -p 协议 --dport 目标端口 -j 允许/屏蔽
iptables -A INPUT -p tcp --dport 22 -j DROP
```



#### 删除指定规则

```shell
# 在规则前面显示序号
iptables -nL --line-number

# 删除规则 删除INPUT链的第1个规则
iptables -D INPUT 1
```





# 设备



## usb

```shell
lsusb
```





# 配置


## 库查找路径
```shell
echo "libpath" >> /etc/ld.so.conf
# 修改完后
ldconfig
```



## 磁盘扩容
### Ubuntu
```shell
apt-get install gparted
gparted
```

### CentOS
```shell
# 查看分区
fdisk -l

# 增加空间的硬盘是/dev/sda
fdisk /dev/sda
	p														// 查看已分区数量
	   Device Boot      Start         End      Blocks   Id  System
		/dev/sda1   *        2048     2099199     1048576   83  Linux
		/dev/sda2         2099200    41943039    19921920   8e  Linux LVM
	n														// 增加分区
	p
		Select (default p): p
		Partition number (3,4, default 3):
		
	# 默认（起始扇区）	
	回车
	First sector (41943040-83886079, default 41943040):
	Using default value 41943040
	Last sector, +sectors or +size{K,M,G} (41943040-83886079, default 83886079):
	
	#  默认（结束扇区）
	回车
	
	# 修改分区类型
	t
	
	# 修改为LVM（8e就是LVM）
	8e
	
	# 写分区表
	w
	
	# 完成，退出fdisk命令
	q

# 出现了新分区
fdisk -l
	   Device Boot      Start         End      Blocks   Id  System
		/dev/sda1   *        2048     2099199     1048576   83  Linux
		/dev/sda2         2099200    41943039    19921920   8e  Linux LVM
		/dev/sda3        41943040    83886079    20971520   8e  Linux LVM

重启

# 将新分区扩容到系统根目录
	lvm
	pvcreate /dev/sda3
	vgextend centos /dev/sda3
	lvextend -L +19.9G /dev/mapper/centos-root
	# 说明：这里扩容了20G，但是直接写20G会报错，要写比20G小一点点
	pvdisplay
	quit
	xfs_growfs /dev/mapper/centos-root
	df -h
```



# Git

## Generating a new SSH key

```shell
ssh-keygen -t ed25519 -C "your_email@example.com"
```

> 如果系统不支持ed25519，使用以下命令：
>
> ```shell
> ssh-keygen -t rsa -b 4096 -C "your_email@example.com"
> ```



```shell
Generating public/private ed25519 key pair.
Enter file in which to save the key (/root/.ssh/id_ed25519): 
Enter passphrase (empty for no passphrase):
```



## 分支

```shell
# 切换到tag，但会收到一个警告，处于一个detached head 状态
# 因为tag相当于一个快照，不能更改它的代码
git checkout tag_name

# 如果要修改tag代码，需要创建一个分支
git checkout -b branch tag_name

# 切换到分支
git checkout branch

# 查看分支
git branch

# 取回所有分支更新
git fetch
```



## 常见错误

### 1. git@github.com: Permission denied (publickey).

这是由于没有将pubkey加入到github



# Linux Shell



## sed工具





```shell
# shell获取自身pid
$$

# 获取命令的执行结果
var=$(commond)
# 或
var=`commond`

# 获取字符串的长度
str="http://www.fengbohello.xin3e.com/blog/shell-truncating-string"
echo "string : [${str}]"
length=${#str}
echo "length : [${length}]"
```

## 使用 # 和 ## 获取尾部子字符串

### # 最小限度从前面截取word

语法：

```shell
${parameter#word}  
```

示例代码：

```shell
str="http://www.fengbohello.xin3e.com/blog/shell-truncating-string"
echo "string : [${str}]"

#分割符为'/'
substr=${str#*/}
echo "substr : [${substr}]"
```

执行结果：

```shell
string : [http://www.fengbohello.xin3e.com/blog/shell-truncating-string]
substr : [/www.fengbohello.xin3e.com/blog/shell-truncating-string]
```

 

### ## 最大限度从前面截取word

语法：

```shell
${parameter##word}
```

示例代码：

```shell
str="http://www.fengbohello.xin3e.com/blog/shell-truncating-string"
echo "string : [${str}]"

#分割符为'/'
substr=${str##*/}
echo "substr : [${substr}]"
```

执行结果：

```shell
string : [http://www.fengbohello.xin3e.com/blog/shell-truncating-string]
substr : [shell-truncating-string]
```

 



# 帮助

## manpages使用：
1. 代表可以执行的命令

2. 系统可调用的函数

3. 库函数

4. 设备文件说明

5. 配置文件或某些文件的格式

6. 游戏文件

7. 附件加一些变量

8. 系统管理员用的命令

   

   示例：

```shell
[root@localhost linux-3.10.0-693.el7]# man -f open
open (3pm)           - perl pragma to set default PerlIO layers for input and output
[root@localhost linux-3.10.0-693.el7]# man -f cp
cp (1)               - copy files and directories
```



# 常见错误

## 1. 解决apt-get /var/lib/dpkg/lock-frontend 问题

运行 apt-get 时报如下错

```shell
E: Could not get lock /var/lib/dpkg/lock-frontend - open (11: Resource temporarily unavailable)
E: Unable to acquire the dpkg frontend lock (/var/lib/dpkg/lock-frontend), is another process using it?
```

解决方案:

```shell
sudo rm /var/lib/dpkg/lock-frontend
sudo rm /var/lib/dpkg/lock
sudo rm /var/cache/apt/archives/lock
```

## 2. xxx is not in the sudoers file.

在CentOS安装后，想要修改root密码：

```shell
uperman@localhost Desktop]$ sudo passwd root
[sudo] password for superman: 
superman is not in the sudoers file.  This incident will be reported.
```

解决方案：

1. 进入超级用户模式，`su -`
2. 添加文件写权限，`chmod u+w /etc/sudoers`
3. 编辑`/etc/sudoers`文件，找到`root ALL=(ALL) ALL`，在下面一行加入`xxx ALL=(ALL) ALL`
4. 取消文件写权限，`chmod u-w /etc/sudoers`



## 3. Command 'ifconfig' not found

```shell
apt install net-tools
```



## 4. warning: group mockbuild does not exist - using root

```shell
useradd mockbuild
```



## 5.Linux切换su很慢

```shell
```





# 折叠测试

<details>
    <summary>1111111111111111</summary>
    222222222222222222222
</details>


同步测试
