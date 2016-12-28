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

##资源定义与管理

##使用示例