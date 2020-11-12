

LVS模式

1.VS/NAT(Virtual Server via Network Address Translation)，即网络地址翻转技术实现虚拟服务器。当请求来到时，Diretor server上处理的程序将数据报文中的目标地址（即虚拟IP地址）改成具体的某台Real Server,端口也改成Real Server的端口，然后把报文发给Real Server。Real Server处理完数据后，需要返回给Diretor Server，然后Diretor server将数据包中的源地址和源端口改成VIP的地址和端口，最后把数据发送出去。由此可以看出，用户的请求和返回都要经过Diretor Server，如果数据过多，Diretor Server肯定会不堪重负。

2.VS/TUN（Virtual Server via IP Tunneling）,即IP隧道技术实现虚拟服务器。它跟VS/NAT基本一样，但是Real server是直接返回数据给客户端，不需要经过Diretor server,这大大降低了Diretor server的压力。

3.VS/DR（Virtual Server via Direct Routing），即用直接路由技术实现虚拟服务器。跟前面两种方式，它的报文转发方法有所不同，VS/DR通过改写请求报文的MAC地址，将请求发送到Real Server，而Real Server将响应直接返回给客户，免去了VS/TUN中的IP隧道开销。这种方式是三种负载调度机制中性能最高最好的，但是必须要求**Director Server与Real Server都有一块网卡连在同一物理网段**上。

