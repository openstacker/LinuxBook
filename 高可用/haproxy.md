#HAProxy

---

##简介

HAProxy 是一款非常的专业的全7层的反向代理负载均衡器，可以实现 4 层和 7 层的负载均衡，4 层使用的是 tcp 模式可以模拟 lvs，7 层使用的是 http 模式可以模拟 nginx。

HAProxy 还支持 ssl offload，为后端的服务器提供 ssl 终结和卸载。

##安装

HAProxy 的安装部署非常简单：
```shell
$ sudo yum install haproxy
$ sudo apt-get install haproxy
```
安装完成后，HAProxy 以服务的形式存在，配置文件位于 /etc/haproxy/haproxy.cfg

##简单示例

三台真实提供 Web 服务的节点分别为：

* node1 192.168.1.101
* node2 192.168.1.102
* node3 192.168.1.103

提供 HAProxy 服务器的节点地址为 192.168.1.100。
如希望按照 RR 算法实现最简单的负载均衡调度，那么则需要在 HAProxy 的配置文件中加入如下内容：
```shell
backend web
    balance     roundrobin
    option httpchk
    server  node1 192.168.1.101:80 check
    server  node2 192.168.1.102:80 check
    server  node3 192.168.1.103:80 check
```
配置完成后重启服务，针对192.168.1.100:80的访问将会被轮询的转发至三个不同的节点。

如果希望隐藏真实服务器的端口，则可以使用如下的配置：
```shell
backend web
    bind *.8080
    server  node1 192.168.1.101:80 check
    server  node2 192.168.1.102:80 check
    server  node3 192.168.1.103:80 check
```
此时对外提供服务的端口改变为 8080，而真实服务器的端口仍然为 80。同时真实服务器后所携带的参数 check 则表示 HAProxy 会进行健康检测，当某台真实服务器被探测到不可达之后，请求将不会被转发到该服务器上。

##支持功能

HAProxy 支持大量的功能特性，我们将会介绍 mode，部分 option 及调度算法三部分内容。

###mode

常见的模式为 tcp 和 http，同时还有一个类型为 health。

Tcp 模式下，客户端和真实服务器之间将会建立 tcp 连接，HAProxy 不会对 7 层报文做任何类型的检查；此为默认模式，通常用于 SSL、SSH、SMTP 等应用。

Http 模式下，客户端请求在转发至后端服务器之前将被深度分析，所有不与 HTTP RFC 格式兼容的请求都会被拒绝。

Health 模式下，其对入站请求仅响应 “OK” 信息并关闭连接，且不会记录任何日志信息；此模式将用于响应外部组件的健康状态检查请求，目前此模式已经废弃。

###option

常见 option 如下：
* Httpchk 服务器的健康检测将通过 http 请求完成，同时支持不同的 http 方法、版本及 uri
* Httpclose 默认 HAProxy 工作在 keep-alive 模式下，如果有该参数的存在，HAProxy 在任何一端的通信中收到 connection:close 的参数后，将会在另一端的通信中增加该参数。
* Httplog 开启有关 http 的请求、会话状态以及计时器的日志。
* Redispatch 该参数通常配合部分调度算法使用，当某台真实服务器出现问题的时候，存在该配置的情况下，HAProxy 会将请求转发至其他存活的服务器而不考虑调度算法的限制。
* Srvtcpka 当网络链路中存在防火墙等会话敏感设备的情况下，该参数的存在可以防止服务器端由于长期无操作中断会话而 HAProxy 和客户端仍然保持会话而带来的通信中断的问题

###调度算法

HAProxy 支持大量的调度算法，通过关键词 balance 用来指明，常见算法如下：
* roundrobin 请求将会被依次转发到各个服务器节点上，同时支持在各个服务器上配置不同的权重以调整调度到不同服务器上的概率。
* static-rr 请求将会被严格的轮询到各个真实服务器上，权重不生效。
* leastconn 请求将会被优先调度到连接最少的服务器上。
* source 将按照客户端 IP 进行 hash 后调度，以保证相同的客户端的请求总是被调度到相同的服务器上。


