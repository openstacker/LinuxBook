#Keepalive

---

##简介
Keepalived 是一个基于VRRP协议来实现的服务高可用方案，可以利用其来避免单点故障。

一个服务会有2台服务器运行Keepalived，一台为主服务器（MASTER），一台为备份服务器（BACKUP），但是对外表现为一个虚拟IP，主服务器会发送特定的消息给备份服务器，当备份服务器收不到这个消息的时候，即主服务器宕机的时候，备份服务器就会接管虚拟IP，继续提供服务，从而保证了高可用性。

##安装及使用
HAProxy的安装部署非常简单：
```shell
$ sudo yum install keepalived
$ sudo apt-get install keepalived
```
安装完成后，keepalived以服务的形式存在,配置文件位于/etc/keepalved/keepalived.conf，示例如下：
```shell
#Master Node Config
global_defs {
   notification_email {
   }
   router_id LVS_DEVEL
}

vrrp_instance VI_1 {
    state MASTER
    interface eno16777736
    virtual_router_id 51
    priority 101

    authentication {
        auth_type PASS
        auth_pass 1111
    }

    virtual_ipaddress {
        192.168.123.100
    }
}

#Slave Node Config
global_defs {
   notification_email {
   }
   router_id LVS_DEVEL
}

vrrp_instance VI_1 {
    state SLAVE
    interface eno16777736
    virtual_router_id 51
    priority 100

    authentication {
        auth_type PASS
        auth_pass 1111
    }

    virtual_ipaddress {
        192.168.123.100
    }
}
```