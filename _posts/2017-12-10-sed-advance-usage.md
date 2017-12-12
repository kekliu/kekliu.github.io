---
layout:     post
title:      "sed 的高级用法"
subtitle:   "sed的高级用法，使用到了 pattern space 和 hold space"
date:       2017-12-10
author:     "kekliu"
header-img: "img/post-bg-unix-linux.jpg"
catalog:    true
tags:
    - Linux
    - sed
---

d 清空模式空间,开始下一个循环<br>
D 若模式空间有新行,删除模式空间第一行,使用剩余的模式空间开始下一个循环而非读取文件;若无新行,直接退出

g 使用保持空间替换模式空间<br>
G 将保持空间正文全部追加到模式空间

h 使用模式空间替换保持空间<br>
H 将模式空间正文全部追加到保持空间

n 若自动打印没有被关闭,打印模式空间,读取一行,填充整个模式空间,若无新行,则退出sed<br>
N 追加一行到模式空间,若无新行,则退出sed

p 打印模式空间<br>
P 打印模式空间第一行
