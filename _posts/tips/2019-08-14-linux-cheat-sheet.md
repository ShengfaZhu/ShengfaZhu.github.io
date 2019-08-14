---
layout: post
title: linux cheating sheet 
categories: [Tips]
description: Linux-Ubuntu系统上的常见命令
keywords: Linux, Ubuntu
---

### 1. 压缩与解压

tar压缩与解压命令

```shell
-c 压缩
-x 解压
-v 显示压缩、解压过程
-f 压缩文件名
-C 指定解压文件夹
examples:
tar -cvf test.tgz *.txt
tar -xvf test.tgz -C ./test/ 
```

