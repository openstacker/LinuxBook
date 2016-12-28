# RPM包管理机制

## rpm 和 srpm

rpm 是一种 Linux 系统基于数据记录的包管理机制。  
数据库会记录软件安装的元信息。  
当在安装一个软件时 rpm 会依照元信息验证主机是否符合要求。  
rpm 包是编译过后的包。

srpm 类似 rpm， 但是提供的是没有编译过的包。

| 文件格式 | 包名 | 直接安装 | 编译 | 可编译 |
| :--- | :--- | :--- | :--- | :--- |
| RPM | xxx.rpm | 可以 | 已编译 | 不可 |
| SRPM | xxx.src.rpm | 不可以 | 未编译 | 可 |

rpm 包命名规则  
python-swiftclient-2.6.0-1.el7.noarch

* pytho-swiftclient: 软件包名
* 2.6.0-1： 版本号
* el7： 软件包发行商版本
* noarch： 使用硬件平台

* 发行商版本有： el7 RHEL 7.x\(Red Hat Enterprise Linux\)/CentOS 7.x

* 包名中带有 devel： 说明是 rpm 包是开发包。
* rpm 操作平台：
  * i386：适用于大部分 X86 平台，i 指的是 Intel 兼容CPU， 386 指的是 CPU 等级。 
  * x86\_64：针对64位的CPU
  * noarch： 没有任何硬件等级上的限制，rpm 包里没有 binary，通常是 shell script。
  
```
$ rpm -qi python
Name        : python
Version     : 2.7.5
Release     : 34.el7
Architecture: x86_64
...
```

## yum 工具

yum 工具主要用于解决下载 rpm 包，用于解决包的依赖性等问题。

refer: [http://cn.linux.vbird.org/linux\_basic/0520rpm\_and\_srpm.php](http://cn.linux.vbird.org/linux_basic/0520rpm_and_srpm.php)

## rpm 工具

### rpm 包被安装的位置

| 文件 | 作用 |
| :---: | :---: |
| /etc | 配置文件目录 |
| /usr/bin | 可运行文件目录 |
| /usr/lib | 程序使用的动态函数库 |
| /usr/share/doc | 使用说明手册和文档 |
| /usr/share/man | man page |


## rpm 
###rpm 安装
```
rpm -ivh
# -i install
# -v verbose
# -h human-readable
```

###rpm 升级
```
rpm -Uvh / rpm -Fvh
# -Uvh 升级过程中发现没有相应的包，则去下载
# -Fvh 升级过程中发现没有相应的包，则软件不会被升级
```

###rpm 查询
rpm 进行查询时，其实在查询 /var/lib/rpm/ 目录下的信息
```
$ rpm -q python
python-2.7.5-34.el7.x86_64

# 针对软件 （软件包名）
# -q <software>: 查看 <software> 软件是否安装 (query)
# -qa: 查看所有安装的软件 (query all)
# -qi <software>: 列出 pkg 的所有 information (query information)
# -ql <software>: 列出 pkg 所有文件的安装位置 （query location）
# -qc <software>: 列出 pkg 所有的 configuration 文件安装位置（query configuration）
# -qd <software>: 列出 pkg 所有说明文档 (query docfile)
# -qR <software>: 列出 pkg 所有依赖 （query required）
# -qf <software-sinppet>: 查出 sinppet-pkg 是属于哪个已安装的软件

# 针对package
# -qp <pkg>: -qpR <pkg>, -qpc <pkg> (query package) 这个命令针对的是 rpm 包并不是某个软件。
$ rpm -qpR zabbix-agent-3.0.5-1.el7.x86_64.rpm
warning: zabbix-agent-3.0.5-1.el7.x86_64.rpm: Header V4 DSA/SHA1 Signature, key ID 79ea5ed4: NOKEY
/bin/sh
/bin/sh
/bin/sh
/bin/sh
/usr/sbin/useradd
config(zabbix-agent) = 3.0.5-1.el7
libc.so.6()(64bit)
libc.so.6(GLIBC_2.14)(64bit)
libc.so.6(GLIBC_2.15)(64bit)
libc.so.6(GLIBC_2.17)(64bit)
libc.so.6(GLIBC_2.2.5)(64bit)
libc.so.6(GLIBC_2.3)(64bit)
libc.so.6(GLIBC_2.3.4)(64bit)
libc.so.6(GLIBC_2.4)(64bit)
libc.so.6(GLIBC_2.7)(64bit)
libcrypto.so.10()(64bit)
libcrypto.so.10(OPENSSL_1.0.1)(64bit)
libcrypto.so.10(OPENSSL_1.0.1_EC)(64bit)
libcrypto.so.10(libcrypto.so.10)(64bit)
libcurl.so.4()(64bit)
libdl.so.2()(64bit)
libdl.so.2(GLIBC_2.2.5)(64bit)
liblber-2.4.so.2()(64bit)
libldap-2.4.so.2()(64bit)
libm.so.6()(64bit)
libresolv.so.2()(64bit)
libresolv.so.2(GLIBC_2.2.5)(64bit)
libssl.so.10()(64bit)
libssl.so.10(libssl.so.10)(64bit)
logrotate
rpmlib(CompressedFileNames) <= 3.0.4-1
rpmlib(FileDigests) <= 4.6.0-1
rpmlib(PayloadFilesHavePrefix) <= 4.0-1
rtld(GNU_HASH)
systemd
systemd
rpmlib(PayloadIsXz) <= 5.2-1
```
在查询本机上面的 RPM 软件相关资讯时， 不需要加上版本的名称，只要加上软件名称即可！因为他会由 /var/lib/rpm 这个数据库里面去查询， 所以我们可以不需要加上版本名称。但是查询某个 RPM 文件就不同了，我们必须要列出整个文件的完整档名才行（-qp）。

###rpm 验证

> 验证 (Verify) 的功能主要在於提供系统管理员一个有用的管理机制！作用的方式是『使用 /var/lib/rpm 底下的数据库内容来比对目前 Linux 系统的环境下的所有软件文件 』也就是说，当你有数据不小心遗失， 或者是因为你误杀了某个软件的文件，或者是不小心不知道修改到某一个软件的文件内容， 就用这个简单的方法来验证一下原本的文件系统吧！好让你了解这一阵子到底是修改到哪些文件数据了！验证的方式很简单

```
# 查看所有被改动过的文件
$ rpm -Va
S.5....T.  c /etc/neutron/plugins/ml2/ml2_conf.ini
.M.......    /var/run/resource-agents
S.5....T.  c /etc/ssh/ssh_config
S.5....T.  c /etc/logrotate.d/syslog
S.5....T.  c /etc/rsyslog.conf
S.5....T.  c /etc/sysconfig/rsyslog

# 查看以安装的软件是否被改动过
$ rpm -V openstack-ceilometer-common-5.0.2-1.el7.noarch
S.5....T.  c /etc/ceilometer/ceilometer.conf
..5....T.  c /etc/ceilometer/pipeline.yaml

$ rpm -V openstack-ceilometer-common
S.5....T.  c /etc/ceilometer/ceilometer.conf
..5....T.  c /etc/ceilometer/pipeline.yaml

# 查看某个rpm包中文件否改动过
$ rpm -Vp zabbix-agent-3.0.5-1.el7.x86_64.rpm
warning: zabbix-agent-3.0.5-1.el7.x86_64.rpm: Header V4 DSA/SHA1 Signature, key ID 79ea5ed4: NOKEY
S.5....T.  c /etc/zabbix/zabbix_agentd.conf

# 查看在系统上的某个文件是否被改动过
$ rpm -Vf gnocchi.conf
.......T.  c /etc/gnocchi/api-paste.ini
S.5....T.  c /etc/gnocchi/gnocchi.conf
```