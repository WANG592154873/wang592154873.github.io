---
layout: post
title: 为Ubuntu Mate for ARM 更换中国软件源
date: 2017-12-05
categories: blog
tags: [Ubuntu Mate]
description: 
---

更换步骤

以`root`身份打开` /etc/apt/sources.list `，至于用什么打开随意
将 `http://ports.ubuntu.com/` 全部替换为 `http://mirrors.ustc.edu.cn/ubuntu-ports/` ，这是中科大的
执行 `sudo apt-get update` 测试
查看是否提供arm支持的方法

打开一个镜像站点，然后以此打开 `/dists/xenial/main/` ，看这个目录下面有没有` binary-arm `这样的字眼，如果有，就是提供`arm`支持的