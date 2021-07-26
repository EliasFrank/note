### redis主从复制

概念

主从复制是指：将一台redis服务器的数据，复制到其他redis服务器。前者称为主节点（master/leader），后者称为从节点（slave/follwer）；数据的复制是单向的，只能由主节点到从节点。Master以写为主，Slave以读为主

默认情况下，每台redis服务器都是主节点；且一个主节点可以有多个从节点（或者没有从节点），但一个从节点只能有一个主节点。

主从复制的作用主要包括：

1. 数据冗余：主从复制实现了数据的热备份，是持久化之外的一种数据冗余方式
2. 故障恢复：当主节点出现问题时，可以由从节点提供服务，实现快速的故障恢复；实际上是一种服务的冗余
3. 负载均衡：在主从复制的基础上，配合读写分离，可以由主节点提供写服务，由从节点提供读服务（即写redis数据时，应用连接主节点，读redis数据时，应用连接从节点）分担服务器负载；尤其是在写少读多的场景下，通过多个从节点分担读负载，可以大大提高redis服务的并发量
4. 高可用（集群）基石：除了上述作用外，主从复制还是哨兵和集群能够实施的基础，因此说主从复制是redis高可用的基础

一般来说，要将redis运用于工程项目中，只使用一台redis是万万不能的，原因如下：

1. 从结构上，单个redis服务器会发生单点故障，并且一台服务器需要处理的请求负载，压力较大

2. 从容量上，单个redis服务器内存容量优先，就算一台redis服务器内存容量为256G，也不能将所有内存用作redis存储内存，一般来说，单台redis最大使用内存不应该超过20G。

   电商网站上的商品，一般都是一次上传的，无数次浏览的，说专业点也就是“多读少写“

   对于这种场景，我们可以使用如下这种架构

   ![1600583160703](C:\Users\hl2333\AppData\Roaming\Typora\typora-user-images\1600583160703.png)

主从复制，读写分离。80%的情况下都是在进行读操作！减缓服务器的压力！架构中经常使用“一主二从”！只要在公司中，主从复制就是必须要使用的，因为在真实的项目中不可能单机使用redis！

### 环境配置

只配置从库，不用配置主库

```bash
127.0.0.1:6379> info replication #查看当前信息
# Replication
role:master  # 角色
connected_slaves:0 
master_replid:757604c66a0ddd32b9f61abc4861d14cfd418627
master_replid2:0000000000000000000000000000000000000000
master_repl_offset:0
second_repl_offset:-1
repl_backlog_active:0
repl_backlog_size:1048576
repl_backlog_first_byte_offset:0
repl_backlog_histlen:0
```

复制三个配置文件，然后修改对应的信息

1. 端口
2. pid名字
3. log文件名字

    **geospatial地理位置**

修改完毕后，启动我们的3个redis服务，可以通过进程查看

---

### 一主二从

默认情况下，每台redis服务器都是主节点；我们一般情况下只用配置从机就好了！

认老大！一主(79)二从(80,81)

```bash
#从机
127.0.0.1:6380> info replication
# Replication
role:slave
master_host:127.0.0.1
master_port:6379
master_link_status:up
master_last_io_seconds_ago:5
master_sync_in_progress:0
slave_repl_offset:0
slave_priority:100
slave_read_only:1
connected_slaves:0
master_replid:71b7c68e57d70c37e5cc9b1b316a65a831b9ed46
master_replid2:0000000000000000000000000000000000000000
master_repl_offset:0
second_repl_offset:-1
repl_backlog_active:1
repl_backlog_size:1048576
repl_backlog_first_byte_offset:1
repl_backlog_histlen:0

#主机
127.0.0.1:6379> info replication
# Replication
role:master
connected_slaves:1
slave0:ip=127.0.0.1,port=6380,state=online,offset=14,lag=1
master_replid:71b7c68e57d70c37e5cc9b1b316a65a831b9ed46
master_replid2:0000000000000000000000000000000000000000
master_repl_offset:28
second_repl_offset:-1
repl_backlog_active:1
repl_backlog_size:1048576
repl_backlog_first_byte_offset:1
repl_backlog_histlen:28
```

真实的从主配置应该在配置文件中配置，这样的话是永久的，我们这里使用的时命令，是暂时的 

 细节：主机可以写从机不能写只能读！主机中的所有信息和数据，都会自动被从机保存

主机写

```bash
127.0.0.1:6379> set k1 v1
OK
```

从机只能读

```bash
127.0.0.1:6381> get k1
"v1"
127.0.0.1:6381> set k2 v2
(error) READONLY You can't write against a read only replica.
```

测试：主机断开连接，从机依旧连接到主机，但是没有写操作，这个时候，主机如果回来了，从机依旧可以直接获取到主机写的信息

如果是使用命令行来配置的主从，这个时候如果重启了，就会变回主机！只要变为从机，立马就会从主机中获取值

**复制原理**

```
slave启动成功连接到master后会发送一个sync命令
master接到命令，启动后台的存盘进程，同时收集说有接收到的用于修改数据集命令，在后台进程执行完毕之后，master将传送整个数据文件到slave，并完成一次完全同步

全量复制：而slave服务在接收到数据库文件数据后，将其存盘并加载到内存中
增量复制：master继续将新的所有收集到的修改命令一次传给slave，完成同步
但是只要是重新连接master，一次完全同步（全量复制）将被自动执行！我们的数据一定可以在从机中看到
```

**层层链路**

上一个M连接下一个S

![1600587676154](C:\Users\hl2333\AppData\Roaming\Typora\typora-user-images\1600587676154.png)

这个时候也可以完成我们的主从复制

**如果没有主机了，这个时候可以手动选择一个主机**

如果主机断开了连接，我们可以使用**slaveof no one**让自己变成主机！其他的节点就可以手动连接到最新的这个主节点（手动）如果这时候主机恢复了，那就重新连接

### 哨兵模式

(自动选举主机的模式)

概述：主从切换技术的方法是：当主服务器宕机后，需要手动把一台从服务器切换为主服务器，这就需要人工干预，费事费力，还会造成一段时间内服务不可用，这不是一种推荐的方式，更多的时候，我们优先考虑哨兵模式。redis从2.8开始正式提供了sentinel（哨兵）架构来解决这个问题

谋朝篡位的自动版，能够后台监控主机是否故障，如果故障了根据投票数自动将从库转换为主库

哨兵模式是一种特殊的模式，首先redis提供了哨兵的命令，哨兵是一个独立的进程，作为进程，他会独立运行，其原理是哨兵通过发送命令，等待redis服务器响应，从而监控运行的多个redis实例

![1600588386080](C:\Users\hl2333\AppData\Roaming\Typora\typora-user-images\1600588386080.png)

这里的哨兵有两个作用

* 通过发送命令，让redis服务器返回监控其运行状态，包括主服务器和从服务器
* 当哨兵检测到master宕机，会自动将slave切换成master，然后通过**发布订阅模式**通知其他的从服务器修改配置文件，让他们切换主机

然而一个哨兵进程对redis服务器进行监控，可能会出现问题，为此，我们可以使用多个哨兵进行监控。各个哨兵之间还会进行监控，这样就形成了多哨兵模式

![1600588773261](C:\Users\hl2333\AppData\Roaming\Typora\typora-user-images\1600588773261.png)

假设主服务器宕机，哨兵1先检测到这个结果，系统并不会马上进行failover过程，仅仅是哨兵1主观的认为主服务器不可用，这个现象称为**主观下线**。当后面的哨兵也检测到主服务器不可用，并且数量达到一定值时，那么哨兵之间就会进行一次投票，投票的结果由一个哨兵发起，进行failover[故障转移]操作，切换成功后，就会通过发布订阅模式，让各个哨兵把自己监控的从服务器实现切换主机，这个过程称为**客观下线**

我们目前的状态是一主二从

1. 配置哨兵配置文件sentinel.conf

   ```bash
   # sentinel monitor 被监控的名称 host port 1
   sentinel monitor myredis 127.0.0.1 6379 1
   ```

   后面的数字1代表主机挂了，slave投票看让谁接替成为主机，票数最多的，就会成为主机

2. 启动哨兵

   ```bash
   redis-sentinel kconfig/sentinel.conf 
   ```

3. 如果master节点断开，这个时候就会从从机中随机选择一个服务器！（这里面有一个投票算法 ）

4. 如果主机此时回来了，只能归并到新的主机下，当做从机，这就是哨兵模式的规则

优点：

1. 哨兵集群，基于主从复制模式，所有的主从配置优点，他全有
2. 主从可以切换，故障可以转移，系统的可用性就会更好
3. 哨兵模式就是主从模式的升级，手动到自动，更加健壮

缺点：

1. redis不好在线扩容，集群容量一旦到达上限，在线扩容就十分麻烦
2. 实现哨兵模式的配置其实是很麻烦的，里面有很多选择
