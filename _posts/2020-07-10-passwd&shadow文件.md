---
title: passwd&shadow文件
tags: linux
---

[toc]

> 两文件均在`/etc`目录下

# passwd

这个文件存放着所有用户帐号的信息，包括用户名和密码

passwd文件由许多条记录组成，`每条记录占一行`，记录了一个用户帐号的所有信息。每条记录由`7个字段`组成，字段间用`冒号“：”隔开`，其格式如下：

```bash
username:password:User ID:Group ID:comment:home directory:shell
```

- username:用户名 用户在登录时使用的就是它
- password:该帐号的口令
- User ID :用户识别码，简称UID,Linux系统内部使用UID来标识用户，而不是用户名
- GROUP ID:用户组识别码，简称GID
- comment :这是给用户帐号做的注解,可为空
- home directory:主目录,这个目录属于该帐号，当用户登录后，它就会被置于此目录中，就像回到家一样。一般来说，root帐号的主目录是/root，其他帐号的家目录都在/home目录下，并且和用户名同名
- shell :登录的shell

# shadow

为了增强系统的安全性，Linux系统还可以为用户提供MD5和Shadow安全密码服务。如果在安装 Linux 时在相关配置的选项上选中了MD5和Shadow服务，那么将看到的/etc/passwd文件里的passwd项上无论是什么用户，都是一个“x”，系统其实是把真正的密码数据放在了/etc/shadow文件里。/etc/shadow文件只能以root身份来浏览。

格式如下：

```bash
用户名：加密口令：上一次修改的时间（从1970年1月1日起的天数）：口令在两次修改间的最小天数：口令修改之前向用户发出警告的天数：口令终止后账号被禁用的天数：从1970年1月1日起账号被禁用的天数：保留域
```



> 在linux中，口令文件在/etc/passwd中，早期的这个文件直接存放加密后的密码，前两位是"盐"值，是一个随机数，后面跟的是加密的密码。为了安全，现在的linux都提供了 /etc/shadow这个影子文件，密码放在这个文件里面，并且是只有root可读的。

# 缺省账号

在利用了shadow文件的情况下，密码用一个x表示，普通用户看不到任何密码信息。如果你仔细的看看这个文件，会发现一些奇怪的用户名，她们是系  统的缺省账号，缺省账号是攻击者入侵的常用入口，因此一定要熟悉缺省账号，特别要注意密码域是否为空。下面简单介绍一下这些缺省账号

- adm拥有账号文件，起始目录/var/adm通常包括日志文件
- bin拥有用户命令的可执行文件
- daemon用来执行系统守护进程
- games用来玩游戏
- halt用来执行halt命令
- lp拥有打印机后台打印文件
- mail拥有与邮件相关的进程和文件
- news拥有与usenet相关的进程和文件
- nobody被NFS（网络文件系统）使用
- shutdown执行shutdown命令
- sync执行sync命令
- uucp拥有uucp工具和文件

/etc/passwd文件在很大范围内是可读的，因为许多应用程序需要用他来把UID转换为用户名，例如，如果不能访问/etc/passwd，那么ls -l命令将显示UID而不是用户名

# group补充

etc/group  文件是用户组的配置文件，内容包括用户和用户组，并且能显示出用户是归属哪个用户组或哪几个用户组，因为一个用户可以归属一个或多个不同的用户组；同一用户组的用户之间具有相似的特征。比如我们把某一用户加入到root用户组，那么这个用户就可以浏览root用户家目录的文件，如果root用户把某个文件的读写执行权限开放，root用户组的所有用户都可以修改此文件，如果是可执行的文件（比如脚本），root用户组的用户也是可以执行的；用户组的特性在系统管理中为系统管理员提供了极大的方便，但安全性也是值得关注的，如某个用户下有对系统管理有最重要的内容，最好让用户拥有独立的用户组，或者是把用户下的文件的权限设置为完全私有；另外root用户组一般不要轻易把普通用户加入进去。

格式如下：

```bash
group_name:passwd:GID:user_list
```

- group_name:用户组名称
- passwd:用户组密码
- GID:GID
- user_list:用户列表，每个用户之间用,号分割；本字段可以为空；如果字段为空表示用户组为GID的用户名；

# 密码破解

参照此篇[文章](/_posts/2019-03-13-Hydra)

# 相关linux编程

## 函数介绍

- getpwuid

- getpwnam

  >getpwnam, getpwnam_r, getpwuid, getpwuid_r - get password file entry

  ```c
  #include <sys/types.h>
  #include <pwd.h>
  struct passwd *getpwnam(const char *name);
  struct passwd *getpwuid(uid_t uid);
  int getpwnam_r(const char *name, struct passwd *pwd,
  char *buf, size_t buflen, struct passwd **result);
  int getpwuid_r(uid_t uid, struct passwd *pwd,
  char *buf, size_t buflen, struct passwd **result);
  Feature Test Macro Requirements for glibc (see feature_test_macros(7)):
  getpwnam_r(), getpwuid_r():
  _POSIX_C_SOURCE
  || /* Glibc versions <= 2.19: */ _BSD_SOURCE || _SVID_SOURCE
  ```

passwd结构体定义`<pwd.h>`文件中，具体如下：

```c
struct passwd {
               char   *pw_name;       /* username */
               char   *pw_passwd;     /* user password */
               uid_t   pw_uid;        /* user ID */
               gid_t   pw_gid;        /* group ID */
               char   *pw_gecos;      /* user information */
               char   *pw_dir;        /* home directory */
               char   *pw_shell;      /* shell program */
           };
```

- getgrgid

- getgrgrnam

  > getgrnam, getgrnam_r, getgrgid, getgrgid_r - get group file entry

  ```c
  #include <sys/types.h>
  #include <grp.h>
  
  struct group *getgrnam(const char *name);
  
  struct group *getgrgid(gid_t gid);
  
  int getgrnam_r(const char *name, struct group *grp,
  char *buf, size_t buflen, struct group **result);
  
  int getgrgid_r(gid_t gid, struct group *grp,
  char *buf, size_t buflen, struct group **result);
  
  Feature Test Macro Requirements for glibc (see feature_test_macros(7)):
  
  getgrnam_r(), getgrgid_r():
  _POSIX_C_SOURCE
  || /* Glibc versions <= 2.19: */ _BSD_SOURCE || _SVID_SOURCE
  ```

group结构体定义`<group.h>`文件中，具体如下：

```c
struct group {
               char   *gr_name;        /* group name */
               char   *gr_passwd;      /* group password */
               gid_t   gr_gid;         /* group ID */
               char  **gr_mem;         /* NULL-terminated array of pointers
                                          to names of group members */
           };
```

## shadow密码部分解析

案例：

```markdown
$6$/yP9fQlL$/2sALmuVqVSTCrBo5ObPl8v9cpOvSHX1dHOrAQORCNiB6fJc5.93po8RorRBWo1415wnOdd0EmNmgq.wpwYjE0
```

以*$*符为分割，

- $6$：表示加密方法
- $/yP9fQlL$ :杂质串,需要原串或上这个杂质串,然后经过第一个*$*间定义的加密方式进行加密,然后获得第三个*$*间的内容
- $/2sALmuVqVSTCrBo5ObPl8v9cpOvSHX1dHOrAQORCNiB6fJc5.93po8RorRBWo1415wnOdd0EmNmgq.wpwYjE0 :加密生成的

## 相关函数

- getspnam

> getspnam, getspnam_r, getspent, getspent_r, setspent, endspent, fgetspent, fgetspent_r, sgetspent,
>   sgetspent_r, putspent, lckpwdf, ulckpwdf - get shadow password file entry

```c
#include <shadow.h>
struct spwd *getspnam(const char *name);
struct spwd *getspent(void);
void setspent(void);
void endspent(void);
struct spwd *fgetspent(FILE *stream);
struct spwd *sgetspent(const char *s);
int putspent(const struct spwd *p, FILE *stream);
int lckpwdf(void);
int ulckpwdf(void);
/* GNU extension */
#include <shadow.h>
int getspent_r(struct spwd *spbuf,
char *buf, size_t buflen, struct spwd **spbufp);
int getspnam_r(const char *name, struct spwd *spbuf,
char *buf, size_t buflen, struct spwd **spbufp);
int fgetspent_r(FILE *stream, struct spwd *spbuf,
char *buf, size_t buflen, struct spwd **spbufp);
int sgetspent_r(const char *s, struct spwd *spbuf,
char *buf, size_t buflen, struct spwd **spbufp);
```

相应结构体为于`<shadow.h>`

```c
struct spwd {
               char *sp_namp;     /* Login name */
               char *sp_pwdp;     /* Encrypted password */
               long  sp_lstchg;   /* Date of last change
                                     (measured in days since
                                     1970-01-01 00:00:00 +0000 (UTC)) */
               long  sp_min;      /* Min # of days between changes */
               long  sp_max;      /* Max # of days between changes */
               long  sp_warn;     /* # of days before password expires
                                     to warn user to change it */
               long  sp_inact;    /* # of days after password expires
                                     until account is disabled */
               long  sp_expire;   /* Date when account expires
                                     (measured in days since
                                     1970-01-01 00:00:00 +0000 (UTC)) */
               unsigned long sp_flag;  /* Reserved */
           };
```

- crypt

> crypt, crypt_r - password and data encryption

```c
#define _XOPEN_SOURCE       /* See feature_test_macros(7) */
#include <unistd.h>

char *crypt(const char *key, const char *salt);

#define _GNU_SOURCE         /* See feature_test_macros(7) */
#include <crypt.h>

char *crypt_r(const char *key, const char *salt,
              struct crypt_data *data);
```

> 注意：编译时需要带上` -lcrypt.`

结合shadow文件密码部分，发现`crypt`函数参数并没有定义加密方法，其实这个部分被整合到salt部分去了，参照如下：

The glibc2 version of this function supports additional encryption algorithms.

If salt is a character string starting with the characters "$id$" followed by a string  optionally
   terminated by "$", then the result has the form:

          $id$salt$encrypted

   id  identifies  the encryption method used instead of DES and this then determines how the rest of
   the password string is interpreted.  The following values of id are supported:

| ID   | Method                                                       |
| ---- | ------------------------------------------------------------ |
| 1    | MD5                                                          |
| 2a   | Blowfish (not in mainline glibc; added in some Linux distributions) |
| 5    | SHA-256                                                      |
| 6    | SHA-512                                                      |

Thus, $5$salt$encrypted and $6$salt$encrypted contain the password encrypted  with,  respectively,
   functions based on SHA-256 and SHA-512.

   "salt"  stands  for the up to 16 characters following "$id$" in the salt.  The "encrypted" part of
   the password string is the actual computed password.  The size of this string is fixed:

   MD5     | 22 characters
   SHA-256 | 43 characters
   SHA-512 | 86 characters

   The characters in "salt" and "encrypted" are drawn from the set [a-zA-Z0-9./].  In the MD5 and SHA
   implementations the entire key is significant (instead of only the first 8 bytes in DES).

   Since glibc 2.7, the SHA-256 and SHA-512 implementations support a user-supplied number of hashing
   rounds, defaulting to 5000.  If the "$id$" characters in the salt are followed  by  "rounds=xxx$",
   where xxx is an integer, then the result has the form

          $id$rounds=yyy$salt$encrypted

   where  yyy  is  the number of hashing rounds actually used.  The number of rounds actually used is
   1000 if xxx is less than 1000, 999999999 if xxx is greater than 999999999, and  is  equal  to  xxx
   otherwise

检测用户密码例子

> 代码不规范，没有做一些判断处理等细节
>
> 编译： gcc checkpass.c -lcrypt -D _XOPEN_SOURCE
>
> 注意需要用root用户去运行(需要访问shadow文件)，不然会有段错误

```c
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
#include <shadow.h>
int main(int argc,char ** argv[])
{
    char *input_pass;
    struct spwd *shadowline;
    char *crypt_pass;
    if(argc < 2)
    {
        fprintf(stderr,"usage ...\n");
        exit(EXIT_SUCCESS);
    }
    //获取用户口令
    //关闭输入回显
    input_pass = getpass("PassWord:");
    shadowline =  getspnam(argv[1]);
    crypt_pass = crypt(input_pass,shadowline->sp_pwdp);
    if(strcmp(shadowline->sp_pwdp,crypt_pass) == 0)
    {
        puts("ok");
    }
    else
    {
        puts("error");
    }
    return 0;
}
```

