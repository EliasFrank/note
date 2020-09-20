redis发布订阅（pub/sub）是一种消息通信模式，发送者（pub）接收消息。微信、微博、关注系统！

redis客户端可以订阅任意数量的频道

订阅/发布消息图：

第一个：消息发送者， 第二个：频道， 第三个：消息订阅者

![1600404591180](C:\Users\hl2333\AppData\Roaming\Typora\typora-user-images\1600404591180.png)

测试：

订阅端：

```bash
127.0.0.1:6379> subscribe hl #订阅一个频道 hl
Reading messages... (press Ctrl-C to quit)
1) "subscribe"
2) "hl"
3) (integer) 1
#等待读取推送的信息
1) "message" # 消息
2) "hl" # 哪个频道的消息
3) "hello" # 消息的具体内容 
1) "message"
2) "hl"
3) "world"
```

发送端：

```bash
127.0.0.1:6379> publish hl hello # 发布者发布消息到频道
(integer) 1
127.0.0.1:6379> publish h1 jj # 发布者发布消息到频道，但是这个频道无人订阅，所以是0
(integer) 0 
127.0.0.1:6379> publish hl world # 发布者发布消息到频道
(integer) 1
```

 原理：

```
redis是使用c语言实现的，通过分析redis源码里的pubsub.c文件，了解发布和订阅机制的底层实现，借此加深对redis的理解。
reids荣国publish、subscribe和pusubscribe等命令实现发布和订阅功能
通过subscribe命令订阅某个频道后，redis-server里维护了一个字典，字典的键就是一个个channel，而字典的值则是一个链表，链表中保存了所有订阅这个channel的客户端，subscribe命令的关键，就是将客户端添加到给定channel的订阅链表中
pub/sub从字面上理解就是发布（publish）和订阅（subscribe），在redis中，你可以设定对某一个key值进行消息发布及消息订阅，当一个keu值上进行了消息发布后，所以订阅他的客户端都会收到对应的消息。这一功能最明显的用法就是用作实时消息系统， 比如普通的即时聊天，群聊等功能
```

使用场景：

```
实时消息系统
实时聊天（频道当做聊天室，将信息回显给所有人）
订阅，关注系统
稍微复杂的场景我们就会使用消息中间件MQ（）
```

