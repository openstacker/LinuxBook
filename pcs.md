#PCS

##简介
Pacemaker是一个群集资源管理器，提供资源管理功能，通常与提供了心跳的corosync配合使用，以实现服务的监控与自动维护功能。

##安装部署
Pacemaker的安装比较简单：
```shell
$ sudo yum install pcs pacemaker corosync fence-agents-all
$ sudo systemctl start pcsd
$ passwd hacluster    #该用户会在安装过程中自动创建
Changing password for user hacluster    #需要在所有的节点上执行
New password:
Retype new password:
passwd: all authentication tokens updated successfully.
$ pcs cluster auth pcs1 pcs2
Username: hacluster
Password: 
pcs1: Authorized
pcs2: Authorized
```

##资源定义与管理

##使用示例