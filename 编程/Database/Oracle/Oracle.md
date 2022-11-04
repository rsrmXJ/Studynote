# Oracle

## 第一章 安装 Oracle

- OUI (Oracle Universal Installer) 图形化模式
  - 缺点：手动选择，不适合自动批量和重复的操作。不适合远程安装
  - 优点：适合学习。

### 1.1 OFA

Oracle 最佳灵活体系结构（OFA）标准。规定一致的目录结构和文件命名约定。

##### 1\. Oracle 清单目录

用于存储在服务器上安装的 Oracle 软件清单。该目录是必须创建的，安装的所有文件公用此目录。

首次安装：

① 首先检查是否存在 `/u[01-09]/app` 格式符合 OFA 标准的目录结构，如果存在就自动创建例如：`/u01/app/oralInventory`

② 如果用户 oracle 定义了 `ORACLE_BASE` 变量，那么为自动创建：`ORACLE_BASE/../oraInventory` 目录。也就是在 ORACLE_BASE 的上一层目录创建 oraInventory

​	例如: ORACLE_BASE 定义为：`/ora01/app/oracle`,那么就会自动创建：`/ora01/app/oraInventory`

③ 如果上面两个都没找到：那么就会创建:`ORACLE_BASE\oraInventory` (19c)，`/home/oracle/oraInventory`（12C）,可以通过 `/etc/oralnst.ioc` 来查看

Tips: 如果是升级，则安装程序直接从 `/etc/oralnst.ioc` 来查看。不同的 Oracle 版本可能有不同的 ORACLE_HOME 目录。

##### 2. Oracle 基础目录 （ORACLE_BASE）

是安装 Oracle 软件的最顶层目录，可以在该目录下安装 Oracle 的多个版本。基础目录的 OFA 标准：`/<monut_point>/app/<software_onwner>`  即：`/挂载点/app/软件所有者`

挂载点的典型名称：`/u01，/ora01，/oracle 和/oracle01`  软件所有者通常会被命名为: oracle。典型例子:`/u01/app/oracle`

##### 3\. oracle 主目录 (ORACLE_HOME)

定义了特定产品安装位置。

OFA 标准：`ORACLE_BASE/product/<version>/<install_name>`

- version 为软件版本号。
- install_name 可以使用的值包括: `db_1,devdb1,test1,prod1,dbhome_1`

例如:`/opt/oracle/product/19c/dbhome_1`

##### 4. Oracle 网络文件目录 （TNS_ADMIN）

某些 Oracle 程序使用 TNS_ADMIN 定位网络配置文件。该目录被定义为：`$ORACLE_HOME/network/admin`。其中常含有 Oracle Net 文件 `tnsnames.ora和listener.ora`

> 有些 DBA 喜欢设置 TNS_ADMIN 指向中心目录位置，例如：`/etc` 或 `/var/opt/oracle`。这使他们可以维护一组 Oracle 网络文件，而不是单个 ORACLE_HOME 目录中的网络文件。(因为有可能安装多个版本 Oracle)

##### 5\. 自动诊断信息库 ADR_HOME

指定了 Oracle 相关诊断文件的位置。 `ORACLE_BASE/diag/rbdms/lower(db_unique_name)/instance_name`。可以查看 V$PARAMETER 视图，来获得 `db_unique,instance_name` 的值。

### 1.2 安装 Oracle

##### 1\. 创建  OS 组和用户

第一种：组织中仅有一个 DBA，且无需更细致地划分团队成员的权限。

仅创建 dba 组和用户 oracle

第二种：创建多个组，添加多个 OS 用户，然后根据必要条件和工作角色将它们分配到这些组中。可以为用户提供特定数据库权限。



![Oracle 分组以及权限](Oracle%20%E5%88%86%E7%BB%84%E4%BB%A5%E5%8F%8A%E6%9D%83%E9%99%90.png)

```
sysdba 超级管理员 sys：默认密码为 oracle

sysoper 操作员 system 

normal 一般用户
# 管理员账户只有一个，需要验证密码和用户名，但需要指明身份 `sysplus sys/oracle as sysdba` 或 `sysplus / as sysdba`
`sqlplus 用户名/密码 as 身份` 
```

```
/usr/sbin/groupadd -g 54321 oinstall
/usr/sbin/groupadd -g 54322 dba
/usr/sbin/groupadd -g 54323 oper
/usr/sbin/groupadd -g 54324 backupdba
/usr/sbin/groupadd -g 54325 dgdba
/usr/sbin/groupadd -g 54326 kmdba
/usr/sbin/groupadd -g 54327 asmdba
/usr/sbin/groupadd -g 54328 asmoper
/usr/sbin/groupadd -g 54330 racdba
/usr/sbin/useradd -u 54321 -g oinstall -G dba,asmdba,backupdba,dgdba,kmdba,racdba oracle
/usr/sbin/useradd -u 54331 -g oinstall -G dba,asmdba,backupdba,dgdba,kmdba,racdba grid
```

##### 2\. 确保充分配置了 OS

该步骤会随着不同版本的数据库和 OS 略有变化。必须阅读数据库和 OS 供应伤的 Oracle 安装手册，了解确切的要求（<www.oracle.com/documentation>）。验证和配置下列 OS 组件：

- 内存的大小: `grep MemTotal /proc/meminfo`
- 内存和交换空间的容量: `free -t`
- 磁盘的可用空间：`df -h`
- OS 版本: `cat /proc/version`
- 内核信息：`uname -r`
- 确定安装了必要的软件包：`rpm -q <package_name>`

##### 3\. 下载 Oracle 软件

下载地址：<https://www.oracle.com/database/technologies/> 

<https://www.oracle.com/downloads/>

##### 4\. 解压缩文件

在解压之前创建一个标准目录，纯粹用于容纳 Oracle 安装文件。例如 `/home/oracle/orainst/`

```shell
mkdir -p /home/oracle/orainst/11.2.0.2 # 后面的数字是 oracle 的版本号
mv 安装包 /home/oracle/orainst/11.2.0.2
unzip 按照包.zip # 解压文件
cat 按照包.cpio.gz | gunzip | cpio -idvm
netca /silent /responseFile /u01/app/oracle/product/19.5.0/assistants/netca/netca.rsp
```

##### 5\. 创建 oraInst.loc 文件

使用 OUI 图形界面安装程序，安装程序会自动创建。

- Linux 系统：`/etc` 目录下
- Unix 系统 (Solaris): `/var/opt/oracle`

该文件的信息：

- Oracle 清单目录路径：
- 拥有安装和升级 Oracle 软件操作权限的 OS 组的名称：

```shell
inventory_loc=/u01/app/oraInventory
inst_group=oinstall
```

需确保文件归 oracle 所有，并拥有适当权限：

```shell
chown oracle:oinstall oraInst.loc
chmod 664 oraInst.loc 
```

##### 6\. 安装必要的依赖软件

```shell
yum install -y bc binutils elfutils-libelf elfutils-libelf-devel fontconfig-devel glibc glibc-devel ksh libaio libaio-devel libXrender libX11 libXau libXi libXtst libgcc libnsl librdmacm libstdc++ libstdc++-devel libxcb libibverbs make smartmontools sysstat ipmiutil libnsl2 libnsl2-devel net-tools nfs-utils unixODBC
```

##### 7\. 配置内核

创建或编辑`/etc/sysctl.d/97-oracle-database-sysctl.conf`文件 (不必要，但可以更好的发挥心梗你)

```
fs.aio-max-nr = 1048576
fs.file-max = 6815744
kernel.shmall = 2097152
kernel.shmmax = 4294967295
kernel.shmmni = 4096
kernel.sem = 250 32000 100 128
net.ipv4.ip_local_port_range = 9000 65500
net.core.rmem_default = 262144
net.core.rmem_max = 4194304
net.core.wmem_default = 262144
net.core.wmem_max = 1048576
```

```
# 更改内核参数的值
/sbin/sysctl --system
#确认设置值
/sbin/sysctl -a
```

重新启动计算机，或运行`sysctl --system`以使`/etc/sysctl.d/97-oracle-database-sysctl.conf`文件更改在活动的内核内存中可用。

8\. 配置应答文件及安装

为了版本的兼容性，安装之前先执行：`export CV_ASSUME_DISTID=RHEL7.6`

```java
./runInstaller -ignorePrereq  -silent -responseFile 应答文件所在的目录
```

### 1.3  使用已安装程序的副本安装 Oracle

第一步：使用 OS 实用程序复制已安装的程序



oracle 常用命令

- 显示当前用户：`show user` 或 `sho user`
- 修改用户属性：`ALTER USER 用户名 具体的操作`
  - 修改密码：`IDENTIFIED BY 密码`
  - 密码过期：`password EXPIRE`
  - 账户解/锁：`account unlock/lock;`
- 关闭 oracle:
  - 掉电（非法）：`shut abort`
  - 启动：`startup`

用户都存放在 dba_users 表中

`$ORACLE_HOME/sqlplus/glogin.sql` 在连接数据库会先初始化配置

- `set linesize 120` 不到120 个字符不换行
- `set pagesize 100`

切换用户：`connect [账户名]/[密码] [指明身份]` 可以将 connect 略写为 conn

Oracle 数据库管理



1\. 在数据库中导入文件

sql developer 中：

```SQL
@sql文件所在的位置;
```

> 查看系统日期 ：`SELECT SYSDATE FROM dual;`

