## 数据结构与对象

### 简单动态字符串

Simple Dynamic String

```c
struct sdshdr {
    //记录buf数组中已使用字节的数量
    //等于SDS所保存字符串的长度
    int len;
    
    //记录buf数组中未使用的字节的数量
    int free;
    //字节数组，用于保存字符串
    char[] buf;
}
```

![image-20210721165706926](H:\Notes\redis\upload\image-20210721165706926.png)

##### SDS与C字符串的区别

1. 常数复杂度获取字符串长度

2. 杜绝缓冲区溢出

3. 减少修改字符串时带来的内存重分配次数

    1. 空间预分配

        * 如果对SDS**修改之后**，SDS长度将小于1MB，则程序分配和len属性同样大小的未使用空间。
        * 如果修改之后，SDS长度大于等于1MB，则分配1MB的未使用空间

    2. 惰性空间释放

        当SDS的API需要缩短SDS保存的字符串时，程序并不立即使用内存重分配来回收缩短后多出来的字节，而是使用free属性将这些字节的数量记录起来，并等待将来使用

4. 二进制安全

    SDS的API都会以二进制的方式来处理buf数组里面的数据，程序不会对其中的数据做任何限制、过滤或者假设，数据在写入时是怎么样的，他被读取时就是什么样。

5. 兼容部分C字符串函数

### 链表

```c
typedef struct listNode{
    //前置节点
    struct listNode* prev;
    //后置节点
    struct listNode* next;
    //节点的值
    //void的字面意思是“无类型”，void *则为“无类型指针”，void *可以指向任何类型的数据。
    void *value;
}listNode
```

![image-20210722151801892](H:\Notes\redis\upload\image-20210722151801892.png)

```c
typedef struct list {
    //表头节点
    listNode* head;
    //表尾节点
    listNode* tail;
    //链表锁包含的节点数量
    unsigned long len;
    //节点值复制函数
    void *(*dup)(void* ptr);
    //节点值释放函数
    void (*free)(void *ptr);
    //节点值对比函数
    int (*match)(void *ptr, void *key);
}list;
```

![image-20210722153333457](H:\Notes\redis\upload\image-20210722153333457.png)

**特性**

双端、无环、带表头指针和表尾指针、带链表长度计数器、多态

### 字典

#### 哈希表

```c
typedef struct dicht {
    //哈希表数组
    dicEntry **table;
    //哈希表大小
    unsigned long size;
    //哈希表大小掩码，用于计算索引值，总是等于size-1
    unsigned long sizemask;
    //该哈希表已有节点的数量
    unsigned long used;
} dictht;
```

![image-20210722155316297](H:\Notes\redis\upload\image-20210722155316297.png)

#### 哈希表节点

```c
typedef struct dictEntry {
	//键
    void *key;
    
    //值
    union {
        void *val;
        uint64_tu64;
        int64_ts64;
    }v;
    
    //指向下个哈希表节点，形成链表
    struct dictEntry *next;
} dictEntry;
```

![image-20210722160009080](H:\Notes\redis\upload\image-20210722160009080.png)

#### 字典

```c
typedef struct dict {
    //类型特定函数
    dictType *type;
    //私有数据
    void *privdata;
    //哈希表
    dictht ht[2];
    //rehash索引，当rehash不在进行时，值为-1
    int trehashidx;
}dict;

typedef struct dictType {
    //计算哈希值的函数
    unsigned int (*hashFunction)(const void *key);
    //复制键的函数
    void *(*keyDup)(void *privdata, const void *key);
    //复制值的函数
    int (*keyCompare)(void *privdata, void *key);
    //对比键的函数
    void (*valDestructor)(void *privdata, void *key);
    //销毁键的函数
    void (*keyDestructor)(void *privdata, void *obj));
    //销毁值的函数
    void (*valDestructor)(void *privdata, void *obj);
}dictType;
```

![image-20210722160700839](H:\Notes\redis\upload\image-20210722160700839.png)

**解决键的冲突**

和java中的HashMap一样，不过redis使用的是头插法

**rehash**

redis对字典的哈希表执行rehash的步骤如下

1. 为字典ht[1]哈希表分配空间，空间大小取决于要执行的操作，以及ht[0]当前包含的键值对数量（ht[0].used属性的值）
    1. 如果执行的是扩展操作，那么ht[1]的大小为第一个大于等于ht[0].used*2的2的n次方幂
    2. 如果执行的时收缩操作，那么ht[1]的大小为第一个大于等于ht[0].used的2的n次方幂
2. 将保存在ht[0]中的所有键值对都rehash到ht[1]上面
3. 当ht[0]包含的所有键值对都迁移到了ht[1]之后（ht[0]变成空表），释放ht[0]，将ht[1]设置为ht[0]，并在ht[1]新创建一个空白哈希表，为下一次rehash做准备

#### 哈希表的扩展与收缩

负载因子 = 哈希表已保存节点数量 / 哈希表大小

load_factor = ht[0].used / ht[0].size

当以下条件中的任意一个被满足时，程序会自动开始对哈希表执行扩展操作

1. 服务目前没有在执行BGSAVE指令或者BGREWRITEAOF命令，并且哈希表的负载因子大于等于1

2. 服务目前正在执行BGSAVE或者BGREWRITEAOF命令，并且哈希表的负载因子大于等于5

    **根据BGSAVE指令或者BGREWRITEAOF命令是否正在执行，服务器执行扩展操作所需的负载因子并不相同，这是因为在执行BGSAVE指令或者BGREWRITEAOF命令的过程中，redis需要创建当前服务器进程的子进程而大多数操作系统都采用写时复制技术来优化子进程的使用效率，所以在子进程存在期间，服务器会提高执行扩展操作所需的负载因子，从而尽可能的避免在子进程存在期间进行哈希表扩展操作，这可以避免不必要的内存写入操作，最大限度地节约内存**

    ==另一方面，当哈希表的负载因子小于0.1时，程序自动开始对哈希表执行收缩操作==

#### 渐进式rehash

为了避免rehash对服务器性能造成影响，服务器不是一次性将ht[0]里面的所有键值对全部rehahs到ht[1]，而是分多次、渐进式地将ht[0]里面的键值对慢慢地rehash到ht[1]

以下是哈希表渐进式rehash的详细步骤

1. 为ht[1]分配空间，让字典同时持有ht[0]和ht[1]两个哈希表
2. 在字典中维持一个索引计数器变量rehashidx，并将他设置为0，表示rehash工作正式开始
3. 在rehash进行期间，每次对字典执行添加、删除、查找或者更新操作时，程序除了执行指定的操作以外，还会顺带将ht[0]哈希表在rehashidx索引上的所有键值对rehash到ht[1]，当rehahs工作完成之后，程序将rehahsidx属性的值增一
4. 随着字典操作的不断执行，最终在某个时间点上，ht[0]的所有键值对都会被rehash到ht[1]，这时程序将rehashidx属性的值设为-1，表示rehash操作已完成

渐进式rehash的好处在于他采取分而治之的方式，将rehash键值对所需的计算工作均摊到字典的每个添加、删除、查找和更新操作上，从而避免了集中式rehahs而带来的庞大计算量

**渐进式rehash执行期间的哈希表操作**

  因为在进行渐进式rehash的过程中，字典会同时使用ht[0]和ht[1]两个哈希表，所以在渐进式rehash进行期间，字典的删除、查找、更新等操作会在两个哈希表上进行。例如，要在字典里面查找一个键的话，程序会现在ht[0]里面进行查找，如果没找到的话，就会继续到ht[1]里面进行查找

  另外，在渐进式rehash执行期间，新添加到字典的键值对一律会被保存到ht[1]里面，而ht[0]则不再进行任何添加操作，这一措施保证了ht[0]包含的键值对数量会只减不增，并随着rehash操作的执行而最终变成空表

### 跳跃表

skiplist是一种有序数据结构，它通过在每个节点中维持多个指向其他节点的指针，从而达到快速访问节点的目的

redis只在两个地方用到了跳跃表，一个是实现有序集合键，另一个是在集群节点中用作内部数据结构，除此之外，跳跃表在redis里面没有其他用途

#### 跳跃表节点

```c
typedef struct zskiplistNode{
    //层
    struct zskiplistLevel {
        //前进指针
        struct zskiplistNode *forward;
        //跨度
        unsigned int span;
    } level[];
    
    //后退指针
    struct zskiplistNode* backward;
    //分值
    double score;
    //成员对象
    robj *obj;
} zskiplistNode;
```

1. 层

      跳跃表节点的level数组可以包含多个元素，每个元素都包含一个指向其他节点的指针，程序可以通过这些层来加快访问其他节点的速度，一般来说，层的数量越多，访问其他接地那的速度就越快

      每次创建一个新跳跃表及节点的时候，程序都根据幂次定理随机生成一个介于1和32之间的值作为level数组的大小，这个大小就是层的“高度”

2. 前进指针

    每个层都有一个指向表尾方法的前进指针(level[i].forward属性)，用于从表头向表尾方向访问节点。

3. 跨度

    层的跨度(level[i].span属性)用于记录两个节点之间的距离

    * 两个节点之间的跨度越大，他们相距的就越远
    * 指向null的所有区前进指针的跨度都为0，以为他们没有连向任何节点

4. 后退指针

    节点的后退指针(bacward属性)用于从表尾向表头方向访问节点；跟可以一次跳过多个节点的前进指针不同，因为每个节点只有一个后退指针，所以每次只能后退至前一个节点。

5. 分值和成员

    ​    节点的分值(score属性)是一个double类型的浮点数，跳跃表中的所有节点都按分值从小到大来排序

    ​    节点的成员对象(obj属性)是一个指针，它指向一个字符串对象，而字符串对象则保存着一个SDS值

    ​    在同一个跳跃表中，各个节点保存的成员对象必须是唯一的，但是多个节点保存的分支却可以是相同的。分值相同的节点将按照成员对象在字典中的大小来进行排序，成员对象较小的节点会排在前面（靠近表头的方向），而成员对象较大的节点则会排在后面（靠近表尾的方向）

### 整数集合

### 压缩列表

### 对象