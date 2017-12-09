---
layout: post
title:  "安装 Linux 重启进入 grub rescue 怎么办？"
date:   2017-12-09 20:27:00 +0800
categories: Linux
---

安装完Linux系统，开机进入 grub rescue 界面怎么办？

修复引导步骤：
{% highlight bash %}
# 列出分区
grub rescue> ls
(hd0) (hd0,msdos8) (hd0,msdos7) (hd0,msdos6) (hd0,msdos1)
# 尝试所有分区，直到找到 /boot 所在分区
grub rescue> ls (hd0,msdos7)/
. .. efi/ grub/ lost+found/ abi-4.10.0-42-generic memtest86+.bin config-4.10.0-42-generic memtest86+.elf  memtest86+_multiboot.bin System.map-4.10.0-42-generic initrd.img-4.10.0-42-generic vmlinuz-4.10.0-42-generic vmlinuz-4.10.0-42-generic.efi.signed
# 设置关联
grub rescue> set root=(hd0,msdos7)
grub rescue> set prefix=(hd0,msdos7)/grub    # 若 /boot 没有进行单独分区，应为 prefix=(hd0,msdos7)/boot/grub
# 启动
grub rescue> insmod normal
grub rescue> normal

# 开机后重新生成grub
$ sudo update-grub
$ sudo grub-install /dev/sda

{% endhighlight %}
