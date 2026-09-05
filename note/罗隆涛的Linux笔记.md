# Linux学习笔记（精简整理版）
## 1、常见操作系统
### PC端
- Windows、Linux、MacOS

### 移动端
- Android、iOS、HarmonyOS（鸿蒙）

## 2、Linux系统简介
- 创始人：林纳斯·托瓦兹，1991年
- Linux内核职责：调度CPU、内存、文件系统、网络通讯、IO
- **内核 + 系统级应用程序 = Linux发行版**
- 主流发行版：CentOS、Ubuntu、Rocky Linux（替代CentOS）

## 3、虚拟机环境
1. VirtualBox 获取Linux实验环境
    - `Win+R` → `ncpa.cpl` 打开网络适配器
    - VirtualBox：清华镜像下载，同步安装Oracle扩展包
    - Rocky9镜像：中科大镜像站
2. 快捷键：右侧`Ctrl`释放鼠标，退出虚拟机捕获
3. SSH远程连接
```bash
ssh root@192.168.56.101
```
>网卡2配置为仅主机模式，实现本机访问虚拟机
4. 关机：`shutdown -h now`
5. 两种使用模式：图形化、命令行；远程工具：FinalShell
6. 快照：虚拟机存档，可随时回滚环境

## 4、Linux目录基础
1. 唯一根目录：`/`
2. 路径分隔符：Linux `/`；Windows `\`
3. 命令通用格式
```bash
command [-options] [-parameter]
```
- 选项：控制命令行为；参数：操作目标

### ls、cd、pwd
- `ls`：列出目录内容
  - `-a`显示全部（含隐藏文件）；`-l`详细列表；`-h`人性化大小，配合`‑l`使用
- `cd`：切换目录；`pwd`：打印当前工作目录；普通用户家目录`home`

#### 权限字符串解读
1. 第1位文件类型：`d`目录、`‑`普通文件、`l`软链接
2. 后9位分三段：属主、属组、其他人
3. `r`读(4)、`w`写(2)、`x`执行(1)；`‑`代表无权限

#### 特殊路径符号
- `.`当前目录；`..`上一级；`../..`向上两级
- `~`用户家目录；`cd ..`返回上级；`cd ~`回到家目录

### 文件&文件夹操作
- `mkdir -p`：创建目录，`‑p`递归多级创建
- `touch`：创建空文件
- `cat`：一次性读取全部文件内容
- `more`分页查看，空格翻页，`q`退出
- `cp [-r] 源 目标`：复制，复制文件夹必须加`‑r`
- `mv 源 目标`：移动/重命名
- `rm [-f -r]`：删除，`‑f`强制不提示，`‑r`删除文件夹
- 通配符`*`：匹配任意字符

### 查找命令
- `which 命令`：查找命令程序路径
- `find 起始路径 -name "文件名"`：按名称查找，支持通配符
- `find 起始路径 -size +n/-n`：按大小查找，单位k/M/G

### 文本处理
- `grep [-n] "关键字" 文件`：过滤匹配行，`‑n`显示行号
- `wc [-c/m/l/w] 文件`：统计字节、字符、行数、单词数
- 管道符 `|`：左侧输出作为右侧输入
- `echo`输出内容；反引号`` `命令` ``执行命令
- `>`覆盖重定向；`>>`追加重定向
- `tail [-f num] 文件`：查看文件尾部；`‑f`跟踪日志；`Ctrl+C`终止

### vim编辑器
- `vim 文件路径`
- 三种模式：命令模式（默认）、输入模式、底线命令模式
  - `i`进入编辑；`Esc`退回命令模式；`:`进入底线模式
- 底线命令：`w`保存、`q`退出、`wq`保存退出；`set nu`显示行号；`set paste`粘贴模式

### 用户与权限
1. 用户切换
```bash
su - 用户名      #切换用户，exit退回
sudo 命令        #临时获取root权限
usermod -aG wheel 用户名   #普通用户加入wheel组获得sudo权限
```
2. 用户管理
- `useradd 用户名`新建；`passwd 用户名`设置密码
- `groupadd`创建组；`groupdel`删除组
- `id [用户名]`查看用户信息
- `usermod -aG 组名 用户名`追加附属组
- `getent passwd`查看全部用户；`getent group`查看全部组
3. 修改权限
- `chmod [-R]` 修改权限，符号/八进制方式；`‑R`递归目录
- `chown [-R] 用户:组 文件` 修改属主属组，仅root可用

> `-R`递归：对文件夹内所有子文件子目录生效

## 5、实用技巧
### 命令行快捷键
- `Ctrl+C`强制终止；`Ctrl+D`登出退出；`history`历史命令；`!前缀`执行历史命令
- `Ctrl+R`关键词搜索历史；`Ctrl+A`行首；`Ctrl+E`行尾；`Ctrl+L`清屏（等价clear）

### 软件安装
- Rocky/RHEL：`yum/dnf [-y] install/remove/search 软件名`，`‑y`自动确认
- Ubuntu：`apt`；rpm底层包管理器
- `systemctl start/stop/status/enable/disable 服务名` 管理系统服务（firewalld、sshd等）
- `chrony`时间同步服务

### 链接、日期、网络
1. 软链接：`ln -s 源文件 链接位置`
2. 日期格式化：`date +%Y年%m月%d日 %H:%M:%S`
3. 网络
    - `hostname`查看主机名；`hostnamectl set‑hostname xxx`修改主机名
    - `ping [-c 次数] IP`测试连通性
    - `wget [-b] url`下载，`‑b`后台；`curl url`发起请求，`‑O`下载文件
4. 端口：1‑1023公认端口；1024‑49151注册端口；49152‑65535动态端口
    - `nmap`扫描端口；`netstat -anp | grep 端口`查看端口占用

### 进程管理
- `ps -ef`查看进程快照；`kill [-9] PID`结束进程，`‑9`强制杀死
- `top`实时资源监控
    - 参数：`‑d`刷新间隔、`‑u`指定用户、`‑p`指定PID、`‑b`批处理输出
    - 交互快捷键：`q`退出、`M`按内存排序、`P`按CPU排序、`k`杀进程
- `ps -aux`一次性进程快照

### 磁盘、IO、网络监控
- `df -h`磁盘使用
- `iostat [-x] 间隔 次数` IO统计，需要`sysstat`包
- `sar -n DEV 间隔 次数`网络流量统计

### 环境变量
- `env`查看全部环境变量；`echo $PATH`读取变量
- 临时：`export key=value`；用户永久配置 `~/.bashrc`；全局 `/etc/profile`
- `source 文件`使配置立即生效

### 文件上传下载
- `yum install lrzsz`；`rz`上传，`sz`下载，大文件建议拖拽

### 压缩解压
1. zip：`zip -r 输出.zip 源`；`unzip xxx.zip [-d 目录]`
2. tar：仅打包不压缩；gzip仅压缩；**tar.gz = tar打包+gzip压缩（Linux标准）**

|参数|说明|
|----|----|
|`‑c`|创建打包|
|`‑x`|解压|
|`‑v`|打印过程|
|`‑f`|指定包名，**必须放参数末尾**|
|`‑z`|gzip压缩|
|`‑C`|指定解压目标目录|

```bash
#打包压缩
tar -zcvf test.tar.gz 文件/文件夹
#解压到指定目录
tar -zxvf test.tar.gz -C /opt
```

>口诀：c创建 x解压，v看过程 z压缩，f放最后，‑C指定去哪放

## 6、Rocky Linux9 部署MySQL8.0
>dnf为yum的软链接，用于DataGrip远程连接

### 1.安装
```bash
dnf install -y mysql-community-server --nogpgcheck
```

### 2.启动服务
```bash
systemctl enable --now mysqld
systemctl status mysqld
```
看到`active(running)`代表运行成功。

### 3.获取初始密码
```bash
grep 'temporary password' /var/log/mysqld.log
```

### 4.登录修改密码
```bash
mysql -uroot -p
```
```sql
ALTER USER 'root'@'localhost' IDENTIFIED BY '你的密码';
ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY '你的密码';
update mysql.user set host='%' where user='root';
flush privileges;
exit;
```
>密码策略：大小写+数字+英文符号，禁止中文符号

### 5.防火墙放行3306端口（shell终端执行，不要进mysql）
```bash
firewall-cmd --add-port=3306/tcp --permanent
firewall-cmd --reload
```

### 6.DataGrip连接参数
Host填虚拟机静态IP，Port3306，User root，对应密码

### 密码忘记重置方案
```bash
systemctl stop mysqld
systemctl set-environment MYSQLD_OPTS="--skip-grant-tables --skip-networking"
systemctl start mysqld
mysql -uroot
```
```sql
flush privileges;
ALTER USER 'root'@'localhost' IDENTIFIED BY '你的密码';
ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY '你的密码';
update mysql.user set host='%' where user='root';
flush privileges;
exit;
```
```bash
systemctl unset-environment MYSQLD_OPTS
systemctl restart mysqld
```
>⚠️务必清除环境变量，否则永久免密登录，有安全风险

### MySQL服务命令
```bash
systemctl status mysqld
systemctl stop mysqld
systemctl start mysqld
systemctl restart mysqld
systemctl enable mysqld
systemctl disable mysqld
```

### 踩坑总结
1. `firewall‑cmd`是shell命令，不能在mysql交互内执行
2. 密码符号必须英文半角
3. 远程连接填写虚拟机IP，不要写localhost
4. 重置密码必须清除`MYSQLD_OPTS`环境变量
5. 配置完成建议打虚拟机快照保存环境
