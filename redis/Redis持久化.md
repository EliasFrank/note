### RDB (Redis Database)

![1600255306387](C:\Users\hl2333\AppData\Roaming\Typora\typora-user-images\1600255306387.png)

在指定的时间间隔内将内存中的数据集快照写入磁盘，也就是行话将的snapshot快照，他恢复时是将快照文件直接读到内存中。

reids会单独创建（fork）一个子进程来进行持久化，会先将数据写入到一个临时文件中，待持久化过程都结束了，再用这个临时文件替换上次持久化好的文件。整个过程中，主进程是不需要进行任何io操作的。这就确保了极高的性能，如果需要进行大规模数据的恢复，且对于数据恢复的完整性不是非常敏感，那RDB方式要比AOF方式更加的高效。RDB的缺点就是最后一次持久化后的数据可能丢失i。我们默认的就是RDB，一般情况下不需要修改这个配置

 

**rdb保存的文件是dump.rdb**都是在我们的配置文件中快照中进行配置的

**触发机制**

1. save的规则满足的情况下，会自动触发rdb规则
2. 执行flushall命令，也会触发我们的rdb规则
3. 退出redis，也会产生rdb文件

> 如何恢复rdb文件

1. 只需要将rdb文件放在我们redis启动目录就可以，redis启动的时候会自动检查dump.rdb恢复其中的数据！

2. 查看需要存在的位置

   ```bash
   127.0.0.1:6379> config get dir
   1) "dir"
   2) "/usr/local/bin"# 如果在这个目录下存在dump.rdb文件，启动就会自动恢复其中的数值
   ```

   几乎他自己的默认配置就够用了，但是我们还是需要去学习

   优点：

   1. 适合大规模的数据恢复
   2. 对数据的完整性要求不高

   缺点：

   1. 需要一定的时间间隔进行进程操作！如果redis意外宕机了，这个最后一次修改数据就没有了
   2. fork进程的时候，会占用一定的内存空间

------

### AOF (Append Only File)

将我们的所有命令都记录下来，history，恢复的时候就把这个文件全部再执行一遍

![1600256510515](C:\Users\hl2333\AppData\Roaming\Typora\typora-user-images\1600256510515.png)

以日志的形式来记录每个写操作，将Redis执行过的所有指令记录下来（读操作不记录），只许追加文件，但不可以改写文件，redis启动之初会读取该文件重新构建数据，换言之，redis重启的话就根据日志文件的内容将写指令从前到后执行一次以完成数据的恢复工作

AOF保存的是appendonly.aof文件

![1600257009454](C:\Users\hl2333\AppData\Roaming\Typora\typora-user-images\1600257009454.png)

默认是不开启的。我们需要手动进行配置！我们只需将appendonly改为yes就开启了aof

重启，redis就可以生效了

如果这个aof文件有错位，这个时候redis是启动不起来的，我们需要修复这个aof文件

redis给我们提供了一个工具 redis-check-aof --fix

![1600395662317](C:\Users\hl2333\AppData\Roaming\Typora\typora-user-images\1600395662317.png)

如果文件正常，重启就可以直接恢复了

```bash
appendonly no #默认是不开启aof模式的，默认是使用rdb方式持久化的，在大部分所有的情况下，rdb完全够用
appengfilename "appendonly.aof" #持久化的文件的名字

#appendfsync always #每次修改都会synchronized，消耗性能
appendfsync everysec #每秒执行一次sync，可能会丢失ls的数据
#appendfsync no #不执行sync，这个时候操作系统自己同步数据，速度最快
```

优点：

1. 每一次修改都同步，文件的完整性都会更加好 
2. 每秒同步一次，可能会丢失一秒的数据
3. 从不同步，效率最高

缺点：

1. 相对于数据文件来说，aof远远大于rdb，修复的速度也比rdb慢
2. aof运行效率也要比rdb慢，所以我们redis默认的配置就是rdb持久化

