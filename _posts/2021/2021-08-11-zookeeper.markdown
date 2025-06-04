---
layout: post
title:  "ZooKeeper入门指南"
category: zookeeper
date:   2021-08-11 23:55:35 +0200
---
## 使用Zookeeper协调分布式应用
本文档包含了让您快速开始使用ZooKeeper的信息。它主要针对希望尝试它的开发人员， 并包含单个ZooKeeper服务器的简单安装说明，一些命令来验证它正在运行，和一个简单的编程示例。最后，为了方便起见， 有一些章节是关于更复杂的安装， 例如，运行副本的部署，并优化事务日志。然而，对于商业部署的完整说明，[请参照](https://zookeeper.apache.org/doc/current/zookeeperAdmin.html)

## 先决条件
[请看管理指南](https://zookeeper.apache.org/doc/current/zookeeperAdmin.html#sc_systemReq)
## 下载
要获得一个ZooKeeper分发版，[从Apache Mirrors下载一个最近稳定的释放版本](http://zookeeper.apache.org/releases.html)
## 独立模式
在独立模式下设置ZooKeeper服务器非常简单。服务器包含在一个JAR文件中，因此安装包括创建配置。
一旦你已经下载稳定释放的版本的Zookeeper，解压它，并cd到根目录
为了启动Zookeeper，你需要一个配置文件，下面是一个示例，在conf/zoo.cfg中创建:
```
tickTime=2000
dataDir=/var/lib/zookeeper
clientPort=2181
```
配置文件的名称随意，但是为了便于讨论，将其命名为conf/zoo.cfg。更改dataDir的值以指定一个现有的(以空开始)目录。下面是每个字段的含义:
- tickTime ：ZooKeeper使用的基本时间单位(毫秒)。 它用于执行心跳，并且最小会话超时将是tickTime的两倍。
- dataDir ：存储内存中数据库快照的位置，以及数据库更新的事务日志(除非另行指定，单独指定事务日志可以优化系统，通过属性dataLogDir)。
- clientPort ：监听客户端连接的端口
现在你已经创建了配置文件，你可以启动ZooKeeper了:
```
bin/zkServer.sh start
```
ZooKeeper使用log4j记录消息 -- 更多详细地可以获得在[Logging 章节](https://zookeeper.apache.org/doc/current/zookeeperProgrammers.html#Logging)对应编程实现。您将看到日志消息打印到控制台(默认)和/或日志文件，具体取决于log4j配置。

这里概述的步骤是在独立模式下运行ZooKeeper。没有副本，如果Zookeeper进程失败，服务就要中断。这对于大多数开发情况来说是很好的，但要以副本模式运行ZooKeeper，[请看](https://zookeeper.apache.org/doc/current/zookeeperStarted.html#sc_RunningReplicatedZooKeeper)
## 管理Zookeeper存储
对于长时间运行的生产系统，必须对ZooKeeper存储进行外部管理 (dataDir and logs)，[更多详细的情况请看这个章节](https://zookeeper.apache.org/doc/current/zookeeperAdmin.html#sc_maintenance)
## 客户端连接到ZooKeeper
```
$ bin/zkCli.sh -server 127.0.0.1:2181
```
这允许您执行简单的、类似于文件的操作。

一旦你连接上了，你应该会看到如下内容:
```
Connecting to localhost:2181
log4j:WARN No appenders could be found for logger (org.apache.zookeeper.ZooKeeper).
log4j:WARN Please initialize the log4j system properly.
Welcome to ZooKeeper!
JLine support is enabled
[zkshell: 0]
```
在shell中，输入help以获取可从客户端执行的命令列表，例如:
```
[zkshell: 0] help
ZooKeeper -server host:port cmd args
addauth scheme auth
close
config [-c] [-w] [-s]
connect host:port
create [-s] [-e] [-c] [-t ttl] path [data] [acl]
delete [-v version] path
deleteall path
delquota [-n|-b] path
get [-s] [-w] path
getAcl [-s] path
getAllChildrenNumber path
getEphemerals path
history
listquota path
ls [-s] [-w] [-R] path
ls2 path [watch]
printwatches on|off
quit
reconfig [-s] [-v version] [[-file path] | [-members serverID=host:port1:port2;port3[,...]*]] | [-add serverId=host:port1:port2;port3[,...]]* [-remove serverId[,...]*]
redo cmdno
removewatches path [-c|-d|-a] [-l]
rmr path
set [-s] [-v version] path data
setAcl [-s] [-v version] [-R] path acl
setquota -n|-b val path
stat [-w] path
sync path
```
从这里开始，您可以尝试一些简单的命令，以了解这个简单的命令行界面。首先，发出list命令(如ls)，结果如下:
```
[zkshell: 8] ls /
[zookeeper]
```
接下来，通过运行create /zk_test my_data创建一个新的znode。这将创建一个新的znode并将字符串“my_data”与该节点关联起来。您应该看到:
```
[zkshell: 9] create /zk_test my_data
Created /zk_test
```
注意，现在已经创建了zk_test目录。

接下来，通过运行get命令验证数据是否与znode相关联，如:
```
[zkshell: 12] get /zk_test
my_data
cZxid = 5
ctime = Fri Jun 05 13:57:06 PDT 2009
mZxid = 5
mtime = Fri Jun 05 13:57:06 PDT 2009
pZxid = 5
cversion = 0
dataVersion = 0
aclVersion = 0
ephemeralOwner = 0
dataLength = 7
numChildren = 0
```
我们可以通过发出set命令来更改与zk_test关联的数据，如下所示:
```
[zkshell: 14] set /zk_test junk
cZxid = 5
ctime = Fri Jun 05 13:57:06 PDT 2009
mZxid = 6
mtime = Fri Jun 05 14:01:52 PDT 2009
pZxid = 5
cversion = 0
dataVersion = 1
aclVersion = 0
ephemeralOwner = 0
dataLength = 4
numChildren = 0
[zkshell: 15] get /zk_test
junk
cZxid = 5
ctime = Fri Jun 05 13:57:06 PDT 2009
mZxid = 6
mtime = Fri Jun 05 14:01:52 PDT 2009
pZxid = 5
cversion = 0
dataVersion = 1
aclVersion = 0
ephemeralOwner = 0
dataLength = 4
numChildren = 0
```
注意，我们在设置数据后做了一个get，它确实发生了变化。

最后，让我们通过以下命令删除节点:
```
[zkshell: 16] delete /zk_test
[zkshell: 17] ls /
[zookeeper]
[zkshell: 18]
```
到此为止。要了解更多信息,请看[ Zookeeper CLI](https://zookeeper.apache.org/doc/current/zookeeperCLI.html).

## ZooKeeper的编程
ZooKeeper有Java绑定和C绑定。它们在功能上是等价的。C绑定有两种变体:单线程和多线程。它们的区别仅在于消息传递循环是如何完成的。更多的消息，看这个[ Programming Examples in the ZooKeeper Programmer's Guide ](https://zookeeper.apache.org/doc/current/zookeeperProgrammers.html#ch_programStructureWithExample)  使用不同api的示例代码。

## 运行Zookeeper在副本模式
以独立模式运行ZooKeeper便于评估、一些开发和测试。但是在生产中，应该以副本模式运行ZooKeeper。在相同应用程序中一组副本服务器叫做`仲裁或法定人数`(quorum),在副本模式下，仲裁中的所有服务器都有相同配置文件的副本。

注意：对于副本模式， 至少需要三个服务器， 强烈建议您使用奇数个服务器。 如果您只有两个服务器，那么您将处于这样一种情况:如果其中一个服务器出现故障，则没有足够的机器来组成多数quorum。两个服务器本质上比单个服务器更不稳定，因为存在两个单点故障。

副本模式所需的conf/zoo.cfg文件与独立模式中使用的文件类似，但有一些不同。下面是一个例子:
```
tickTime=2000
dataDir=/var/lib/zookeeper
clientPort=2181
initLimit=5
syncLimit=2
server.1=zoo1:2888:3888
server.2=zoo2:2888:3888
server.3=zoo3:2888:3888
```
新条目initLimit是ZooKeeper用来限制仲裁ZooKeeper服务器连接leader的时间长度的超时。syncLimit条目限制了服务器从leader发出的超时时间。

对应这两个超时，使用tickTime指定时间单位。在这个例子，在本例中，initLimit的超时是5个节拍(每节拍2000毫秒)，即10秒。

表单服务器的条目。列出组成ZooKeeper服务的服务器。当服务器启动时，它通过在数据目录中查找文件myid来知道它是哪个服务器。该文件包含服务器号，用ASCII码表示。

最后，注意每个服务器名后面的两个端口号:“2888”和“3888”。对等体使用前一个端口连接其他对等体。这样的连接是必要的，以便对等端可以通信，例如，就更新的顺序达成一致。更具体地说，ZooKeeper服务器使用这个端口连接follower和leader。当一个新的leader出现时，一个follower使用这个端口打开一个到leader的TCP连接。因为默认的leader选举也使用TCP，所以我们目前需要另一个端口来进行leader选举。这是服务器条目中的第二个端口。

 `注意：`

如果您想在一台机器上测试多个服务器，请将服务器名指定为localhost，为每个服务器指定唯一的quorum和leader选举端口(即上面示例中的2888:3888、2889:3889、2890:3890)。在该服务器的配置文件中。当然，分开的_dataDir_s和不同的_clientPort_s也是必要的(在上面复制的示例中，运行在单个本地主机上，仍然有三个配置文件)。

请注意，在一台机器上设置多个服务器不会产生任何冗余。如果发生了导致机器死亡的事情，所有的zookeeper服务器将会离线。完全冗余要求每个服务器都有自己的机器。它必须是一个完全独立的物理服务器。同一物理主机上的多个虚拟机仍然容易受到该主机完全故障的影响。

 如果您的ZooKeeper机器中有多个网络接口，您还可以指示ZooKeeper绑定所有的接口，并在网络故障时自动切换到健康的接口。具体请参见[配置参数](https://zookeeper.apache.org/doc/current/zookeeperAdmin.html#id_multi_address)。


## 其他优化
还有一些其他的配置参数可以大大提高性能:
- 为了降低更新延迟，有一个专用的事务日志目录是很重要的。默认情况下，事务日志与数据快照和myid文件放在同一个目录中。dataLogDir参数表示用于事务日志的不同目录。