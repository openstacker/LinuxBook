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
$ pcs cluster setup --start --name my_cluster pcs1 pcs2    #创建名为my_cluster的集群
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
$ pcs cluster enable --all
pcs1: Cluster Enabled
pcs2: Cluster Enabled

```

##资源定义与管理

##使用示例