---
layout:     post
title:      "如何将 Linux 的 /home 目录挂载到空闲分区下"
subtitle:   "Linux重新挂载/home"
date:       2017-12-09 19:41:00 +0800
author:     "kekliu"
header-img: "img/post-bg-unix-linux.jpg"
catalog:    true
tags:
    - Linux
---

若安装Linux系统，进行分区的时候根目录分小了不够用，那么可以使用下面方法将空闲分区挂载到某些目录下，达到扩展Linux分区总大小的作用，其步骤为:
1. 将空闲分区格式化为ext4
2. 将空闲分区挂载到临时挂载点
3. 然后将目标文件夹下的文件备份至空闲分区
4. 清空目标文件夹
5. 将有目标文件夹数据的分区挂载至目标文件夹

例如，当空闲分区为 /dev/sdb6, 待挂载目录为 /home 时，挂载命令如下
{% highlight bash %}
# 格式化
$ mkfs -t ext4 /dev/sdb6           # 将分区格式化为 ext4 格式

# 挂载
$ sudo mkdir /mnt/home             # 创建临时挂载点
$ sudo mount /dev/sdb6 /mnt/home   # 挂载
$ sudo cp -a /home/* /mnt/home     # 将 /home 中所有文件拷贝到分区
$ sudo umount /dev/sdb6            # 取消挂载
$ sudo rm -rf /home/*              # 清空 /home
$ sudo mount /dev/sdb6 /home       # 挂载到 /home
$ sudo rm -rf /mnt/home            # 删除临时挂载点

# 将挂载信息写入开机启动文件
$ sudo echo '/dev/sdb6 /home ext4 defaults 1 2' >> /etc/fstab
{% endhighlight %}
