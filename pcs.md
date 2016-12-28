#PCS

##简介
Pacemaker是一个群集资源管理器，提供资源管理功能，通常与提供了心跳的corosync配合使用，以实现服务的监控与自动维护功能。

##安装部署
Pacemaker的安装比较简单：
```shell
$ sudo yum install pcs pacemaker corosync fence-agents-all
$ sudo systemctl start pcsd
```

安装完成后，要对pcs进行初始化的配置
```shell
$ passwd hacluster    #该用户会在安装过程中自动创建
Changing password for user hacluster    #需要在所有的节点上执行
New password:
Retype new password:
passwd: all authentication tokens updated successfully.
$ pcs cluster auth pcs1 pcs2    #集群各节点之间进行认证
Username: hacluster
Password: 
pcs1: Authorized
pcs2: Authorized
$ pcs cluster setup --start --name my_cluster pcs1 pcs2    #创建名为my_cluster的集群，并将两个节点加入该集群
Shutting down pacemaker/corosync services...
Redirecting to /bin/systemctl stop  pacemaker.service
Redirecting to /bin/systemctl stop  corosync.service
Killing any remaining services...
Removing all cluster configuration files...
pcs1: Succeeded
pcs2: Succeeded
Starting cluster on nodes: pcs1, pcs2...
pcs2: Starting Cluster...
pcs1: Starting Cluster...
$ pcs cluster enable --all    #设置集群自启动
pcs1: Cluster Enabled
pcs2: Cluster Enabled
```

集群状态查看
```shell
$ pcs cluster status
Cluster Status:
 Last updated: Thu Dec 29 00:46:35 2016		Last change: Thu Dec 29 00:41:14 2016 by hacluster via crmd on pcs2
 Stack: corosync
 Current DC: pcs2 (version 1.1.13-10.el7-44eb2dd) - partition with quorum
 2 nodes and 0 resource configured
 Online: [ pcs1 pcs2 ]

PCSD Status:
  pcs1: Online
  pcs2: Online
```

##资源管理
Pacemaker 本身已经自带了很多常用应用的管理功能。但是如果要使用 Pacemaker 来管理自己实现的服务或是一些别的没现成的东西可用的服务时，就需要自己实现一个资源了。

其中Pacemaker 自带的资源管理程序都在/usr/lib/ocf/resource.d下。

查看pcs支持的资源代理标准：
```shell
$ pcs resource standards
ocf
lsb
service
systemd
stonith
```

查看pacemaker支持资源高可用的列表：
```shell
$  pcs resource list
ocf:.isolation:docker-wrapper - docker wrapper
ocf:heartbeat:CTDB - CTDB Resource Agent
ocf:heartbeat:Delay - Waits for a defined timespan
ocf:heartbeat:Dummy - Example stateless resource agent
ocf:heartbeat:Filesystem - Manages filesystem mounts
ocf:heartbeat:IPaddr - Manages virtual IPv4 and IPv6 addresses (Linux specific version)
ocf:heartbeat:IPaddr2 - Manages virtual IPv4 and IPv6 addresses (Linux specific version)
...
systemd:tuned
systemd:unbound-anchor
systemd:wpa_supplicant
```

增加一个集群中的资源
```shell
$ pcs resource create VirtualIP ocf:heartbeat:IPaddr2 ip=192.168.123.101 cidr_netmask=24 nic=eno16777736 op monitor interval=5s
$ pcs status
Cluster name: my_cluster
Last updated: Thu Dec 29 00:52:58 2016		Last change: Thu Dec 29 00:52:55 2016 by root via cibadmin on pcs1
Stack: corosync
Current DC: pcs2 (version 1.1.13-10.el7-44eb2dd) - partition with quorum
2 nodes and 1 resource configured

Online: [ pcs1 pcs2 ]

Full list of resources:

 VirtualIP	(ocf::heartbeat:IPaddr2):	Started pcs1

PCSD Status:
  pcs1: Online
  pcs2: Online

Daemon Status:
  corosync: active/enabled
  pacemaker: active/enabled
  pcsd: active/enabled
```


##使用示例