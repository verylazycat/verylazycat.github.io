---
title: John_the_Ripper
tags: 安全
---

[toc]

# 安装

官方网站：http://www.openwall.com/john/

下载：wget http://www.openwall.com/john/j/john-1.8.0.tar.gz

解压：tar -xvf john-1.8.0.tar.gz

进入src目录：

cd john-1.8.0 && cd src

```bash
make
```

根据自己系统版本选择,一般为如下

```bash
make clean linux-x86-64
```

编译成功会在run目录下生成john可执行文件

# 破解

把想破解的/etc/shadow放在shadow.txt文件夹下：

```bash
./john shadow.txt
```

John the Ripper的默认密码字典为run目录下的password.lst