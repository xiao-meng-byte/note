# Linux学习笔记

## 1. 常见操作系统

### PC端
- Windows
- Linux
- macOS

### 移动端
- Android
- iOS
- HarmonyOS

---

## 2. Linux系统简介

### 2.1 Linux是什么
- 创始人：林纳斯·托瓦兹
- 诞生于1991年
- **严格来说主要指Linux内核**，负责CPU调度、内存管理、进程管理、文件系统、网络、设备、I/O、系统调用

### 2.2 Linux发行版
Linux内核 + 系统工具 + 软件包管理器 = 发行版

**主流发行版**：
- Ubuntu
- Debian
- Fedora
- Rocky Linux、AlmaLinux、Arch Linux、openSUSE
- RHEL

**CentOS已停止维护**，Rocky/Alma通常作为RHEL兼容版使用。

---

## 3. 虚拟机环境

### 3.1 VirtualBox
- 支持创建Linux虚拟机
- 推荐环境：Windows + VirtualBox + Rocky Linux 9

**常用网络模式**：
| 模式         | 作用               |
|--------------|-------------------|
| NAT          | 虚拟机上网          |
| 桥接         | 虚拟机加入局域网     |
| 仅主机模式   | 宿主机与虚拟机互访   |
| NAT网络      | 多台虚拟机通信      |

**推荐配置**：
- 网卡1：NAT（上网）
- 网卡2：仅主机模式（互访）

---

### 3.2 SSH远程连接
```bash
ssh root@192.168.56.101
```
**退出**：`exit` 或 `Ctrl + D`

---

### 3.3 关机与重启
```bash
shutdown -h now   # 立即关机
reboot            # 立即重启
shutdown -r now   # 立即重启
```

---

### 3.4 Linux使用方式
- **GUI**：图形化界面
- **CLI**：命令行界面（服务器为主）
- 远程工具：Xshell、PuTTY、FinalShell、VS Code Remote SSH

---

### 3.5 虚拟机快照
非常适合学习：安装完软件后创建快照，出问题可直接恢复。

---

## 4. Linux目录基础

### 4.1 根目录
Linux只有一个根目录：`/`

所有文件都从 `/` 开始

---

### 4.2 常见目录
```
/├── bin
├── boot
├── dev
├── etc          # 配置文件（如 /etc/passwd、/etc/hosts）
├── home         # 普通用户家目录
├── root         # root用户家目录
├── run
├── sbin
├── tmp          # 临时文件
├── usr
└── var          # 日志（/var/log）
```

---

## 5. Linux命令基础

### 5.1 命令通用格式
```bash
command [-选项] [-参数]
```

示例：
```bash
ls -lh /home
```

---

## 6. ls、cd、pwd

### 6.1 ls
```bash
- 作用：列出目录内容

- 语法：ls [-alh] [路径]

  - 选项：-a，显示所有文件（包括以.开头的隐藏文件）
  - 选项：-l，以列表形式显示详细信息
  - 选项：-h，以人类可读方式显示文件大小（如K、M、G），需配合-l使用
  - 参数：路径，要查看的目录路径，不指定则查看当前目录
```
```bash
ls
ls -a
ls -l
ls -lh
ls -lah
```

### 6.2 cd
* 作用：切换工作目录
* 语法：cd [路径]
   * 参数：路径，要切换到的目录路径
```bash
cd /home
cd ..
cd ~
cd /
```

### 6.3 pwd
* 作用：显示当前工作目录的绝对路径
* 语法：直接pwd
   * 无选项和参数
```bash
pwd
```

---

## 7. 特殊路径符号

| 符号 | 含义         |
|------|-------------|
| `.`  | 当前目录     |
| `..` | 上一级目录   |
| `~`  | 当前用户家目录 |
| `/`  | 根目录       |

---

## 8. Linux权限字符串

执行 `ls -l` 后：
```text
-rw-r--r-- 1 root root 123 test.txt
```

### 文件类型
- `-` 普通文件
- `d` 目录
- `l` 软链接

### 后9位权限
- **User**（属主）
- **Group**（属组）
- **Other**（其他用户）

### 权限含义
- `r` = 读（4）
- `w` = 写（2）
- `x` = 执行（1）

常用权限值：
- `rwxr-xr-x` = 755
- `rw-r--r--` = 644
- `rwx------` = 700

$chmod$命令:
```bash
-作用：修改文件或目录的权限
-语法：chmod [-R] [权限] 文件或文件夹
   -选项：-R，对文件夹内全部内容递归应用相同规则
   -参数：权限，数字方式（如755）或符号方式（如+x）
   -参数：文件或文件夹，要修改权限的目标
```
```bash
chmod 755 test.sh     # 数字方式
chmod +x test.sh      # 符号方式
chmod -R 755 test/    # 递归
```

---

## 9. 文件与文件夹操作

### mkdir
$mkdir$ 命令：
* 作用：创建目录
* 语法：**mkdir [-p] 目录路径**
  *  选项：-p，递归创建多级目录
  * 参数：目录路径，要创建的目录路径
```bash
mkdir test
mkdir -p a/b/c     # 递归创建
```

### touch
$touch$ 命令：
* 作用：创建空文件或更新文件时间戳
* 语法：**touch 文件路径**
   * 参数：文件路径，要创建或更新的文件路径
```bash
touch test.txt
```

### cp

$cp$ 命令：

- 作用：复制文件或目录
- 语法：**cp [-r] 源路径 目标路径**
  - 选项：$-r$，递归复制目录
  - 参数：源路径，要复制的文件或目录
  - 参数：目标路径，复制到的位置

```bash
cp a.txt b.txt
cp -r dir1 dir2
```

### mv

$mv$ 命令：

- 作用：移动文件或目录（也可用于重命名）
- 语法：**mv 源路径 目标路径**
  - 参数：源路径，要移动的文件或目录
  - 参数：目标路径，移动到的位置或新名称

```bash
mv a.txt /tmp/
mv old.txt new.txt
```

### rm

$rm$ 命令：

- 作用：删除文件或目录
- 语法：**rm [-rf] 文件或文件夹**
  - 选项：$-r$，递归删除目录
  - 选项：$-f$，强制删除，不提示确认
  - 参数：文件或文件夹，要删除的目标

```bash
rm a.txt
rm -f a.txt
rm -r test
rm -rf test          # 极危险
```

---

## 10. 通配符

| 通配符 | 含义               |
|--------|-------------------|
| `*`    | 任意长度字符       |
| `?`    | 一个字符           |
| `[abc]` | 字符集合           |

---

## 11. 查找命令

### which

$which$ 命令：

- 作用：查找命令的可执行文件位置
- 语法：**which 命令名**
  - 参数：命令名，要查找的命令名称

```bash
which ls
```

### find

$find$ 命令：

```bash
-作用：在文件系统中查找符合条件的文件
-语法：find [路径] [选项] [表达式]
 --参数：路径，要搜索的目录路径
 --选项：-name，按文件名匹配
 --选项：-size，按文件大小筛选
 --选项：-user，按文件所有者筛选
```

```bash
find . -name "*.txt"
find . -size +100M
find . -user zhangsan
```

---

## 12. 文本处理

| 命令   | 作用               |
|--------|-------------------|
| `cat` | 一次性查看整个文件 |
| `more`| 分页查看（按空格） |
| `less`| 分页查看（支持搜索） |
| `head` | 查看前N行           |
| `tail` | 查看后N行（`tail -f`实时日志） |

- $cat$命令
  - 作用：一次性查看整个文件内容
  - 语法：**cat 文件路径**
    - 参数：文件路径，要查看的文件
- $more$命令
  - 作用：分页查看文件内容（按空格翻页）
  - 语法：**more 文件路径**
    - 参数：文件路径，要查看的文件
- $cat$ 命令和$more$ 命令的区别：

- 显示方式：
  - cat一次性显示全部内容，文件过长时内容会快速滚过
  - more分页显示，每屏只显示一页，按空格继续查看
- 交互操作：
  - cat只能从头到尾显示完，无法进行交互
  - more支持交互操作（空格翻页、回车滚动一行、q退出）

| 操作    | cat  | more         |
| ------- | ---- | ------------ |
| 空格    | 无   | 显示下一页   |
| 回车    | 无   | 向下滚动一行 |
| b键     | 无   | 显示上一页   |
| q键     | 无   | 退出查看     |
| /关键词 | 无   | 搜索关键词   |
| =       | 无   | 显示当前行号 |

bash

```
more -10 a.txt     # 每屏只显示10行
```

- 加载方式：
  - cat一次性全部加载到内存
  - more逐页加载，适合大文件

bash

```
# 查看大文件时more更合适
cat /var/log/syslog        # 内容过长，不易查看
more /var/log/syslog       # 分页查看，方便浏览
```

- 回滚能力：
  - cat不支持向上回滚
  - more支持按b键向上翻页
- 应用场景对比：
  - 适合使用cat的场景：
    - 文件内容较少，一屏能看完
    - 需要将文件内容作为其他命令的输入
    - 合并多个文件时使用cat
  - 适合使用more的场景：
    - 文件内容较长（如日志文件）
    - 需要分页查看，避免内容快速滚动
    - 需要在文件中上下翻动浏览
- 补充建议：
  - more功能相对基础，less是more的增强版
  - less支持上下翻页、前后搜索、更丰富的交互操作

- less命令
  - 作用：分页查看文件内容（支持搜索）
  - 语法：**less 文件路径**
    - 参数：文件路径，要查看的文件

- head命令
  - 作用：查看文件前N行（默认10行）
  - 语法：**head [-n] 文件路径**
    - 选项：$-n$，指定显示的行数
    - 参数：文件路径，要查看的文件

- tail命令
  ```bash
  - 作用：查看文件后N行（默认10行）
  - 语法：tail [-n] [-f] 文件路径
    - 选项：-n，指定显示的行数
    - 选项：-f，实时跟踪文件追加内容（常用于查看日志）
      -- 参数：文件路径，要查看的文件
  ```

- grep命令
  - 作用：在文件中搜索匹配的文本内容
  - 语法：**grep [-nri] 搜索内容 文件路径**
    - 选项：$-n$，显示匹配行的行号
    - 选项：$-r$，递归搜索目录内的所有文件
    - 选项：$-i$，忽略大小写
      - 参数：搜索内容，要查找的字符串或正则表达式
      - 参数：文件路径，要搜索的文件或目录

**grep**（搜索/过滤）

```bash
grep "hello" a.txt
grep -n "hello" a.txt
grep -i "hello" a.txt
grep -r "hello" .
ps -ef | grep mysql
```

---

## 13. wc

$wc$ 命令：

- 作用：统计文件的行数、单词数、字符数
- 语法：wc [-lwc] 文件路径
  - 选项：-l，统计行数
  - 选项：-w，统计单词数
  - 选项：-c，统计字符数
  - 参数：文件路径，要统计的文件

```bash
wc a.txt
wc -l a.txt
wc -w a.txt
wc -c a.txt
```

---

## 14. echo

$echo$ 命令：

- 作用：在终端输出文本或变量的值
- 语法：**echo [字符串或变量]**
  - 参数：字符串或变量，要输出的内容

```bash
echo hello
echo $PATH
echo hello > a.txt
echo hello >> a.txt
```

---

## 15. 命令替换

- 作用：将命令的输出结果作为另一个命令的参数
- 语法：$(命令) 或 `命令`

```bash
echo $(pwd)
```

---

## 16. 管道符 `|`

- 作用：将前一个命令的输出结果传递给后一个命令作为输入
- 语法：命令1 | 命令2

```bash
ps -ef | grep mysql
ls -l | grep ".txt"
```

---

## 17. 重定向
| 符号 | 含义               |
|------|-------------------|
| `>`  | 覆盖写入           |
| `>>` | 追加写入           |
| `<`  | 文件输入           |

---

## 18. Vim编辑器
三种模式：
- 命令模式 → `i` 进入输入
- 输入模式 → `Esc` 退出
- 底线命令 → `:` 开头

**常用操作**：
- 输入：`i`、`a`、`o`
- 删除：`dd`
- 复制：`yy`
- 粘贴：`p`
- 保存：`:w`
- 退出：`:q` 或 `:q!`
- 行号：`:set nu`

---

## 19. 用户与权限

**root** 用户拥有最大的操作系统权限，而普通用户在更多地方是受限的。

### 用户切换

$su$ 命令就是用于用户切换的系统命令，语法为：$su [-][用户名] $

* 符号是可选的，表示是否在切换用户后加载环境变量
* 参数：用户名，表示要切换的用户，用户名也可以省略，表示切换到**root**
* 切换完用户后，可以通过**exit** 命令退回上一个用户，也可以使用快捷键: $ctrl +d$ 
* 普通用户切换到其他用户要输入密码，而**root**用户切换到其他用户可以直接切换

$sudo$ 命令：为普通的命令授权，临时以**root** 身份执行，语法：$sudo    其他命令$ 


1. 切换到 root 用户或使用 sudo
```bash
su - root  /sudo -i
```
2. 将普通用户添加到 sudo 组
```bash
[root@localhost ~]# usermod -aG sudo 用户名
[root@localhost ~]# 
```
3. 验证用户是否已加入 sudo 组
```bash
[root@localhost ~]# groups 用户名
itheima : itheima sudo
[root@localhost ~]# 
```
或者也可以：
```bash
[root@localhost ~]# visudo
```
然后在文件末尾添加：
```bash
用户名 ALL=(ALL:ALL) /usr/bin/systemctl, /usr/bin/apt
```
保存退出（:wq）
```bash
su - root
sudo 命令
```

### 用户管理

以下命令需要用**root** 用户才可使用：

* 创建用户组：**groupadd  用户组名**
* 删除用户组: **groupdel 用户组名**
* 创建用户： **useradd [-g -d] 用户名 **

  * 选项：$-g$ 指定用户的组，不指定 $-g$ ,会创建同名组并自动加入，指定$-g$ 需要组已经存在，如已存在同名组，必须使用$-g$

  * 选项： $-d$ 指定用户**HOME**,不指定，**HOME**目录默认在: /home/用户名
* 删除用户： **userdel  [-r] 用户名**
  * 选项：$-r$ ,删除用户的**HOME**目录，不使用$-r$ ,删除用户时，**HOME**目录保留
* 查看用户所在组：**id [用户组]** :
  * 参数：用户名，被查看的用户，如果不提供则查看自身
* 修改用户所属的组：**usermod -aG 用户组**， 用户名将指定用户加入指定用户组

**getent**命令：可以查看系统中有哪些用户，语法：**getent passwd** 

```bash
root@mengyunxiao:~# getent passwd
root:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
bin:x:2:2:bin:/bin:/usr/sbin/nologin
sys:x:3:3:sys:/dev:/usr/sbin/nologin
sync:x:4:65534:sync:/bin:/bin/sync
games:x:5:60:games:/usr/games:/usr/sbin/nologin
man:x:6:12:man:/var/cache/man:/usr/sbin/nologin
lp:x:7:7:lp:/var/spool/lpd:/usr/sbin/nologin
mail:x:8:8:mail:/var/mail:/usr/sbin/nologin
news:x:9:9:news:/var/spool/news:/usr/sbin/nologin
uucp:x:10:10:uucp:/var/spool/uucp:/usr/sbin/nologin
proxy:x:13:13:proxy:/bin:/usr/sbin/nologin
www-data:x:33:33:www-data:/var/www:/usr/sbin/nologin
backup:x:34:34:backup:/var/backups:/usr/sbin/nologin
list:x:38:38:Mailing List Manager:/var/list:/usr/sbin/nologin
irc:x:39:39:ircd:/run/ircd:/usr/sbin/nologin
_apt:x:42:65534::/nonexistent:/usr/sbin/nologin
nobody:x:65534:65534:nobody:/nonexistent:/usr/sbin/nologin
systemd-network:x:998:998:systemd Network Management:/:/usr/sbin/nologin
dhcpcd:x:996:996:DHCP Client Daemon:/usr/lib/dhcpcd:/bin/false
messagebus:x:995:995:System Message Bus:/nonexistent:/usr/sbin/nologin
syslog:x:100:101::/nonexistent:/usr/sbin/nologin
systemd-resolve:x:989:989:systemd Resolver:/:/usr/sbin/nologin
_chrony:x:988:988:Chrony Daemon:/var/lib/chrony:/usr/sbin/nologin
tss:x:987:987:tss user for tpm2:/:/usr/sbin/nologin
uuidd:x:101:105::/run/uuidd:/usr/sbin/nologin
systemd-oom:x:986:986:systemd Userspace OOM Killer:/:/usr/sbin/nologin
whoopsie:x:102:108::/nonexistent:/bin/false
dnsmasq:x:999:65534:dnsmasq:/var/lib/misc:/usr/sbin/nologin
avahi:x:103:109:Avahi mDNS daemon:/run/avahi-daemon:/usr/sbin/nologin
nm-openvpn:x:984:984:NetworkManager OpenVPN:/var/lib/openvpn/chroot:/usr/sbin/nologin
tcpdump:x:983:983:tcpdump:/nonexistent:/usr/sbin/nologin
sssd:x:104:110:SSSD system user:/var/lib/sss:/usr/sbin/nologin
speech-dispatcher:x:105:29:Speech Dispatcher:/run/speech-dispatcher:/bin/false
usbmux:x:106:46:usbmux daemon:/var/lib/usbmux:/usr/sbin/nologin
cups-pk-helper:x:107:111:user for cups-pk-helper service:/nonexistent:/usr/sbin/nologin
fwupd-refresh:x:982:982:Firmware update daemon:/var/lib/fwupd:/usr/sbin/nologin
saned:x:108:112::/var/lib/saned:/usr/sbin/nologin
geoclue:x:109:113::/var/lib/geoclue:/usr/sbin/nologin
cups-browsed:x:110:111::/nonexistent:/usr/sbin/nologin
pipewire:x:981:981:system user for pipewire:/nonexistent:/usr/sbin/nologin
hplip:x:111:7:HPLIP system user:/run/hplip:/bin/false
gnome-remote-desktop:x:980:980:GNOME Remote Desktop:/var/lib/gnome-remote-desktop:/usr/sbin/nologin
polkitd:x:979:979:User for polkitd:/:/usr/sbin/nologin
rtkit:x:978:978:RealtimeKit:/proc:/usr/sbin/nologin
colord:x:977:977:colord colour management daemon:/var/lib/colord:/usr/sbin/nologin
gdm:x:975:975:Gnome Display Manager:/var/lib/gdm3:/bin/false
mengyunxiao:x:1000:1000:mengyunxiao:/home/mengyunxiao:/bin/bash
vboxadd:x:997:1::/var/run/vboxadd:/bin/false
sshd:x:971:65534:sshd user:/run/sshd:/usr/sbin/nologin
luolongtao:x:1001:1001::/home/luolongtao:/bin/sh
以上表示的是：用户名：密码（x代替）：用户ID:组ID:描述信息（无用）：HOME目录：执行终端（默认bash）
```

```bash
useradd zhangsan
passwd zhangsan
userdel zhangsan
usermod -aG wheel zhangsan
```

---
### 查看权限信息

```bash
mengyunxiao@mengyunxiao-VMware-Virtual-Platform:~/Desktop$ ls -al
total 104
drwxr-xr-x  3 mengyunxiao mengyunxiao  4096 Aug 31 11:58 .
drwxr-x--- 15 mengyunxiao mengyunxiao  4096 Aug 31 11:22 ..
-rw-rw-r--  1 mengyunxiao mengyunxiao  2858 Aug 31 10:31 Book.class
-rw-rw-r--  1 mengyunxiao mengyunxiao  7842 Aug 31 10:31 Library.class
-rw-rw-r--  1 mengyunxiao mengyunxiao  5566 Aug 31 10:31 Main.class
-rw-rw-r--  1 mengyunxiao mengyunxiao 16053 Aug 31 10:31 Main.java
-rw-rw-r--  1 mengyunxiao mengyunxiao  4961 Aug 31 11:46 dengxiangrong.gz
-rw-rw-r--  1 mengyunxiao mengyunxiao 13334 Aug 31 11:57 lingmeiling.zip
drwxrwxr-x  2 mengyunxiao mengyunxiao  4096 Aug 31 10:24 luolongtao
-rw-rw-r--  1 mengyunxiao mengyunxiao   149 Aug 31 10:37 luolongtao.txt
-r--------  1 mengyunxiao mengyunxiao  2879 Jul 26 10:54 main.cpp
-rw-rw-r--  1 mengyunxiao mengyunxiao 20480 Aug 31 11:44 mengyunxiao.tar
-rw-rw-r--  1 mengyunxiao mengyunxiao   577 Aug 31 10:39 sysinfo.sh

以上内容和一下一一对应
┌──────────────┐ ┌─┐ ┌────────────┐ ┌────────────┐ ┌────┐ ┌─────────────┐ ┌──────────────┐
│  权限信息     │ │链│ │  所属用户   │ │  所属用户组 │ │大小│ │  修改时间    │ │  文件/文件夹名 │
│              │ │接│ │            │ │            │ │    │ │             │ │              │
│ -rw-rw-r--   │ │数│ │ mengyunxiao│ │ mengyunxiao│ │2858│ │ Aug 31 10:31│ │ Book.class   │
└──────────────┘ └─┘ └────────────┘ └────────────┘ └────┘ └─────────────┘ └──────────────┘
      ①           ②        ③             ④           ⑤          ⑥             ⑦
以luolongtao为例：
┌────────────┐┌─┐┌────────────┐┌────────────┐┌──────┐┌─────────────┐┌──────────────┐
│ drwxrwxr-x ││2││ mengyunxiao││ mengyunxiao││ 4096 ││ Aug 31 10:24││ luolongtao   │
└────────────┘└─┘└────────────┘└────────────┘└──────┘└─────────────┘└──────────────┘
      ①         ②        ③             ④           ⑤           ⑥             ⑦
```

那么，rwx到底代表什么呢？

- r表示读权限
- w表示写权限
- x表示执行权限

针对文件、文件夹的不同，rwx的含义有细微差别

- r，针对文件可以查看文件内容
  - 针对文件夹，可以查看文件夹内容，如ls命令

- w，针对文件表示可以修改此文件
  - 针对文件夹，可以在文件夹内：创建、删除、改名等操作

- x，针对文件表示可以将文件作为程序执行
  - 针对文件夹，表示可以更改工作目录到此文件夹，即cd进入

###  修改权限

* $chmod$ 命令：修改文件/文件夹的权限信息，只有文件，文件夹的所属的用户或**root** 用户可以修改，语法：**chmod [-R] 权限 文件或文件夹** 

  * 选项：**-R** 对文件夹内的全部内容应用同样的操作

  * 比如说： **chmod u=wx,g=rx,o=x luolongtao.txt** 就是将文件权限改为：**rwxr-x--x** (其中**u**代表**user** 所属用权限，**g**表示**group**代表**group**组权限，**o**表示**other** 其他用户权限)

  * 除此之外，还快捷写法：**chmod 751 luolongtao.txt** 就是把这个文件的权限改为751,我们的权限是可以用数字来表示的：
      *  0：无任何权限，即  **---**
      *  1：仅有**x**权限，即  **--x**
      *  2：仅有**w**权限，即 **-w-**
      *  3：仅有**w**和**x**权限，即 **-wx**
      *  4：仅有**r**权限，即 **r--**
      *  5:   仅有**r**和**x**权限，即 **r-x**
      *  6:  仅有**r**和**w**权限，即 **rw-**
      *  7：有全部权限，即 **rwx**
      * 所以751即为**rwx r-x --x** ，记忆的话就直接**r=4,w=2,x=1**

* **chown**命令: 可以修改文件/文件夹的所属用户和用户组（$\textcolor{red}{普通用户无法修改所属的其他用户/用户组，所以此命令只适用于root用户执行}$）
  * 语法：
   ```bash
        chown [选项] [用户名][:][用户组] 文件或文件夹
        - 选项：-R，对文件夹内全部内容应用相同规则
        - 用户名：修改所属用户
        - 用户组：修改所属用户组
        - ：用于分隔用户和用户组
      示例：
        - chown root hello.txt，将hello.txt所属用户修改为root
        - chown :root hello.txt，将hello.txt所属用户组修改为root
        - chown root:itheima hello.txt，将hello.txt所属用户修改为root，用户组修改为itheima
        - chown -R root test，将文件夹test的所属用户修改为root并对文件夹内全部内容应用同样规则
   ```
  * ```bash
    - chown命令
    - 功能：修改文件、文件夹的所属用户、组
    - 限制：只可root执行
    - 语法：chown [-R] [用户][:][用户组] 文件或文件夹
    - 选项：-R，同chmod，对文件夹内全部内容应用相同规则
    - 选项：用户，修改所属用户
    - 选项：用户组，修改所属用户组
    - ：用于分隔用户和用户组
    ```

* 一些小技巧：
  *  **ctrl + c**强制停止程序运行（在程序卡住的时候，但是不能退出**vi/vim**）
  * **ctrl + d** 退出或者登出，包括退出账户的登录和退出某些特定程序的专属页面（也不能退出**vi/vim**）
  *  **history** 查看历史输入过的内容（可以利用**grep**辅助过滤），可以通过**！命令前缀** 自动执行上一次匹配前缀的命令：

  ```bash
  mengyunxiao@mengyunxiao:~$ history | grep ch(history | grep ch 表示从命令历史中筛选包含 "ch" 的记录)
     18  echo "hello Linux"
     25  touch luolongtao.md
     56  echo "deb [arch=amd64] https://packages.microsoft.com/repos/vscode stable main" | sudo tee /etc/apt/sources.list.d/vscode.list
     75  nano chaiguilun.txt
     76  nano chaiguilun.txt
    106  chmod u=rwx,g=r,o=w main.c
    108  chmod 0000 luolongtao.txt
    117  touch luolongtao.md
    120  chmod 0000 luolongtao.md
    124  chown root luolongtao.txt
    126  chown luolongtao.md
    128  history | grep ch
  mengyunxiao@mengyunxiao:~$
  ```

  * **ctrl + r** 可以输入内容去匹配历史命令：如果搜到的命令是需要的，那就可以：
    * 回车键直接执行
    * 键盘左右键，可以得到此命令（不执行）
  * 光标移动快捷键：
    * **ctrl + a** ,跳到命令开头
    * **ctrl + e**,跳到命令结尾
    * **ctrl + 键盘左键 **，向左跳一个单词
    * **ctrl + 键盘右键 **，向右跳一个单词
    * **ctrl + l或clear命令** 可以清空终端内容


## 20. 软件安装（RHEL系列与Ubuntu）

### 20.1 RHEL系列（Rocky Linux / RHEL / AlmaLinux）
```bash
dnf install -y nginx
dnf remove nginx
systemctl enable --now nginx
```

**centorOS安装命令**：

```bash
- yum命令
- yum：RPM包软件管理器，用于自动化安装配置Linux软件，并可以自动解决依赖问题。
- 语法：yum [-y] [install | remove | search] 软件名称
  - 选项：-y，自动确认，无需手动确认安装或卸载过程
  - install：安装
  - remove：卸载
  - search：搜索
- yum命令需要root权限，可以su切换到root，或使用sudo提权。
- yum命令需要联网
```

### 20.2 Ubuntu / Debian

语法：

```bash
- 通过前面学习的WSL环境，我们可以得到Ubuntu运行环境。
- 语法：apt [-y] [install | remove | search] 软件名称
- 用法和yum一致，同样需要root权限
  - apt install wget，安装wget
  - apt remove wget，移除wget
  - apt search wget，搜索wget
```

``` bash
sudo apt update
sudo apt upgrade
sudo apt install nginx
sudo apt remove nginx
sudo apt purge nginx
```

### 20.3 软件包管理对比
| 项目          | RHEL系列（dnf）       | Ubuntu/Debian（apt）   |
|---------------|-----------------------|-----------------------|
| 更新索引      | `dnf makecache` 或自动 | `apt update`         |
| 安装          | `dnf install -y`     | `apt install`        |
| 删除          | `dnf remove`         | `apt remove`         |
| 彻底删除      | `dnf remove`         | `apt purge`          |
| 开机启动      | `systemctl enable`   | `systemctl enable`   |

---

## 21. systemctl服务管理

$systemctl$ 命令：控制启动，停止，开机自启，能被$systemctl$ 管理的软件，一般也被称为**服务**，语法：**systemctl start | stop |status | enable |disable 服务名 ** ，它的内置服务比较多：

* **NetworkManager**,主网络服务
* **network** ,副网络服务
* **firewalld**,防火墙服务
* **sshd**,**ssh**服务（**Finalshell**远程登录，Linux使用的就是这个服务）
* **ubuntu** 默认使用的防火墙是**ufw**,所以他的指令为：
  * 查看状态：**sudo systemctl status ufw**
  * 查看规则：**sudo ufw status verbose**
  * 开启防火墙：**sudo ufw enable**
  * 关闭防火墙：**sudo ufw disable** 

```bash
systemctl start/stop/restart/enable/disable/status 服务名
systemctl enable --now mysqld
```

---

## 22. 软链接

在系统中创建链接，可以将文件，文件夹连接到其他位置。类似于**windows**系统中的快捷方式，语法：**ln -s 参数1 参数2 **

* $-s$ 选项，创建文件软连接
* **参数1**：被链接的文件或者文件夹
* **参数2**：要链接去的目的地

```bash
ln -s /opt/test.txt /tmp/test.txt
```

---

## 23. 日期与时间

$date$ 命令：查看系统的时间，语法：

```bash
- 通过date命令可以在命令行中查看系统的时间
- 语法：date [-d] [+格式化字符串]
  - -d 按照给定的字符串显示日期，一般用于日期计算
  - 格式化字符串：通过特定的字符串标记，来控制显示的日期格式
    - %Y 年
    - %y 年份后两位数字 (00..99)
    - %m 月份 (01..12)
    - %d 日 (01..31)
    - %H 小时 (00..23)
    - %M 分钟 (00..59)
    - %S 秒 (00..60)
    - %s 自 1970-01-01 00:00:00 UTC 到现在的秒数
```

比如输入指令：

```bash
mengyunxiao@mengyunxiao:~/Desktop$ date
Sun Aug 30 11:16:38 AM CST 2026
mengyunxiao@mengyunxiao:~/Desktop$ date +%Y-%m-%d
2026-08-30
mengyunxiao@mengyunxiao:~/Desktop$ date '+%Y-%m-%d %H:%M:%S'
2026-08-30 11:18:00
mengyunxiao@mengyunxiao:~/Desktop$ date '+%Y-%m-%d %H:%M:%S %s'
2026-08-30 11:20:01 1788060001
```

修改**Linux** 时区：使用**root**权限，执行以下命令，修改时区为东八区

```bash
rm -r /etc/localtime
sudo ln -s /usr/hare/zoneinfo/Asia/Shanghai /etc/localtime
```

```bash
date
date '+%Y-%m-%d %H:%M:%S'
```

---

## 24. 主机名

可以通过命令：**ifconfig** 来查看ip地址：

```bash
mengyunxiao@mengyunxiao:~/Desktop$ ifconfig
enp0s3: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 192.168.110.84  netmask 255.255.255.0  broadcast 192.168.110.255
        inet6 fe80::a00:27ff:fe99:b8f1  prefixlen 64  scopeid 0x20<link>
        ether 08:00:27:99:b8:f1  txqueuelen 1000  (Ethernet)
        RX packets 1492  bytes 349177 (349.1 KB)
        RX errors 0  dropped 212  overruns 0  frame 0
        TX packets 412  bytes 57613 (57.6 KB)
        TX errors 0  dropped 0  overruns 0  carrier 0  collisions 0

lo: flags=73<UP,LOOPBACK,RUNNING>  mtu 65536
        inet 127.0.0.1  netmask 255.0.0.0
        inet6 ::1  prefixlen 128  scopeid 0x10<host>
        loop  txqueuelen 1000  (Local Loopback)
        RX packets 409  bytes 32505 (32.5 KB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 409  bytes 32505 (32.5 KB)
        TX errors 0  dropped 0  overruns 0  carrier 0  collisions 0
```

从以上指令得出的内容分析得：

```bash
- ifconfig命令用于查看网络接口信息

- 共有两个网络接口：enp0s3（有线网卡）和lo（本地回环）

- enp0s3接口信息：

  - 状态：UP（已启动），BROADCAST（支持广播），RUNNING（正在运行），MULTICAST（支持组播）
  - 网卡名称：enp0s3
  - 当前IPv4地址：192.168.110.84
  - 子网掩码：255.255.255.0
  - 广播地址：192.168.110.255
  - MAC地址：08:00:27:99:b8:f1
  - 接收数据包：1492个，共349177字节（349.1 KB）
  - 接收丢包：212个（可能存在网络问题）
  - 发送数据包：412个，共57613字节（57.6 KB）

- lo回环接口信息：

  - 状态：UP（已启动），LOOPBACK（回环），RUNNING（正在运行）
  - IPv4地址：127.0.0.1（本机地址）
  - 子网掩码：255.0.0.0
  - 接收和发送数据包：409个，共32505字节（32.5 KB）

- 总结：当前主机IP为192.168.110.84，lo回环地址为127.0.0.1，enp0s3存在212个接收丢包
```

另外，我们还有以下操作：

* 可以使用命令：**hostname**查看主机名
* 可以使用命令：**hostnamectl set-hostname 主机名**，可以修改主机名（需要**root**权限）
* 重新登录**FinalShell** 可以看自己的主机名是否已经正确显示

```bash
hostname
hostnamectl set-hostname server01
```

---

## 25. 网络命令

当前我们虚拟机的**Linux**操作系统，它的**IP**地址是通过**DHCP**服务获取的，而**DHCP** 则为动态获取**IP**,即每次重启后都会获取一次，可能导致**IP**地址频繁变化。

### 查看IP
```bash
ip addr
ip route
```

### 测试连通性

$ping$ 命令：检查指定的网络服务器是否是可连接状态，语法：

```bash
- 可以通过ping命令，检查指定的网络服务器是否是可联通状态
- 语法：ping [-c num] ip或主机名
  - 选项：-c，检查的次数，不使用-c选项，将无限次数持续检查
  - 参数：ip或主机名，被检查的服务器的ip地址或主机名地址
```

例如：

```bash
ping 192.168.56.101
ping -c 4 192.168.56.101
```

### 下载文件

$wget$ 是非交互式的文件下载器，可以在命令行内的下载网络文件，语法：**wget [-b] url **

* 选项：**-b**，可选，后台下载，会将日志写入当前工作目录的**wget-log**文件
* 参数：**url**,下载链接

```bash
wget URL
curl -O URL
```

$curl$ 命令：可以发送**http** 网络请求，可用于：下载文件，获取信息，语法：**curl [-O]  url**

* 选项：**-O**，用于下载文件，当**url**是可下载的链接时，可以使用此项保存文件
* 参数：**url**,要发起请求的网络地址

---

## 26. 端口查看

通过端口可以锁定计算机上具体的程序保证程序之间的沟通，**Linux** 系统是一个超大号的小区，可以支持**65535**个端口，可以分为三类使用：

* 公认端口：1~1023，通常用于一些系统内置或知名程序的预留使用，如：**SSH**服务的22端口，HTTPS服务的443端口，非特殊需要，不要占这个范围的端口。


* 注册端口：1024~49151，通常可以随便使用，用于松散的绑定一些程序\服务
* 动态端口：49152~65535，通常不会固定绑定程序，而是对程序对外进行网络连接超时，可以临时使用

$nmap$ 命令：**nmap 被查看的地址**

 发行版原生不自带：需要**sudo apt install nmap -y** 先安装

$netstat$ 命令：语法：**netstat -anp | grep 端口号**，安装：**sudo apt install net-tools -y**，可以查看指定端口的占用情况

```bash
ss -lntp 或者ss -anp | grep (ubuntu自带）# 最推荐
```

---

## 27. 进程管理

### ps

$ps$ 命令，语法：**ps [-e -f]** 

* 选项：**-e**，显示出全部进程
* 选项：**-f**，以完全格式化的形式展示信息
* 一般来说：固定用法就是：**ps -ef**列出全部进程信息
* 而如果是**ps -ef | grep 过滤机制** ，就可以过滤出想要的进程

```bash
ps -ef
ps aux
```

### kill

$kill$ 命令：关闭进程，语法:**kill [-9] 进程ID** 

* 选项：$-9$，表示强制关闭进程，不使用此选项会向进程发送信号要求其关闭，但是否真的关闭看它的处理机制

```bash
kill PID
kill -9 PID
```

---

## 28. 资源监控

$top$ 命令：查看**cpu** ,内存使用情况，默认每5秒刷新一次，语法：直接**top**，按**q**或者**ctrl + c** 退出

```bash
top - 15:56:18 up  1:12,  1 user,   load average: 0.11, 0.03, 0.01
Tasks: 310 total,   1 running, 309 sleeping,   0 stopped,   0 zombie
%Cpu(s):   1.4 us,  2.0 sy,  0.0 ni,  96.6 id,  0.0 wa,  0.0 hi,  0.0 si,  0.0 st
MiB Mem :   3350.6 total,   208.1 free,   1456.9 used,   1946.5 buff/cache
MiB Swap:   3372.0 total,   3372.0 free,     0.0 used.   1893.8 avail Mem

    PID USER      PR  NI    VIRT    RES    SHR S  %CPU  %MEM     TIME+ COMMAND
   4922 mengyunx+  20   0 3825788 252328 143868 S   5.3   7.4   0:56.08 gnome-shell
   4922 mengyunx+  20   0 1615356 239152 117736 S   2.7   7.0   0:11.31 pttyx
    496 root      -51   0       0      0      0 S   0.3   0.0   0:00.54 irq/16-vmwgfx
    951 root       20   0  116644  11028   9356 S   0.3   0.3   0:09.08 vmtoold
      1 root       20   0   26596  17584  11804 S   0.0   0.5   0:06.98 systemd
      2 root       20   0       0      0      0 S   0.0   0.0   0:00.04 kthreadd
      3 root       20   0       0      0      0 S   0.0   0.0   0:00.00 pool_workqueue_release
      4 root        0 -20       0      0      0 S   0.0   0.0   0:00.00 kworker/R-rcu_gp
      5 root        0 -20       0      0      0 S   0.0   0.0   0:00.00 kworker/R-sync_wq
      6 root        0 -20       0      0      0 S   0.0   0.0   0:00.00 kworker/R-kvfreq_rcu_reclaim
      7 root        0 -20       0      0      0 S   0.0   0.0   0:00.00 kworker/R-slub_flushwq
      8 root        0 -20       0      0      0 S   0.0   0.0   0:00.00 kworker/R-netns
     10 root        0 -20       0      0      0 S   0.0   0.0   0:00.13 kworker/0:0H-kblockd
     12 root        0 -20       0      0      0 S   0.0   0.0   0:00.00 kworker/u512:0-ipv6_addrconf
     13 root        0 -20       0      0      0 S   0.0   0.0   0:00.00 kworker/R-mm_percpu_wq
     14 root       20   0       0      0      0 S   0.0   0.0   0:00.04 ksoftirqd/0
     15 root       20   0       0      0      0 S   0.0   0.0   0:00.73 rcu_preempt
     16 root       20   0       0      0      0 S   0.0   0.0   0:00.01 rcu_exp_par_gp_kthread_worker/1
     17 root       20   0       0      0      0 S   0.0   0.0   0:00.01 rcu_exp_gp_kthread_worker
     18 root       20   0       0      0      0 S   0.0   0.0   0:00.07 migration/0
     19 root       20   0       0      0      0 S   0.0   0.0   0:00.00 kprobe-optimizer
     20 root       20   0       0      0      0 S   0.0   0.0   0:00.00 idle_inject/0
     21 root       20   0       0      0      0 S   0.0   0.0   0:00.00 cpu/hp/0
     22 root       20   0       0      0      0 S   0.0   0.0   0:00.00 cpu/hp/1
     23 root       20   0       0      0      0 S   0.0   0.0   0:00.00 idle_inject/1
     24 root       20   0       0      0      0 S   0.0   0.0   0:00.00 migration/1
     25 root       20   0       0      0      0 S   0.0   0.0   0:00.11 ksoftirqd/1
     27 root        0 -20       0      0      0 S   0.0   0.0   0:00.00 kworker/1:0H-kblockd
     30 root       20   0       0      0      0 S   0.0   0.0   0:00.00 kdevtmpfs
     31 root        0 -20       0      0      0 S   0.0   0.0   0:00.00 kworker/R-inet_frag_wq
     32 root       20   0       0      0      0 S   0.0   0.0   0:00.00 rcu_tasks_kthread
     33 root       20   0       0      0      0 S   0.0   0.0   0:00.00 rcu_tasks_rdue_kthread
     34 root       20   0       0      0      0 S   0.0   0.0   0:00.00 kauditd
     35 root       20   0       0      0      0 S   0.0   0.0   0:00.01 khungtaskd
     36 root       20   0       0      0      0 S   0.0   0.0   0:00.00 oom_reaper
     37 root       20   0       0      0      0 S   0.0   0.0   0:01.03 kworker/u513:1-flush-8:0
```

```bash
top - 15:56:18 up  1:12,  1 user,   load average: 0.11, 0.03, 0.01
Tasks: 310 total,   1 running, 309 sleeping,   0 stopped,   0 zombie
%Cpu(s):   1.4 us,  2.0 sy,  0.0 ni,  96.6 id,  0.0 wa,  0.0 hi,  0.0 si,  0.0 st
MiB Mem :   3350.6 total,   208.1 free,   1456.9 used,   1946.5 buff/cache
MiB Swap:   3372.0 total,   3372.0 free,     0.0 used.   1893.8 avail Mem
```

我们对顶部的内容分析可得：

- 第一行：`top - 15:56:18 up 1:12, 1 user, load average: 0.11, 0.03, 0.01`
  * top：命令名称
  * 15:56:18：当前系统时间
  * up 1:12：系统已运行1小时12分钟
  * 1 user：当前有1个用户登录
  * load average: 0.11, 0.03, 0.01：系统1分钟、5分钟、15分钟的平均负载

- 第二行：`Tasks: 310 total, 1 running, 309 sleeping, 0 stopped, 0 zombie`
  * Tasks: 310 total：共310个进程
  * 1 running：1个进程正在运行
  * 309 sleeping：309个进程处于休眠状态
  * 0 stopped：0个停止进程
  * 0 zombie：0个僵尸进程

- 第三行：`%Cpu(s): 1.4 us, 2.0 sy, 0.0 ni, 96.6 id, 0.0 wa, 0.0 hi, 0.0 si, 0.0 st`
  * %Cpu(s)：CPU使用率
  * us：用户空间CPU占用率 1.4%
  * sy：系统内核CPU占用率 2.0%
  * ni：高优先级进程占用CPU时间百分比 0.0%
  * id：空闲CPU率 96.6%
  * wa：IO等待CPU占用率 0.0%
  * hi：CPU硬件中断率 0.0%
  * si：CPU软件中断率 0.0%
  * st：强制等待占用CPU率 0.0%

- 第四行：`MiB Mem : 3350.6 total, 208.1 free, 1456.9 used, 1946.5 buff/cache`
  * MiB Mem：物理内存
  * total：总量 3350.6 MB
  * free：空闲 208.1 MB
  * used：已使用 1456.9 MB
  * buff/cache：缓冲和缓存占用 1946.5 MB

- 第五行：`MiB Swap: 3372.0 total, 3372.0 free, 0.0 used, 1893.8 avail Mem`
  * MiB Swap：虚拟内存（交换空间）
  * total：总量 3372.0 MB
  * free：空闲 3372.0 MB
  * used：已使用 0.0 MB
  * avail Mem：可用物理内存 1893.8 MB

$top$ 命令也支持选项：
| 选项 | 功能                                                         |
| :--- | :----------------------------------------------------------- |
| `-p` | 只显示某个进程的信息                                         |
| `-d` | 设置刷新时间，默认是 5s                                      |
| `-c` | 显示产生进程的完整命令，默认是进程名                         |
| `-n` | 指定刷新次数，比如 `top -n 3`，刷新输出 3 次后退出           |
| `-b` | 以非交互非全屏模式运行，以批次的方式执行 top，一般配合 `-n` 指定输出几次统计信息，将输出重定向到指定文件，比如 `top -b -n 3 > /tmp/top.tmp` |
| `-i` | 不显示任何闲置（idle）或无用的（zombie）的进程               |
| `-u` | 查找特定用户启动的进程                                       |

当$top$ 以交互式运行（非**-b**选项运行），可以用一下交互式命令：

| 按键 | 功能                                                         |
| :--- | :----------------------------------------------------------- |
| `h`  | 按下 `h` 键，会显示帮助画面                                  |
| `c`  | 按下 `c` 键，会显示产生进程的完整命令，等同于 `-c` 参数，再次按下 `c` 键，变为默认显示 |
| `f`  | 按下 `f` 键，可以选择需要展示的项目                          |
| `M`  | 按下 `M` 键，根据驻留内存大小（RES）排序                     |
| `P`  | 按下 `P` 键，根据 CPU 使用百分比大小进行排序                 |
| `T`  | 按下 `T` 键，根据时间/累计时间进行排序                       |
| `E`  | 按下 `E` 键，切换顶部内存显示单位                            |
| `e`  | 按下 `e` 键，切换进程内存显示单位                            |
| `1`  | 按下 `1` 键，切换显示平均负载和启动时间信息                  |
| `i`  | 按下 `i` 键，不显示闲置或无用的进程，等同于 `-i` 参数，再次按下，变为默认显示 |
| `t`  | 按下 `t` 键，切换显示 CPU 状态信息                           |
| `m`  | 按下 `m` 键，切换显示内存信息                                |

```bash
top
df -h
du -sh /opt
```

---

$df$ 命令：可以看硬盘的使用情况，语法：**df [-h] **

* 选项：**-h**，可以更加人性化的单位显示

$iostat$ 命令：可以查看**CPU**，磁盘的相关信息，语法：

```bash
- 语法：iostat [-x] [num1] [num2]

  - 选项：-x，显示更多信息
  - num1：数字，刷新间隔（秒）
  - num2：数字，刷新次数
```

使用**iostat** 的**-x**选项，可以显示更多信息：

| 列名       | 含义                                                         |
| :--------- | :----------------------------------------------------------- |
| `rrqm/s`   | 每秒合并的读请求数量（当系统发现不同的读请求读取的是同一数据块时，会将它们合并为一个请求，以提高 I/O 利用率，避免重复调用） |
| `wrqm/s`   | 每秒合并的写请求数量                                         |
| `r/s`      | 每秒发送到设备的读请求数量                                   |
| `w/s`      | 每秒发送到设备的写请求数量                                   |
| `rkB/s`    | 每秒从设备读取的数据量（单位：KB）                           |
| `wkB/s`    | 每秒写入设备的数据量（单位：KB）                             |
| `avgrq-sz` | 平均每个 I/O 请求的大小（单位：扇区数）                      |
| `avgqu-sz` | 平均 I/O 请求队列长度（队列越短越好，值越大说明系统 I/O 压力越大，有大量请求在等待处理） |
| `await`    | 每个 I/O 请求的平均处理时间（单位：毫秒，包括排队时间和实际服务时间） |
| `r_await`  | 每个读请求的平均处理时间（单位：毫秒）                       |
| `w_await`  | 每个写请求的平均处理时间（单位：毫秒）                       |
| `svctm`    | 每个 I/O 请求的平均服务时间（单位：毫秒，不包含排队时间，仅指设备实际处理请求的时间） |
| `%util`    | 磁盘利用率（百分比），表示磁盘有多繁忙，接近 100% 说明磁盘已达到性能瓶颈 |

网络的相关监控：

* 可以使用**sar** 命令查看网络的相关统计（**sar**命令极其复杂，这里仅是简单用于统计网络）
* 语法：**sar -n DEV num1 num2 **
* 选项：$-n$ ,查看网络，**DEV** 表示网络接口
* num1(刷新间隔，不填就查看一次就结束)，num2(查看次数，不填无限次数)
* 监控系统的不同方面：


| 监控类型   | 选项     | 说明                                                         |
| :--------- | :------- | :----------------------------------------------------------- |
| CPU 使用率 | `-u`     | 显示 CPU 的整体使用情况，包括用户态、系统态、I/O 等待和空闲时间 |
| 指定 CPU   | `-P`     | 查看特定 CPU 核心的统计数据，例如 `sar -P 0` 查看第一个核心  |
| 内存使用   | `-r`     | 报告物理内存和交换分区的使用情况                             |
| 交换分区   | `-S`     | 专门查看交换分区的使用率                                     |
| 磁盘 I/O   | `-b`     | 报告磁盘 I/O 和传输速率的相关统计信息（如 tps）              |
| 块设备     | `-d`     | 报告每个块设备（如硬盘）的活动情况，类似 `iostat -x`         |
| 网络       | `-n DEV` | 报告网络设备的统计信息，如每秒收发的数据包和字节数           |
| 系统负载   | `-q`     | 查看系统负载和队列长度                                       |

## 29. 环境变量

$ env$ 命令：可以查看当前系统中记录的环境变量，环境变量是一种**key-value**型结构，即名称和值：

```bash
mengyunxiao@mengyunxiao-VMware-Virtual-Platform:~/Desktop$ env
SHELL=/bin/bash
QT_ACCESSIBILITY=1
COLORTERM=truecolor
XDG_CONFIG_DIRS=/etc/xdg/xdg-ubuntu:/etc/xdg
XDG_MENU_PREFIX=gnome-
GNOME_DESKTOP_SESSION_ID=this-is-deprecated
QT_IM_MODULES=wayland;ibus
PTYXIS_PROFILE=67ee69e704ef0a97a58ae4096a94d98d
SSH_AUTH_SOCK=/run/user/1000/gcr/ssh
MEMORY_PRESSURE_WRITE=c29tZSAyMDAwMDAgMjAwMDAwMAA=
XMODIFIERS=@im=ibus
DESKTOP_SESSION=ubuntu
GTK_MODULES=gail:atk-bridge
PWD=/home/mengyunxiao/Desktop
XDG_SESSION_DESKTOP=ubuntu
LOGNAME=mengyunxiao
XDG_SESSION_TYPE=wayland
GPG_AGENT_INFO=/run/user/1000/gnupg/S.gpg-agent:0:1
SYSTEMD_EXEC_PID=3055
XAUTHORITY=/run/user/1000/.mutter-Xwaylandauth.5YX2U3
GJS_DEBUG_TOPICS=JS ERROR;JS LOG
HOME=/home/mengyunxiao
USERNAME=mengyunxiao
LANG=en_US.UTF-8
LS_COLORS=rs=0:di=01;34:ln=01;36:mh=00:pi=40;33:so=01;35:do=01;35:bd=40;33;01:cd=40;33;01:or=40;31;01:mi=00:su=37;41:sg=30;43:ca=00:tw=30;42:ow=34;42:st=37;44:ex=01;32:*.tar=01;31:*.tgz=01;31:*.arc=01;31:*.arj=01;31:*.taz=01;31:*.lha=01;31:*.lz4=01;31:*.lzh=01;31:*.lzma=01;31:*.tlz=01;31:*.txz=01;31:*.tzo=01;31:*.t7z=01;31:*.zip=01;31:*.z=01;31:*.dz=01;31:*.gz=01;31:*.lrz=01;31:*.lz=01;31:*.lzo=01;31:*.xz=01;31:*.zst=01;31:*.tzst=01;31:*.bz2=01;31:*.bz=01;31:*.tbz=01;31:*.tbz2=01;31:*.tz=01;31:*.deb=01;31:*.rpm=01;31:*.jar=01;31:*.war=01;31:*.ear=01;31:*.sar=01;31:*.rar=01;31:*.alz=01;31:*.ace=01;31:*.zoo=01;31:*.cpio=01;31:*.7z=01;31:*.rz=01;31:*.cab=01;31:*.wim=01;31:*.swm=01;31:*.dwm=01;31:*.esd=01;31:*.avif=01;35:*.jpg=01;35:*.jpeg=01;35:*.mjpg=01;35:*.mjpeg=01;35:*.gif=01;35:*.bmp=01;35:*.pbm=01;35:*.pgm=01;35:*.ppm=01;35:*.tga=01;35:*.xbm=01;35:*.xpm=01;35:*.tif=01;35:*.tiff=01;35:*.png=01;35:*.svg=01;35:*.svgz=01;35:*.mng=01;35:*.pcx=01;35:*.mov=01;35:*.mpg=01;35:*.mpeg=01;35:*.m2v=01;35:*.mkv=01;35:*.webm=01;35:*.webp=01;35:*.ogm=01;35:*.mp4=01;35:*.m4v=01;35:*.mp4v=01;35:*.vob=01;35:*.qt=01;35:*.nuv=01;35:*.wmv=01;35:*.asf=01;35:*.rm=01;35:*.rmvb=01;35:*.flc=01;35:*.avi=01;35:*.fli=01;35:*.flv=01;35:*.gl=01;35:*.dl=01;35:*.xcf=01;35:*.xwd=01;35:*.yuv=01;35:*.cgm=01;35:*.emf=01;35:*.ogv=01;35:*.ogx=01;35:*.aac=00;36:*.au=00;36:*.flac=00;36:*.m4a=00;36:*.mid=00;36:*.midi=00;36:*.mka=00;36:*.mp3=00;36:*.mpc=00;36:*.ogg=00;36:*.ra=00;36:*.wav=00;36:*.oga=00;36:*.opus=00;36:*.spx=00;36:*.xspf=00;36:*~=00;90:*#=00;90:*.bak=00;90:*.old=00;90:*.orig=00;90:*.part=00;90:*.rej=00;90:*.swp=00;90:*.tmp=00;90:*.dpkg-dist=00;90:*.dpkg-old=00;90:*.ucf-dist=00;90:*.ucf-new=00;90:*.ucf-old=00;90:*.rpmnew=00;90:*.rpmorig=00;90:*.rpmsave=00;90:
XDG_CURRENT_DESKTOP=ubuntu:GNOME
MEMORY_PRESSURE_WATCH=/sys/fs/cgroup/user.slice/user-1000.slice/user@1000.service/session.slice/org.gnome.Shell@ubuntu.service/memory.pressure
VTE_VERSION=8400
WAYLAND_DISPLAY=wayland-0
INVOCATION_ID=b1574362d6744b3ba92fc445da917142
MANAGERPID=2689
GJS_DEBUG_OUTPUT=stderr
GNOME_SETUP_DISPLAY=unix:/tmp/.X11-unix/X1
LESSCLOSE=/usr/bin/lesspipe %s %s
XDG_SESSION_CLASS=user
TERM=xterm-256color
LESSOPEN=| /usr/bin/lesspipe %s
USER=mengyunxiao
DISPLAY=:0
SHLVL=1
QT_IM_MODULE=ibus
MANAGERPIDFDID=5296
XDG_RUNTIME_DIR=/run/user/1000
DEBUGINFOD_URLS=https://debuginfod.ubuntu.com 
IM_CONFIG_ENTRY=profile
JOURNAL_STREAM=10:38074
XDG_DATA_DIRS=/usr/share/ubuntu:/usr/share/gnome:/usr/local/share/:/usr/share/:/var/lib/snapd/desktop
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin:/snap/bin
GDMSESSION=ubuntu
XDG_SESSION_EXTRA_DEVICE_ACCESS=render:accel
DBUS_SESSION_BUS_ADDRESS=unix:path=/run/user/1000/bus
PTYXIS_VERSION=50.1
FLATPAK_TTY_PROGRESS=1
_=/usr/bin/env
```

以上说明：
**1. 用户与会话信息**
*   `USER=mengyunxiao`：当前登录的用户名。
*   `HOME=/home/mengyunxiao`：当前用户的主目录。
*   `PWD=/home/mengyunxiao/Desktop`：你执行 `env` 命令时所在的目录，即“桌面”。
*   `SHELL=/bin/bash`：当前使用的命令行解释器是 `bash`。
*   `LOGNAME=mengyunxiao`：登录用户名，与 `USER` 一致。
*   `LANG=en_US.UTF-8`：系统语言和字符编码为美式英语和 UTF-8，这代表系统界面和错误消息是英文的，同时也能正常显示中文文件名。

**2. 图形界面与显示**
*   `XDG_SESSION_TYPE=wayland`：当前图形显示服务器协议是 **Wayland**（新一代），而不是传统的 X11。这意味着 Ubuntu 虚拟机用的是 Wayland 显示服务器。
*   `DISPLAY=:0`：X11 显示服务器的编号，表明当前环境兼容 X11 应用。
*   `WAYLAND_DISPLAY=wayland-0`：Wayland 显示服务器的具体标识。
*   `DESKTOP_SESSION=ubuntu`：当前使用的桌面环境是 Ubuntu 默认的 GNOME。
*   `QT_IM_MODULES=wayland;ibus` 和 `XMODIFIERS=@im=ibus`：指定了输入法框架为 `ibus`（智能输入法总线），支持中文等复杂输入。

**3. 系统与程序路径**
*   `PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin`：这是**命令搜索路径**。当你在终端输入一个命令时，系统会按顺序在这些目录中查找对应的可执行文件。这个路径包含了系统程序、用户程序和 Snap 安装的程序。
*   `SSH_AUTH_SOCK=/run/user/1000/gcr/ssh`：SSH 密钥代理的套接字地址，用于安全地管理 SSH 密钥，避免每次连接都输入密码。
*   `DBUS_SESSION_BUS_ADDRESS=...`：D-Bus 会话总线地址，这是桌面环境里各个程序之间通信用的“消息总线”。

**4. 终端与交互**
*   `TERM=xterm-256color`：终端类型，表明终端支持 256 色，这会影响命令行的颜色显示效果。
*   `LS_COLORS=...`：这是一个很长的配置，它定义了 `ls` 命令输出不同文件类型时的颜色，例如：目录显示蓝色、可执行文件显示绿色等，让终端输出更易读。
*   `VTE_VERSION=8400`：说明使用的终端模拟器（VTE 库）的版本号。


**$** 符号：被用于取"变量"的值，取得环境变量的值就可以通过语法：**$**环境变量名 来获得:

| 命令 | 输出 |
| :--- | :--- |
| `echo $PATH` | `/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin:/snap/bin` |
| `echo ${PATH}ABC` | `/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin:/snap/binABC` |

**说明：**

- **`echo $PATH`**：标准的变量引用。Shell 将 `$PATH` 替换为其存储的路径字符串，然后 `echo` 命令打印出这个字符串。
- **`echo ${PATH}ABC`**：使用花括号界定变量名。`${PATH}` 明确告诉 Shell 变量名是 `PATH`，后面的 `ABC` 是普通文本，直接拼接在变量值之后。输出中看不到任何空格或分隔符，因为 `ABC` 被紧贴在了路径字符串的末尾。

临时变量设置，语法：$export$ 变量名=变量值

* 永久生效
  * 针对当前用户生效，配置在当前用户的：**~/bashrc**文件中
  * 针对所有用户生效，配置在系统的：**/etc/profile** 文件中
  * 并通过语法：**source**配置文件，进行立刻生效，或者重新登录**Finalshell**生效

```bash
env
echo $PATH
echo ${PATH}ABC
export JAVA_HOME=...
source ~/.bashrc
```

---

## 30. 文件上传下载

- 我们可以通过FinalShell工具，方便的和虚拟机进行数据交换。

- 在FinalShell软件的下方窗体中，提供了Linux的文件系统视图，可以方便的：

  - 浏览文件系统，找到合适的文件，右键点击下载，即可传输到本地电脑

  - 浏览文件系统，找到合适的目录，将本地电脑的文件拖拽进入，即可方便的上传数据到Linux中

$rz$ 命令，进行上传，语法：直接输入$rz$ 即可安装

$sz$ 命令，进行下载，语法：$sz$ 要下载的文件

以上两个命令都需要安装：

```
# Ubuntu/Debian
sudo apt update
sudo apt install lrzsz -y

# CentOS/RHEL
yum install lrzsz -y
```

```bash
rz     # 上传
sz 文件名   # 下载
```

---

## 31. 压缩与解压

市面上有非常多的压缩格式：

| 压缩格式 | 主要使用平台          | 说明                                                |
| :------- | :-------------------- | :-------------------------------------------------- |
| **zip**  | Linux、Windows、macOS | 最常见，跨平台兼容性最好，Windows 和 Linux 原生支持 |
| **7zip** | Windows 常用          | 压缩率高，但 Linux 下需额外安装 p7zip               |
| **tar**  | Linux、macOS 常用     | 仅打包不压缩，通常与 gzip 结合使用（`.tar.gz`）     |
| **gzip** | Linux、macOS 常用     | 单文件压缩，通常配合 tar 使用（`.tar.gz` / `.tgz`） |

**.tar** ，为归档文件，就是简单的将文件组装到一个**.tar**的文件内，没有太多的大小压缩，只是简单的封装

**.gz**  ,也常见为**.tar.gz**,**gzip**格式的压缩文件，即利用**gzip**压缩算法将文件压缩到另一个文件内，可以极大的减少压缩后的体积

### tar

$tar$ 命令：进行压缩和解压的操作，语法：**tar [-c -v -x -f -z -C] 参数1 参数2 ... 参数N **

* $-c$ ，创建解压文件，用于压缩格式
* $-v$ , 显示压缩，解压过程，用于查看进度
* $-x$ ,解压模式
* $-f$ ,要创建的文件，或者要解压的文件，$-f$ 选项必须在所有选项中的位置处于最后一个
* $-z$ ,**gzip** 模式，不使用就是普通的**tarball** 格式
* $-C$ ,选择解压的目的地，用于解压模式

```bash
tar -zcvf test.tar.gz 文件/文件夹
tar -zxvf test.tar.gz
```

### zip

$zip$ 命令：压缩文件为**zip**压缩包，语法：**zip [-r] 参数1 参数2 参数3 ... 参数N **

* $-r$ ，被压缩的文件包含文件夹的时候，需要使用$-r$ 选项，和**rm**,**cp**的$-r$ 效果一致

```bash
zip -r test.zip test/
unzip test.zip
```

---

###  unzip

使用$unzip$ 命令，可以方便的解压**zip** 压缩包，语法：**unzip [-d] 参数 **

* $-d$ ,指定要解压的去的位置，和**tar**的$-C$ 选项一样
* 参数，被解压的**zip**压缩文件

## 32. MySQL部署

```bash
dnf install -y mysql-community-server --nogpgcheck
systemctl enable --now mysqld
mysql -uroot -p
```

修改密码：
```sql
ALTER USER 'root'@'localhost' IDENTIFIED BY '新密码';
```

远程连接：
```sql
CREATE USER 'root'@'%' IDENTIFIED BY '密码';
GRANT ALL PRIVILEGES ON *.* TO 'root'@'%';
```

防火墙：
```bash
firewall-cmd --add-port=3306/tcp --permanent
firewall-cmd --reload
```

---

## 33. 忘记MySQL密码恢复
```bash
systemctl stop mysqld
systemctl set-environment MYSQLD_OPTS="--skip-grant-tables --skip-networking"
systemctl start mysqld
mysql -uroot
FLUSH PRIVILEGES;
ALTER USER 'root'@'localhost' IDENTIFIED BY '新密码';
systemctl unset-environment MYSQLD_OPTS
systemctl restart mysqld
```

---

## 34. 常见命令速查表

| 分类 | 命令          | 作用                 |
|------|--------------|---------------------|
| 目录 | pwd / ls / cd / mkdir / touch / cp / mv / rm | 文件操作 |
| 文本 | cat / less / head / tail / grep / wc | 文本处理 |
| 权限 | chmod / chown | 权限管理 |
| 用户 | useradd / passwd / su / sudo | 用户管理 |
| 进程 | ps / top / kill | 进程监控 |
| 服务 | systemctl | 服务管理 |
| 磁盘 | df / du | 磁盘空间 |
| 网络 | ip / ping / ss / curl / wget | 网络命令 |
| 软件 | dnf / apt | 软件包管理 |
| 压缩 | tar / zip | 压缩 |

---

## 35. 最重要的是Linux命令

```text
pwd ls cd mkdir touch cp mv rm cat less head tail find grep chmod chown ps top kill df du ip ping ss curl wget systemctl dnf apt tar vim ssh
```

---

## 36. Linux命令语法详细说明

### 命令结构
```bash
command [-选项] [-参数]
```

### 短选项
```bash
ls -l -a -h
ls -lah
```

### 长选项
```bash
ls --help
ls --version
```

---

## 37. 特殊符号详解

| 符号 | 含义               |
|------|-------------------|
| `/`  | 绝对路径起点        |
| `.`  | 当前目录           |
| `..` | 上一级目录         |
| `~`  | 当前用户家目录     |
| `*`  | 任意长度字符       |
| `?`  | 一个字符           |
| `[]` | 字符集合           |
| `|`  | 管道               |
| `>`  | 覆盖写入           |
| `>>` | 追加写入           |
| `&`  | 后台运行           |
| `&&` | 前一条成功才执行   |
| `||` | 前一条失败才执行   |

---

## 38. man帮助系统

$man$ 命令：

```bash
作用：查看Linux系统内置手册，获取命令的详细帮助信息
- 语法：man [选项] [命令名]
  - 选项：-f，查看命令在哪些章节有手册
  - 选项：-k，搜索包含关键词的手册条目
  - 参数：命令名，要查看帮助的命令名称
```

```bash
man ls
```
常用操作：Space / Enter / /搜索 / q退出

---

## 39. echo和printf命令的对比和区别

对于$echo$ 命令：

```bash
- 作用：在终端输出文本或变量的值
- 语法：echo [选项] [字符串或变量]
  - 选项：-n，不换行输出
  - 选项：-e，启用转义字符解释（如\n换行、\t制表符）
  - 参数：字符串或变量，要输出的内容
```

对于$printf$ 命令：

```bash
- 作用：格式化输出文本，类似C语言的printf函数，输出更规范可控
- 语法：printf [格式字符串] [参数...]
  - 参数：格式字符串，定义输出格式（如%s、%d、%f）
  - 参数：要填充的数据
```

主要区别在于：

- 换行方式：
  - $echo$默认自动换行（可用$-n$取消换行）
  - $printf$默认不换行，需要显式添加`\n`

```
echo "hello"          # 输出后自动换行
printf "hello"        # 输出后不换行
printf "hello\n"      # 需要加\n才换行
```

- 输出格式化：
  - $echo$输出较为简单，适合普通输出
  - $printf$支持格式化占位符，输出结构化的数据

| 格式符  | 含义                   |
| ------- | ---------------------- |
| `%s`    | 字符串                 |
| `%d`    | 整数                   |
| `%f`    | 浮点数                 |
| `%x`    | 十六进制               |
| `%o`    | 八进制                 |
| `%-10s` | 左对齐，占10个字符宽度 |
| `%10s`  | 右对齐，占10个字符宽度 |

```
printf "%d + %d = %d\n" 1 2 3
printf "%s的年龄是%d岁\n" "小明" 25
```

- 转义字符：
  - $echo$需加$-e$选项才支持转义字符
  - $printf$直接支持转义字符

| 转义字符 | 含义       |
| -------- | ---------- |
| `\n`     | 换行       |
| `\t`     | 水平制表符 |
| `\r`     | 回车       |
| `\\`     | 反斜杠     |
| `\b`     | 退格       |
| `\v`     | 垂直制表符 |

```
echo -e "第一行\n第二行\t制表符"
printf "第一行\n第二行\t制表符\n"
```

- 执行效率：
  - $printf$略优于echo，尤其在循环中多次输出时
- 可移植性：
  - $printf$在不同shell中行为更一致
  - $echo$在不同shell中行为存在差异
- 使用建议：
  - 简单输出：使用$echo$
  - 格式化输出：使用$printf$

```bash
echo hello
echo -e "hello\nworld"
printf "Hello %s\n" "$name"
```

---

## 40. 文件权限详细理解
- 文件类型 + 9位权限
- 数字方式：755（rwxr-xr-x）
- 符号方式：`chmod u+x` / `chmod g-w`

---

## 41. 常见参数速记

| 参数 | 含义               |
|------|-------------------|
| `-a` | all / 全部        |
| `-l` | long / 详细       |
| `-h` | human-readable    |
| `-r` | recursive / 递归   |
| `-f` | force / 强制      |
| `-i` | ignore case       |
| `-n` | number            |
| `-v` | verbose           |
| `-z` | gzip              |
| `-C` | 指定目录           |

---

## 42. Linux服务器排错万能流程

```text
问题出现 → 查看日志 → systemctl status → ps / top → ss 端口 → df/du → 权限 → 网络 → 防火墙
```

---

## 43. Ubuntu与Debian

### 43.1 安装与更新
```bash
sudo apt update
sudo apt upgrade
sudo apt install nginx
sudo apt remove nginx
sudo apt purge nginx
```

### 43.2 软件包管理
```bash
apt search nginx
apt show nginx
```
## 44. Shell脚本

- Shell脚本本质上是将多个Linux命令按顺序写入一个文本文件中，一次性批量执行

### 创建和执行脚本

- 创建脚本文件

```bash
touch test.sh
vim test.sh
```

- 脚本内容

```bash
#!/bin/bash
echo "Hello World"
```

- 执行方式

| 方式       | 命令             | 说明                    |
| ---------- | ---------------- | ----------------------- |
| 直接执行   | `./test.sh`      | 需先 `chmod +x test.sh` |
| 解释器执行 | `bash test.sh`   | 无需执行权限            |
| source执行 | `source test.sh` | 在当前shell执行         |

### 变量

- 定义和使用

```bash
name="张三"
echo $name
echo ${name}      # 推荐加花括号
```

- 常用环境变量

| 变量    | 含义                    |
| ------- | ----------------------- |
| `$PATH` | 命令搜索路径            |
| `$HOME` | 用户家目录              |
| `$0`    | 脚本名                  |
| `$1-$9` | 第1-9个参数             |
| `$#`    | 参数个数                |
| `$?`    | 上条命令状态码（0成功） |

```bash
echo "脚本名：$0"
echo "第一个参数：$1"
echo "参数个数：$#"
```

### 算术运算

```bash
echo $((10 + 20))
echo $((10 * 20))
```

### 条件判断

| 操作符 | 含义           |
| ------ | -------------- |
| `-f`   | 是否为普通文件 |
| `-d`   | 是否为目录     |
| `-e`   | 是否存在       |
| `-eq`  | 数值相等       |
| `-gt`  | 大于           |

```bash
[ -f test.txt ] && echo "文件存在"
[ 10 -gt 5 ] && echo "10大于5"
```

### if语句

```bash
if [ -f test.txt ]; then
    echo "test.txt存在"
elif [ -d test ]; then
    echo "test是目录"
else
    echo "都不存在"
fi
```

### for循环

```bash
for name in a b c; do
    echo $name
done

for ((i=1; i<=5; i++)); do
    echo $i
done
```

### while循环

```bash
i=1
while [ $i -le 5 ]; do
    echo $i
    i=$((i + 1))
done
```

### case语句

```bash
case $answer in
    yes|y)
        echo "选择了yes"
        ;;
    no|n)
        echo "选择了no"
        ;;
    *)
        echo "输入无效"
        ;;
esac
```

### 函数

```bash
say_hello() {
    echo "Hello, $1!"
}
say_hello "张三"
```

### 读取用户输入

```bash
read -p "请输入姓名: " name
echo "你好，$name"
```

### 退出状态码

- `0`：成功
- `1-255`：失败

```bash
exit 0
```

### 调试脚本

```bash
bash -x test.sh
```

### 注意事项

- 脚本需要执行权限：`chmod +x test.sh`
- 第一行写：`#!/bin/bash`
- 变量赋值等号两边不能有空格
- 条件判断 `[ ]` 左右必须有空格


## 45. Linux常见文件夹的作用

### 根目录下常见文件夹

| 目录          | 作用                                                       |
| ------------- | ---------------------------------------------------------- |
| `/`           | 根目录，所有目录的起点                                     |
| `/bin`        | 存放基本命令（如ls、cp、mv），所有用户都可使用             |
| `/sbin`       | 存放系统管理命令（如fdisk、ifconfig），通常只有root可执行  |
| `/etc`        | 存放系统配置文件（如passwd、hostname、apt源列表）          |
| `/home`       | 普通用户的家目录，每个用户有一个子目录（如/home/username） |
| `/root`       | root用户的家目录                                           |
| `/var`        | 存放变化的数据（如日志/var/log、缓存/var/cache）           |
| `/tmp`        | 临时文件目录，系统重启会清空                               |
| `/usr`        | 存放用户安装的应用程序和文件（类似Windows的Program Files） |
| `/opt`        | 存放第三方软件（如JDK、MySQL手动安装包）                   |
| `/boot`       | 存放系统启动文件（内核、引导程序）                         |
| `/dev`        | 存放设备文件（如硬盘/dev/sda、终端/dev/tty）               |
| `/proc`       | 虚拟文件系统，存放进程和内核信息（如CPU信息/proc/cpuinfo） |
| `/sys`        | 虚拟文件系统，存放内核设备信息                             |
| `/mnt`        | 临时挂载点，用于挂载外部设备                               |
| `/media`      | 自动挂载点，用于U盘、光驱等可移动设备                      |
| `/lib`        | 存放系统库文件（类似Windows的DLL文件）                     |
| `/lost+found` | 文件系统恢复时存放丢失的文件                               |

### 常用路径速查

| 路径                    | 作用                     |
| ----------------------- | ------------------------ |
| `/etc/passwd`           | 用户账号信息             |
| `/etc/shadow`           | 用户密码信息（加密存储） |
| `/etc/group`            | 用户组信息               |
| `/etc/hostname`         | 主机名配置               |
| `/etc/hosts`            | 本地域名解析             |
| `/etc/apt/sources.list` | Ubuntu软件源配置         |
| `/etc/ssh/sshd_config`  | SSH服务配置              |
| `/var/log/syslog`       | 系统日志                 |
| `/var/log/nginx/`       | Nginx日志                |
| `/home/用户名/.bashrc`  | 用户bash配置             |
| `/usr/local/bin`        | 用户安装的可执行程序     |

