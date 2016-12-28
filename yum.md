## YUM 研究
### 查询功能: yum [list|info|search|provides|whatprovides] 
```
yum [option][查找工作目录]
[option]:
    -y： 当 yum 要等待使用者输入时，这个选项可以自动提供 yes 的回应；
    --installroot=/some/path: 将该软件安装在 /some/path 而不使用默认路径
[查询工作项目] [相关参数]：这方面的参数有：
  search  ：搜寻某个软件名称或者是描述 (description) 的重要关键字；
  list    ：列出目前 yum 所管理的所有的软件名称与版本，有点类似 rpm -qa；
  info    ：同上，不过有点类似 rpm -qai 的运行结果；
  provides：从文件去搜寻软件！类似 rpm -qf 的功能！

$ yum search python | less
============================= N/S matched: python ==============================
GitPython.noarch : Python Git Library
OpenIPMI-python.x86_64 : IPMI Python language bindings
abrt-addon-python.x86_64 : abrt's addon for catching and analyzing Python
                         : exceptions
                         abrt-python.x86_64 : ABRT Python API
                         abrt-python-doc.noarch : ABRT Python API Documentation
                         antlr-python.noarch : Python runtime support for ANTLR-generated parsers
                         at-spi-python.x86_64 : Python bindings for at-spi


$ yum info openstack-nova
Available Packages
Name        : openstack-nova
Arch        : noarch
Epoch       : 1
Version     : 12.0.4
Release     : 1.el7.centos.ustack
Size        : 7.1 k
Repo        : eos-0.1-ustack
Summary     : OpenStack Compute (nova)
URL         : http://openstack.org/projects/compute/
License     : ASL 2.0
Description : OpenStack Compute (codename Nova) is open source software designed to
            : provision and manage large networks of virtual machines, creating a
            : redundant and scalable cloud computing platform. It gives you the
            : software, control panels, and APIs required to orchestrate a cloud,
            : including running instances, managing networks, and controlling access
            : through users and projects. OpenStack Compute strives to be both
            : hardware and hypervisor agnostic, currently supporting a variety of
            : standard hardware configurations and seven major hypervisors.

$ yum list | less
Installed Packages
MariaDB-client.x86_64                10.1.10-1.el7.centos   @eos-0.1-mariadb
MariaDB-common.x86_64                10.1.10-1.el7.centos   @eos-0.1-mariadb
MariaDB-server.x86_64                10.1.10-1.el7.centos   @eos-0.1-mariadb
MariaDB-shared.x86_64                10.1.10-1.el7.centos   @eos-0.1-mariadb
MySQL-python.x86_64                  1.2.3-11.el7           @eos-0.1-centos-base




[root@server-42.100.ct.ustack.in ~ ]$ yum list updates
ceph                                                                                                                                                                                     | 2.9 kB  00:00:00
eos-0.1-centos-base                                                                                                                                                                      | 2.9 kB  00:00:00
eos-0.1-centos-centosplus                                                                                                                                                                | 3.4 kB  00:00:00
eos-0.1-centos-contrib                                                                                                                                                                   | 2.9 kB  00:00:00
eos-0.1-centos-extras                                                                                                                                                                    | 3.4 kB  00:00:00
eos-0.1-centos-updates                                                                                                                                                                   | 3.4 kB  00:00:00
eos-0.1-epel                                                                                                                                                                             | 2.9 kB  00:00:00
eos-0.1-foreman                                                                                                                                                                          | 2.9 kB  00:00:00

$ yum provides /etc/ceilometer/ceilometer.conf
1:openstack-ceilometer-common-5.0.0-1.el7.noarch : Components common to all OpenStack ceilometer services
Repo        : eos-0.1-openstack-liberty
Matched from:
Filename    : /etc/ceilometer/ceilometer.conf
```

### yum 软件安装和升级
yum [option] [查询工作项目] [相关参数]
选项与参数：
  install ：后面接要安装的软件！
  update  ：后面接要升级的软件。若要整个系统都升级(包括内核)，就直接 update 即可

### yum 删除软件
yum [remove] 软件
会接触依赖问题。

