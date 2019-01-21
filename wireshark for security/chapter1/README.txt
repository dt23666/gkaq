显示过滤器使用bootp

ipconfig  /release   断开网络连接，截获到一个DHCP Release数据包
ipconfig  /renew     请求网络连接，Wireshark中新增了4个DHCP数据包
数据包1：DHCP Discover 
数据包2：DHCP Offer 
数据包3：DHCP Request 
数据包4：DHCP ACK

1，DHCP starvation attack，DHCP饥饿攻击 
其实各种各样的攻击技术总是会让人觉得兴奋。抛却道德不谈，必须承认的是，制造这些攻击的人都是高智商。

有许多中攻击DHCP的技术，这里介绍其中一种，有点类似于SYN 洪范攻击。 
DHCP starvation attack，中文即DHCP饥饿攻击，可以顾名思义一下，饥饿攻击，就是大量地进食，把可以吃的食物全部吃完，然后让其他人没得吃，最后给其他人提供一些毒药，把人家毒死，姑且可以这样浅显地认为。

下面来说这种攻击是如何实现的。一些不法分子，伪造合法的MAC地址，不断地向DHCP服务器发出DHCP Request包，最后耗尽服务器的可用IP,于是原有的这台DHCP服务器便不能够给客户端分配IP了，此时不法分子再伪造一台DHCP服务器，给客户端分配IP,将客户端的默认网关和DNS都设置成自己的机器，于是便可以对客户端进行中间人攻击。

2，一些关于DHCP安全性的研究 
网络到处都有安全漏洞啊，上网需谨慎。现在有很多关于DHCP安全的研究，一些论文提出了诸如四元组安全认证的办法，还有一些论文提出了安全DHCP协议。

例如这篇论文《A Secure DHCP Protocol to Mitigate LAN Attacks》，这算是一篇比较新的论文了，16年发表的，作者提出了2种技术，一种是认证和秘钥管理技术，另一种是消息认证技术（利用数字信号认证Server和Client端的交互信息），可以减轻LAN Attack。
--------------------- 
作者：琳小白 
来源：CSDN 
原文：https://blog.csdn.net/qq_24421591/article/details/50936469 
版权声明：本文为博主原创文章，转载请附上博文链接！

