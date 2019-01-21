tshark:
tshark -D   //输出可用于捕获的接口
tshark -i  eth0  //捕获eth0接口
tshark -i  lo  //捕获环回接口流量


Windows系统添加环回适配器：
1、在命令提示符下运行hdwwiz 
2. 单击“下一步”，然后选择手动设备选择选项（“高级”）。 
3. 选择“网络适配器”作为硬件类型，然后单击“ 下一步”。 
4. 选择Microsoft作为制造商，然后选择Microsoft Loopback Adapter作为网络适配器

您可以在Windows上嗅探发往本地主机的流量，而无需安装环回适配器。Netresec有一个名为RawCap的公共工具，
可以用来嗅探具有IP地址的Windows机器上的任何接口，特别是可以嗅探发往目的地的流量127.0.0.1 。RawCap输出为pcap格式




在Linux Bridge上嗅探
Linux桥接支持内置于内核中，但要开始使用它，您需要安装支持实用程序。对于基于Debian / Ubuntu的系统，请安装包bridge-utils ：  
localhost＃apt-get install bridge-utils 

并为基于Red Hat的系统执行以下操作：
localhost #yum install bridge-utils

要使用计算机的eth1和eth2 创建名为testbr的网桥，请使用以下命令：

root @ pickaxe：〜＃brctl addbr testbr 
root @ pickaxe：〜＃brctl addif testbr eth1 
root @ pickaxe：〜＃brctl addif testbr eth2 
root @ pickaxe：〜＃ifconfig eth1 up promiscuous 
root @ pickaxe：〜＃ifconfig eth2 up promiscuous 
root @pickaxe：〜＃ifconfig testbr up

加载和保存捕获文件
使用Wireshark在GUI中查看数据包或者在TShark中观看它们滚动是很棒的。但是，有时Wireshark并不是您想要用于数据包分析的唯一工具。数据包捕获可以来自不同工具生成的不同来源，并保存为不同的格式。Wireshark支持保存常见的pcap格式和读取/保存各种专有格式。
您无法保存正在运行的捕获，因此为了节省流量，您需要使用菜单或单击工具栏中的“停止”按钮来停止捕获; 否则，“保存”按钮或菜单选项将显示为灰色。停止正在运行的捕获会话后，您可以通过选择文件⇨ 保存或按Ctrl + S 来保存它。这将显示“保存”对话框，您可以在其中选择数据包捕获的文件名，目标路径和输出格式。
同样，在线可以进行非常有趣的数据包捕获以进行加载和分析。虽然大多数迹线都保持在最小尺寸和通用格式，但您可能会发现一些需要额外注意。  
文件格式
自Wireshark 1.8版以来，默认的输出格式是PcapNG，这是WinPcap开发的一种新格式。

实际上，pcap是一种序列化网络流量数据的方法，尽管它可用于序列化任何内容。它只是标准给出的字节顺序。
关于pcap格式的一个很好的参考是在Wireshark wiki上，网址是https://wiki.wireshark.org/Development/LibpcapFileFormat。
它实际上是一种非常简单的文件格式。有一个全局标题，包括一个幻数（应用程序如何识别它是一个pcap文件），文件所在的pcap版本，时区偏移，时间戳的准确性（例如秒与微秒），捕捉长度，
这是为每个数据包捕获的数据量，最后是从中捕获数据包数据的网络类型（以太网，IP等）。
然后，该全局标头之后是第一个数据包的数据包标头。捕获的每个数据包都有一个数据包标头。数据包标头包含有关数据包的元数据，例如以秒和微秒为单位的时间戳，捕获的数据包数据的长度以及数据包的实际长度。
如果您之前记得，这就解释了为什么“数据包详细信息”窗格包含一个“帧”列，该列告诉您捕获的字节数与实际传输的字节数。
Wireshark能够从pcap文件中解析出这一切。在pcap标头之后，您有实际的数据包/帧数据。有什么关于pcap的真棒是它实际上是一种非常简单的格式，这意味着即使没有某种高级库也很容易构建自己的pcap文件。
这实际上是方法我们采用了本书中开发的一些自定义嗅探应用程序。
现在您了解了pcap，应该清楚的是，在进行实时嗅探时，Wireshark正在从Dumpcap中读取pcap格式的数据。Dumpcap如何从实际网卡获取数据因操作系统甚至所使用的网络类型和网卡而异。
在Windows中，您几乎总是会使用WinPcap。WinPcap是一个库，允许您实际捕获网卡中的原始数据包数据，然后将其格式化为pcap格式。
在Windows中，Dumpcap将使用WinPcap库，而在Linux上，它通常将使用libpcap。Libpcap是原始的数据包捕获库，几乎可用于任何库* nix系统，是一个编程库，允许您将原始网络数据格式化为pcap。
（libpcap开发人员实际上发明了pcap格式。）

合并多个文件：
mergecap -w 30SecCap 10SecRing_00003_20161006110657 10SecRing_00004_20161006110707 10SecRing_00005_20161006110717
该-w 开关告诉mergecap到输出文件，在我们的例子名为“30SecCap”。您将输出文件与要合并的文件一起使用。而已！ 
如果你使用-v verbose开关，mergecap会告诉你每个文件的格式类型，在我们的例子中是pcapng

解剖器：
第一个解剖器始终是框架解剖器。它补充说时间戳并将原始字节传递给下一个最低协议解析器 - 通常是以太网。Wireshark使用表的组合，
其中包含构建哪些协议，其他协议与端口号之类的启发式结合，以决定将哪个解析器应用于数据包。一些协议，如以太网，有一个字段，
说明它封装了哪个协议，因此不需要启发式，Wireshark可以轻松地为工作选择正确的解剖器

解剖非标准HTTP流量.pcap数据包:
应用显示过滤器为tcp.port==1080

smb.pcap文件数据包过滤：
smb.file and smb.cmd == 0xa2 and smb.flags.response == 0

tshark -2 -R "smb.file and smb.cmd == 0xa2 and smb.flags.response
== 0"  -T fields -e smb.file -r smb_test.dump \ | sort | uniq -c

着色规则.pcap：
更改arp过滤规则的颜色

wireshark的pcap文件来源：https://wiki.wireshark.org/SampleCaptures
安全专业人士存储库：http://www.netresec.com/?page=PcapFiles
更进一步，您可以使用一组颜色规则集。在以下地址的Wireshark站点上，您将找到Wireshark社区成员针对各种场景发送的规则集：
https://wiki.wireshark.org/ColoringRules



演习
1. 执行两次捕获，一次处于混杂模式，另一次不处于混杂模式。仅在混杂模式下捕获的跟踪中查找任何数据包。什么数据包详细信息让您确定跟踪是如何完成的？  
混杂模式会收到源或目的地址均不是本机IP或mac地址的非广播数据包，由此判断处于混杂模式。而非混杂模式只收到包含本机IP或MAC或广播地址的数据包。

2. 是否有可用于排除localhost作为源或目标的显示过滤器？  
不存在用于排除localhost的显示过滤器，但可以捕获作为环回接口loopback0的包含localhost流量。

3. 找到数据包转储中的ARP流量，并确保应用正确的解剖器。  
应用ARP/RARP解剖器可以解析ARP流量。

4. 设计显示过滤器，以帮助您查看另一台计算机首次连接到网络时的DHCP请求和响应流量。 
应用bootp表达式查看一台计算机首次连接网络的DHCP请求和响应流量。

5. 嗅探仅主机网络，NAT网络和桥接网络。 
仅主机网络的虚拟机IP地址与主机地址不在同一网段，可以嗅探虚拟机属于同一网段之间的流量，不可以访问主机和外网。
NAT网络的虚拟机IP地址与主机地址一般不属于同一网段，可以嗅探到虚拟机之间的流量，主机和虚拟机之间的流量和访问外网的流量。
桥接网络的虚拟机IP地址与主机地址同一网段，可以嗅探到虚拟机之间的流量，主机和虚拟机之间的流量和访问外网的流量。

6. 嗅探一些加密的WiFi流量。你怎么看？ 
一些加密的WiFi流量的数据部分为密文不可解析的字符串。

7. 使用Linux桥接设置您自己的仅主机网络。（提示：您可以使用连接到Linux网桥的TUN / TAP，然后将虚拟机桥接到这些接口。）  
通过连接linux网桥的TUN/TAP,将虚拟机桥接到这些接口。
