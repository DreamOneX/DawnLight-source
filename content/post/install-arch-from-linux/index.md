---
title: "利用Bootstrap从已有的Linux发行版上安装ArchLinux"
date: 2022-07-21T13:55:43+08:00
slug: "install-arch-from-linux"
image: "archlinux.png"
categories:
  - code

tags:
  - linux
  - arch

description: "朋友送了一台vps，第一个想法当然是换个Arch"
draft: false

---   
# 准备   
## 备份  
* 网络相关信息（如子网，IP地址，网卡名，网关）  
* 主机名（hostname `/etc/hosts`）   
* DNS服务器（`/etc/resolv.conf`）   
* SSH 密钥  
* Grub配置  
* 硬件信息（`/etc/modules.conf`）  

**推荐备份完整的`/etc`**  
## 思考是否应该手动安装  
**自动化不一定是脚本小子，只是为了避免不必要的麻烦**  
* [vps2arch](https://gitlab.com/drizzt/vps2arch) 可以全程自动化操作，~~虽然年久失修~~  

另外，如需`lvm`你可以直接按上面的脚本或者找别的教程了，~~原因竟是我没弄过~~  
# 正式开始  
## 下载Bootstrap
（按实际情况修改时间及镜像，可参考[Archlinux Downloads](https://archlinux.org/download/)）  
```bash
cd /tmp
wget https://geo.mirror.pkgbuild.com/iso/xxxx.xx.xx/archlinux-bootstrap-x86_64.tar.gz
```   
## 校验   
```bash
wget https://geo.mirror.pkgbuild.com/iso/xxxx.xx.xx/sha256sums.txt
cat ...
sha256sum
```  

## 释放bootstrap  
```bash
tar xzf archlinux-bootstrap-*-x86_64.tar.gz --numeric-owner
```  
**IMPORTANT**：编辑`/tmp/root.x86_64/etc/pacman.d/mirrorlist`选择镜像源   

## 准备`resolv.conf`
```bash
rm /tmp/root.x86_64/etc/resolv.conf
cp /etc/resolv.conf /tmp/root.x86_64/etc/
```   

## 进入`chroot`  
```bash
mount --bind /boot/efi "/root.x86_64/mnt/boot/efi" # 挂载efi
/tmp/root.x86_64/bin/arch-chroot /tmp/root.x86_64/
```   
1. 若提示`FATAL: kernel too old`应更新系统内核   
2. 若不支持`--fork`、`--pid`请参考[ArchLinux Wiki](https://wiki.archlinux.org/title/Install_Arch_Linux_from_existing_Linux)，或尝试更新系统   

## 使用`chroot`环境   
### pacman-key 初始化  
```bash  
pacman-key --init
pacman-key --populate archlinux
```
### 安装软件包  
```bash
pacman -Syu
pacstrap /mnt base linux linux-firmware
pacman -S nano vim man-pages mandb texinfo grub openssh
```
### 生成fstab
```bash
genfstab /mnt >> /etc/fstab
```   
### 本地化
* `/etc/locale.gen`
```
en_US.UTF-8
zh_CN.UTF-8
```  
* `/etc/locale.conf`
```toml
LANG=zh_CN.UTF-8
LANGUAGE=zh_CN.UTF-8:en_US.UTF-8
LC_CTYPE=en_US.UTF-8
LC_NUMERIC=zh_CN.UTF-8
LC_TIME=zh_CN.UTF-8
LC_COLLATE=en_US.UTF-8
LC_MONETARY=zh_CN.UTF-8
LC_MESSAGES=zh_CN.UTF-8
LC_PAPER=zh_CN.UTF-8
LC_NAME=zh_CN.UTF-8
LC_ADDRESS=zh_CN.UTF-8
LC_TELEPHONE=zh_CN.UTF-8
LC_MEASUREMENT=zh_CN.UTF-8
LC_IDENTIFICATION=en_US.UTF-8
LC_ALL=
```  

### 时区  
```bash
ln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime # UTC +8
hwclock --systohc
```   
### 设置密码
```bash
passwd
```   
-----
在执行`lsblk`确认无误后，挂载磁盘  
```bash
mount /dev/sda /mnt
rm -r ...
```  
## **IMPORTANT**  
**/dev**, **/sys**, **/run**, **/tmp**, **/proc** **不应删除**  
~~`rm -rf /*`~~  

## 开启网络、ssh  
* 编辑`/etc/systemd/network/default.network`  
```service
[Match]
Name=eth0 #改为你的网卡名

[Network]
Gateway=114.145.114.145 #你的网关
Address=114.145.114.137/24 #你的子网
```
* 使用systemd启用服务   
```bash
systemctl enable systemd-networkd
systemctl enable sshd
```  

## 准备引导  
```bash
grub-install --target=x86_64-efi --efi-directory=/boot/efi
grub-mkconfig -o /boot/grub/grub.cfg
```
**IMPORTANT**：在不支持efi的情况下  
```bash
grub-install --target=i386-pc --recheck --force /dev/sda
```  

## Done !
现在，重启系统，就能看到`Arch Linux`的字样了   
~~啥？咋重启，好问题~~
### 怎么重启
* 先尝试`reboot`，`init 6`，`shutdown -r`等，如果无效（~~有效才有鬼了~~）再进行下面的步骤  
* 确保所有数据写入硬盘   
```bash
sync
```  
1. 利用内核参数
* 激活`Magic System Request Key`  
```bash
echo 1 > /proc/sys/kernel/sysrq  
```   
* 重启
```bash
echo "b" > /proc/sysrq-trigger
```  
2. 暴力重启  
~~包括但不限于，断电，强行关机，服务器控制面板等~~  

# 参考资料
1. [Arch Linux Wiki Install Arch Linux from existing Linux](https://wiki.archlinux.org/title/Install_Arch_Linux_from_existing_Linux)   
中文页面貌似已过期   
2. [Arch Linux Wiki Installation Guide](https://wiki.archlinux.org/title/Installation_guide_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87))   
