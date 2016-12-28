#Multipath

---

##简介
普通的服务器都是一个硬盘挂接到一个总线上，这里是一对一的关系。而到了有FC或其他网络组成的SAN环境，由于主机和存储通过了交换机连接，就可以构成多对多的关系，也就是说，主机到存储可以有多条路径可以选择，主机到存储之间的IO由多条路径可以选择。

每个主机到所对应的存储可以经过几条不同的路径，如果是同时使用的话，I/O流量如何分配？其中一条路径坏掉了，如何处理？角度来看，每条路径，操作系统会认为是一个实际存在的物理盘，但实际上只是通向同一个物理盘的不同路径而已，这样是在使用的时候，就给用户带来了困惑。

Multipath就是为了解决上面的问题应运而生的。

##安装使用
安装过程较为简单：
```shell
$ sudo yum install device-mapper-multipath
```
安装完成后，会以multipathd的服务形式存在。

在最一开始，需要将无需使用multipath管理的磁盘加入到黑名单中以避免出现问题，建议尽量使用磁盘的WWID而不是使用磁盘名称，因为不能确保重启后磁盘仍为同一名称。
```shell
$ scsi_id -g -u -s /block/sda    #获取sda的wwid
3600508e000000000dc7200032e08af0b
$ cat /etc/multipath.conf
blacklist {
    wwid 35000c5005e7abebf    #wwid与devnode二选一，建议使用devnode
    devnode "^sda$"    
}
```