# Redis

### 开篇

概念：远程服务字典 remote dictionary service，是一个存储中间件。

用来做什么：缓存，分布式锁等。

- 缓存：

  - 概念：是一种高速数据交换存储器，先于内存和CPU交互，速度比较快。

  - 结构：
    - 一级缓存L1 cache：对CPU性能影响较大，静态RAM组成，容量32--256KB
    - 二级缓存L2 cache ：内部和外部两种芯片，内部速率=主频，外部=0.5主频。容量128KB--3M
  - 三级缓存L3 cache：
    
  - 工作原理：CPU要读取数据，先从1级缓存中找，找到就立即读取并送给CPU处理。没找到，就从内存中读取并送给CPU，同时把数据所在的数据块调入缓存中。
  - 磁盘缓存的作用：
    - 预读取，将连续的几个簇中的数据读到缓存中
    - 写入，当写入数据到硬盘上的时候，先将数据保存在缓存中，发信号给系统继续执行，当硬盘空闲时再写入盘片上。若掉电磁头则借助惯性写入暂存区。
    - 临时存储，将频繁访问的数据放在缓存中，提升读写数据效率

- 分布式锁：
  - 概念：为了解决分布式环境下出现的并发问题，需要使用分布式锁
  - 区别：普通锁是多个线程间的并发问题，通过lock和synchronized等，使用内存标记来解决问题。分布式锁是多个设备间的多个进程下的并发问题，所以必须借助Redis等中间件实现。

### 安装

- yum 安装 输入 yum install redis ,显示没有软件包，先安装epel仓库yum install epel-release,安装完成后重新安装Redis
- 配置：
  - 需要启动Redis服务才行，systemctl start redis
  - 开机启动 ，systemctl enable redis
  - 修改配置文件，/etc/redis.conf 。主要修改端口号 port 、密码 requirepass （用户名默认auth）、保护模式protected-mode no
- 连接 ：在usr/bin 目录下，执行Redis-cli -h 127.0.0.1 -p 6379

![img](C:\Users\fanfan\AppData\Local\Temp\企业微信截图_16118181491616.png)

- 练习基本指令

  

### 基础

内部数据结构：string、list、hash、set、zset(sorted set)五种，都是key value格式，唯一的key字符串作为名称

- **string** ：![image-20210125152209361](C:\Users\fanfan\AppData\Roaming\Typora\typora-user-images\image-20210125152209361.png)

  - 用途：常用来缓存用户信息——将对象信息序列化成json字符串，塞入Redis缓存，使用时反序列化。
  - 内部实现原理：类似Arraylist,可以动态调整大小，预分配的容量大小比长度len长，为了减少内存的频繁分配。（字符串长度最大512M，当小于1M时，扩容直接翻倍，大于1M后每次扩容1M）
  - 常用命令：

  ```xml
  set 　　 // 设置key
  get     // 获取key
  append  // 追加string
  mset    // 设置多个键值对
  mget    // 获取多个键值对
  del     // 删除key
  incr    // 递增+1
  decr    // 递减-1
  ```

  ```lua
  > set name code --设置名字为code
  OK --设置成功
  > get name --获取名字
  "code" --为上面设置的code
  > exists name --是否存在
  (interger) 1 --存在1个
  > del name 
  (interger) 1 --删除1个
  > mset name code len 5 --设置名字为code，长度为5
  >mget name len --取出名字和长度，若不存在key,则value=（nil）
  1）"code"
  2）"5"
  > expire name 5 --5S后过期，再找name为nil
  > setex name 5 code --等同于上面先set name再设置过期
  > setnx name code --若不存在name则创建，存在则不创建 == set not exists
  > incr len --len为整数，自增 范围为signed long 最大最小 42亿——0
  "6" --数字加1
  > decr len --自减 和自增一样
"5"
  ```

- **list** ：

  - 概念：相当于linkedlist，链表插入删除操作快，时间复杂度O(1)，索引定位慢O(n)
  - 用法：

  ```
  lpush           // 从列表左边插
  rpush           // 从列表右边插
  lrange          // 获取一定长度的元素  lrange key  start stop
  ltrim           // 截取一定长度列表  (star_index,end_index) 
  lpop            // 删除最左边一个元素
  rpop            // 删除最右边一个元素
  lpushx/rpushx   // key存在则添加值，不存在不处理
  lindex          // get(index)
  ```

  ```lua
  --队列，先进先出，右进左出
  rpush books python java golang --右边插入3个数据
  (integer) 3
  > llen books --长度
  (integer) 3
  > lpop books -- 从左边弹出数据
  "python"
  > lpop books
  "java"
  > lpop books
  "golang"
  > lpop books
  (nil)
  --栈，先进后出，右进右出
  > rpush books python java golang
  (integer) 3
  > rpop books --右边弹出
  "golang"
  > rpop books
  "java"
  > rpop books
  "python"
  > rpop books
  （nil）
  -- index = -1,表示最后一个元素 
  > rpush books python java golang
  (integer) 3
  > lindex books 1 --查找索引为1的元素
  "java"
  > lrange books 0 -1 --获取第0个到倒数第一个，即全部
  1) "python"
  2) "java"
  3) "golang"
  > ltrim books 1 -1 --截取第一个到最后一个
  OK
  > lrange books 0 -1 -- 就剩俩了
  1) "java"
  2) "golang"
  > ltrim books 1 0 --清空了整个列表，因为区间范围长度为负
  OK
  > llen books --长度为0
  (integer) 0
  ```

  - 原理：（未查）
    - 快速链表
    - 压缩链表

- **hash:**

  - 概念：相当于hashmap,是无序字典。内部数组+链表结构，当出现哈希碰撞时，将碰撞的元素用链表相连。数组叫桶，初始值为2^4=16,每个元素有一个唯一的hash值，将hash值与桶取余即存放位置。

    ![image-20210125235215541](C:\Users\fanfan\AppData\Roaming\Typora\typora-user-images\image-20210125235215541.png)

  - 区别：hashmap可以存放任意类型数据，hash存放的值是字符串;rehash的方式不同,redis是渐进式，java是一次性。![image-20210126000017645](C:\Users\fanfan\AppData\Roaming\Typora\typora-user-images\image-20210126000017645.png)

  渐进式就是同时存在两个hash结构，一大一小，然后后续再慢慢迁移（怎么迁移？）

  - 用途：存放用户信息，和字符串相比不用一次性序列化全部信息，可以每个字段单独存储
  - 命令：

  ```
  hset       // 设置散列值
  hget       // 获取散列值
  hmset      // 设置多对散列值
  hmget      // 获取多对散列值
  hsetnx     // 如果散列已经存在，则不设置（防止覆盖key）
  hkeys      // 返回所有keys
  hvals      // 返回所有values
  hlen       // 返回散列包含域（field）的数量
  hdel       // 删除散列指定的域（field）
  hexists    // 判断是否存在
  ```

  ```lua
  -- 需要注意两个点
  hset key field value --当value中字符串有空格时，必须加引号
hincrby key field increment -- 用法和incr差不多，但是后面必须添加增量值（每次增加几），否则报错
  ```
  

![20210131172729](C:\Users\fanfan\Documents\Scrshot\20210131172729.png)

- **set**

  - 相当于hashset ,内部无序但是唯一，可以存储中奖号码

  ```xml
  sadd        // 添加元素
  srem        // 删除元素 
  sismember   // 判断是否为set的一个元素
  smembers    // 返回集合所有的成员
  sdiff       // 返回一个集合和其他集合的差异
  sinter      // 返回几个集合的交集
  sunion      // 返回几个集合的并集
  ```

  ```lua
  -- 无序性，添加是a,b,c,d,查看时候是b,d,c,a
  -- srem 和 spop区别：srem是删除具体元素，srem key member [member...];spop是从最上面弹出几个元素，spop key [count]
  --scard key 长度，相当于count
  -- 集合之间的交并运算
  ```

  ![20210131230158](C:\Users\fanfan\Documents\Scrshot\20210131230158.png)

- **zset**

  - 有序集合，类似于sortedset和hashmap结合，内部实现是跳表结构。具有set的唯一性，又有score作为权重排序。
  - 可以用来存储粉丝列表，id是唯一的，根据关注时间进行排序。还可以用来存储学生成绩，血生id唯一，按照分数排序。

  ```xml
  ZADD         // 添加有序集合
  ZREM         // 删除有序集合中的元素
  ZREVRANGE    // 倒叙
  ZRANGE       // 正序
  ZCARD        // 有序集合的基数
  ZSCORE       // 返回成员的值
  ZRANK        // 返回有序集合中成员的排名
  ```

  ```lua
  -- 需要注意的是score 分数是双精度的，可能会后面很多位
  -- 开始位置和结束位置 0和-1
  ```

  ![20210131234050](C:\Users\fanfan\Documents\Scrshot\20210131234050.png)

  - 跳表：分层级，引用数组的二分查找法插入元素，从L0到L31
  
  

### 应用1：分布式锁

![image-20210201215103664](C:\Users\fanfan\AppData\Roaming\Typora\typora-user-images\image-20210201215103664.png)

- 为了解决分布式多个客户端多个进程之间的并发问题。简单来说就是通过Redis 中间件来get和set数据，然后在Redis中添加锁，保证数据安全。

  ```lua
  setnx lock: codehole true
  ```

- 有锁就要释放锁，为了避免死锁，需要添加过期时间。

  ```lua 
  expire lock:codehole 5 --5秒过期
  del lock:codehole
  ```

  ![image-20210201215652631](C:\Users\fanfan\AppData\Roaming\Typora\typora-user-images\image-20210201215652631.png)

- 但是可能过期时间没到线程就被杀死，锁还没有释放，会造成死锁，setnx 和
  expire 指令可以一起执行。

  ```lua
  set lock:codehole true ex 5 nx 
  del lock:codehole
  ```

- **超时问题**：若加锁和释放锁之间的逻辑执行时间过长，超出了锁的超时限制，会出现异常，需要人工干预。

- **可重入性**：当前持有锁的线程能够再次请求加锁。需要对客户端的 set 方法进行包装，使用线程的 Threadlocal 变量存储当前持有锁的计数。

  - ReentrantLock: 也是一种可重入锁，基于AQS(AbstractQueuedSynchronizer)实现，分为公平锁和非公平锁。

    - 公平锁：多个线程按顺序获取锁，等待队列中第一个线程获取锁，其他阻塞。CPU开销大。

    - 非公平锁：所有线程直接获取锁，谁获得是谁的。有可能一直获取不到。

      ![img](https://awps-assets.meituan.net/mit-x/blog-images-bundle-2018b/7f749fc8.png)

  - ThreadLocal: 一个变量，只能在当前线程实例内使用，用于本线程内方法共享。

    ```java
    public class SessionHandler {
    
      public static ThreadLocal<Session> session = ThreadLocal.<Session>withInitial(() -> new Session());
    
      @Data
      public static class Session {
        private String id;
        private String user;
        private String status;
      }
    
      public String getUser() {
        return session.get().getUser();
      }
    
      public String getStatus() {
        return session.get().getStatus();
      }
    
      public void setStatus(String status) {
        session.get().setStatus(status);
      }
    
      public static void main(String[] args) {
        new Thread(() -> {
          SessionHandler handler = new SessionHandler();
          handler.getStatus();
          handler.getUser();
          handler.setStatus("close");
          handler.getStatus();
        }).start();
      }
    }
    ```

### 应用2：延时队列

- 专业消息队列中间件
  - Rabbitmq
  - Kafka

- Redis 消息队列：没有ack,适合只有一组消费者对于可靠性一般的消息队列。

![image-20210202210431501](C:\Users\fanfan\AppData\Roaming\Typora\typora-user-images\image-20210202210431501.png)

​	通过list数据结构实现消息队列，rpush\lpush进入，lpop\rpop出队列

- blpop/brpop：blocking 阻塞，解决空队列一直读的问题（sleep会导致延迟）。可能出现一直阻塞，然后空闲连接，服务器主动断开减少闲置资源，blpop/brpop会报错。

- 延时队列：
  - 通过zset结构实现：消息的字符串格式作为zse的值value，到期时间作为score，用多个线程轮询zset，获取到期任务处理。
  - 多个线程争抢任务，通过zrem来确定谁处理。if (jedis.zrem(queueKey, s) > 0)  // 抢到了

### 应用3：位图

- 定义：用来存储大量的bool数据，比如一个人365天的签到，签到为1 ，没签为0，只需要365个位（bit）,46个字节。内容就是byte数组，还是普通的字符串。

- 使用：设置单个的bit值  。'h' ASCII码的二进制为01101000，设置的时候从高位到低位数1。

  - 零存整取：按位存，取出整个字符串。

  ​        ![20210202215425](C:\Users\fanfan\Documents\Scrshot\20210202215425.png)            ![20210202214911](C:\Users\fanfan\Documents\Scrshot\20210202214911.png)

  - 整存零取：存一个字符，按位取值。

    ![20210202215929](C:\Users\fanfan\Documents\Scrshot\20210202215929.png)

- 若为不可打印字符，显示16进制形式。

- 统计和查找：bitcount，统计范围内所有1的个数；bitpos,查找范围内第一个0或1.[start, end]为字节索引，所以获取的值都是8bit，需要和8进行换算。用法包括签到，第一次签到或者第一次断签之类的。

  ![20210204203559](C:\Users\fanfan\Documents\Scrshot\20210204203559.png)

- 魔术指令：bitfield——最多可以同时操作64位，包括get、set、incrby三个命令

  其中incrby可能会出现溢出，overflow 包含默认折返处理wrap（抛弃溢出位）、选择失败fail（报错不执行）、饱和截断sat（保留最大值或最小值），但是只有一条指令。

  ![20210204205627](C:\Users\fanfan\Documents\Scrshot\20210204205627.png)

  

### 应用4：HyperLogLog

- hyperloglog?
- uv?
- pv?