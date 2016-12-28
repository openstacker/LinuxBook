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
rpm 安装
```
rpm -ivh
# -i install
# -v verbose
# -h human-readable
```

rpm 升级
```
rpm -Uvh / rpm -Fvh
# -Uvh 升级过程中发现没有相应的包，则去下载
# -Fvh 升级过程中发现没有相应的包，则软件不会被升级
```

rpm 查询
rpm 进行查询时，其实在查询 /var/lib/rpm/ 目录下的信息
```
$ rpm -q python
python-2.7.5-34.el7.x86_64

# -q <pkg>: 查看 <pkg> 软件是否安装 (query)
# -qa: 查看所有安装的软件 (query all)
# -qi <pkg>: 列出 pkg 的所有 information (query information)
# -ql <pkg>: 列出 pkg 所有文件的安装位置 （query location）
# -qc <pkg>: 列出 pkg 所有的 configuration 文件安装位置（query configuration）
# -qd <pkg>: 列出 pkg 所有说明文档 (query docfile)
# -qR <pkg>: 列出 pkg 所有依赖 （query required）
# -qf <sinppet-pkg>: 查出 sinppet-pkg 是属于哪个已安装的软件
# -qp : -qpR, --qpc (query package) 这个命令针对的是 rpm 包并不是某个软件。
```
