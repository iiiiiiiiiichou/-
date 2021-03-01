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

- 概念：
  - hyperloglog:一种数据结构，提供不精确的去重计数方案，标准误差0.81%
  - uv：unique visitor 独立访问用户，不同的账号
  - pv：page views 页面浏览量或点击量，用户每次刷新就算一次
  - ip：Internet protocol 互联网访问协议，一般同一个电脑有一个ip。

- 使用：pfadd 、pfcount、pfmerge

```lua
--pfadd key element[element]增加元素
--pfcount key 统计个数
--pfmerge 合并两个里面的数据
```

- 实现原理：

  - [伯努利试验](https://juejin.cn/post/6844903785744056333)：随机数的个数N和最大低位连续零的个数K之间存在N=2^K关系，通
    过这个 K 值可以估算出随机数的数量。但是当N处于2^K与2^(K+1)之间时，估算值都是2^K。 

  ![image-20210209154819730](C:\Users\fanfan\AppData\Roaming\Typora\typora-user-images\image-20210209154819730.png)

  - loglog优化估算：上面的误差比较大，所以通过平均数来减小误差。比如说以100个随机数为一轮，取4轮，就是(k1+k2+k3+k4)/4
  - 调和平均数：倒数的平均，能够减少极限值（离群值）的影响。4/(1/k1+1/k2+1/k3+1/k4)

  ![image-20210209192442453](C:\Users\fanfan\AppData\Roaming\Typora\typora-user-images\image-20210209192442453.png)

  - 桶：用来表示平均的次数，越大越精确。

- 为什么内存占用12KB：

  使用2^14=16384个桶，每个桶的maxbits需要6个bits来存储，最大可表示maxbits=63,总的占用内存为2^14 * 6 / 8 = 12k。

### 布隆过滤器

- bloomfilter:不那么精确的可以去重的set结构，布隆过滤器对于已经见过的元素肯定不会误判，它只会误判那些没见过的元素。

- 安装：[官网](https://github.com/RedisBloom/RedisBloom)下载的为2.2.4最新版本。由于之前安装的Redis为yum安装的3.2版本，需要升级（我是卸载后重新安装的）yum remove redis __[redis官网](https://redis.io/download)下载为6.0.10版本，

- 问题：按照[教程](https://blog.csdn.net/u013030276/article/details/88350641)添加loadmodule后每次启动后并不能生效，bf.add每次都显示没有命令，寻找解决办法后，修改Redis.conf里面的protectmode为no,注释掉bind 127

- 命令：bf.add 和 bf.exists 增加多个或者判断多个的时候变成bf.madd和bf.mexists

- 参数：**key,error_rate,init_size**

  - 作用：error_rate 表示错误率，错误率越低，需要的存储空间就越大；init_size表示预计存放的元素数量，超过此值误判率会上升。默认的 error_rate 是 0.01，默认的 initial_size 是 100

  - 设定方式：（1）bf.reserve显式的设定

    ```lua
    127.0.0.1:6379> bf.reserve bfs 0.001 100
    OK
    ```

    （2）通过配置文件loadmodule，将后两个参数直接设定。

    ```lua
    loadmodule /usr/local/redisbloomfilter/RedisBloom/redisbloom.so ERROR_RATE 0.001 INIT
    ```

- 原理：位数组和多个无偏hash。add的时候将key通过hash算法算得hash值，作为索引值和数组大小取模得到一个位置置1，多个hash后得到多个位置都为1。exists查找时，同样用这个过程，查找这些位置是否为1，只要有一个为零的话就说明不存在。但是都为1不一定是同一个key,可能是多个key导致。极端太密集的使所有的位置都为1。

  ![image-20210214211705789](C:\Users\fanfan\AppData\Roaming\Typora\typora-user-images\image-20210214211705789.png)

- 空间占用：
  - 布隆过滤器有两个参数，第一个是预计元素的数量 n，第二个是错误率 f。公式根据这
    两个输入得到两个输出，第一个输出是位数组的长度 l，也就是需要的存储空间大小 (bit)，
    第二个输出是 hash 函数的最佳数量 k。hash 函数的数量也会直接影响到错误率，最佳的数
    量会有最低的错误率。
    k=0.7*(l/n) # 约等于
    f=0.6185^(l/n) # ^ 表示次方计算，也就是 math.pow
  - 引入参数 t 表示实际元素和预计元素的倍数 t表示当实际元素超出预计元素时，错误率变化
    f=(1-0.5^t)^k # 极限近似，k 是 hash 函数的最佳数量
  - 根据公式，当错误率为1%，需要9.6bit，其中置1的为

- 应用：nosql数据库内包含布隆过滤器结构；新闻推送和爬虫url去重；邮箱系统，误判的垃圾邮件。

###  简单限流

- 限流：由于处理能力问题或者业务要求，在一定时间内必须限制请求次数。
- 简单限流算法：通过zset结构中的score，记录时间戳，key记录行为，value只需要不重复，也可以是时间戳。 过期时间设置为时间段+1

### 漏斗限流

- 概念：漏斗的剩余空间就代表着当前行为可以持续进行的数量，漏嘴的流水速率代表着系统允许该行为的最大频率。

- 算法：定义一个漏斗，所占内存固定，就是其容量。当灌水开始前，判断是否还有空间，没有的话makespace，等待腾出空间。

- 实现：通过Redis-cell模块，如果被拒绝了，就需要丢弃或重试。cl.throttle 指令重试时间直接取返回结果数组的第四个值进行 sleep 即可，如果不想阻塞线程，也可以异步定时任务来重试。

  ![image-20210216215958349](C:\Users\fanfan\AppData\Roaming\Typora\typora-user-images\image-20210216215958349.png)



### GEOHash

- 概念：一种距离排序算法，位置本来由经纬度表示，此算法映射到一条线上，距离越近的直线上的距离也越近。将地球经纬度当成一个平面，通过细分网格将坐标进行整数编码。编码方式为二刀法，geohash算法将二刀法得到的整数值继续进行base32（0-9，a-z，去掉ailo）编码变成字符串，经纬度用52位整数表示
- 原理：实际上是zset结构，将距离整数值放score中。
- 使用：

```lua
--六个指令geoadd向集合中添加经度坐标、纬度坐标和元素、geodist计算集合中两个元素的距离、geopos 获取元素位置，由于一二维有损转换，会有一点不同、geohash获取元素的hash值，为经纬度编码字符串，通过官网能够直接定位、georadiusbymember获取指定元素附近的其他元素；
geoadd company 116.489033 40.007669 meituan // 1
geoadd company 116.48105 39.996794 juejin 116.562108 39.787602 jd //2
geodist company juejin meituan km //"1.3878"
geopos company juejin //1) "116.48104995489120483" 2) "39.99679348858259686"
geohash company juejin // "wx4gd94yjn0"
georadiusbymember company juejin 20km count 3 asc //1) "juejin"2) "meituan"3) "jd"
georadiusbymember company == ireader可以替换成经纬度116.514202 39.905409 == 20 km withcoord withdist withhash count 3 desc //1)
1) "ireader"
2) "0.0000"
3) (integer) 4069886008361398
4) 1) "116.5142020583152771"
2) "39.90540918662494363"
2)
1) "juejin"
2) "10.5501"
3) (integer) 4069887154388167
4) 1) "116.48104995489120483"
2) "39.99679348858259686"
3) 
1) "meituan"
2) "11.5748"
3) (integer) 4069887179083478
4) 1) "116.48903220891952515"
2) "40.00766997707732031"
```

![20210217104847](C:\Users\fanfan\Documents\Scrshot\20210217104847.png)

![20210217111642](C:\Users\fanfan\Documents\Scrshot\20210217111642.png)



- 注意：单个 key 对应的数据量不宜超过 1M，当数据量过亿时需要按照省份等拆分

### scan

- 应用场景：需要在Redis无数实例中找到符合要求的key,提供的keys能够满足正则表达式寻找的功能，但是具有两个缺点：1.没有offset和limit，满足条件的全部都会被展示。2.keys算法是遍历算法，复杂度O(n)，当keys很多的情况下会造成Redis卡顿，因为单线程，阻塞其他指令。

- 特点：1.通过游标来分步进行，不会阻塞线程，虽然也是复杂度O(n)。

  2.也有匹配功能，具有limit参数，限制最大条数，但是实际条数会小于等于

  3.返回的结果可能会有重复，需要手动去重。

  4.单次返回的结果是空的并不意味着遍历结束，而要看返回的游标值是否为零;

  5.遍历过程中有数据修改的话，能不能遍历到不确定。

- 使用：

```lua
--scan 参数提供了三个参数，第一个是 cursor 整数值，第二个是 key 的正则模式，第三个是遍历的 limit hint。第一次遍历时，cursor 值为 0，然后将返回结果中第一个整数值作为下一次遍历的cursor。一直遍历到返回的 cursor 值为 0 时结束
scan cursor match keys count limit
scan 0 match key99* count 1000  // 1) "13976"2) 1) "key9911"2) "key9974"
scan 13976 match key99* count 1000 // 1) "1996"2) 1) "key9982"2) "key9997"
```

- 字典结构：类似于hashmap，数组＋链表。一维数组大小2^n,扩容加倍。scan 指令返回的游标就是第一维数组的位置索引，我们将这个位置索引称为槽 (slot)。之所以返回的结果可能多可能少，是因为不是所有的槽位上都会挂接链表，有多有少，将每个槽上的符合匹配的返回。

![image-20210220195947469](C:\Users\fanfan\AppData\Roaming\Typora\typora-user-images\image-20210220195947469.png)

- scan遍历顺序：高位进位加法，在扩容缩容时能够避免槽位的遍历重复和遗漏。rehash 就是将元素的hash 值对数组长度进行取模运算，因为长度变了，所以每个元素挂接的槽位可能也发生了变
  化。又因为数组的长度是 2^n 次方，所以取模运算等价于位与操作。假设开始槽位的二进制数是 xxx，那么该槽位中的元素将被 rehash 到0xxx 和 1xxx(xxx+8) 中。 如果字典长度由 16 位扩容到 32 位，那么对于二进制槽位xxxx 中的元素将被 rehash 到 0xxxx 和 1xxxx(xxxx+16) 中。

![image-20210220200928117](C:\Users\fanfan\AppData\Roaming\Typora\typora-user-images\image-20210220200928117.png)

- 注意：大key,内存开销大分配和回收容易卡顿，通过redis-cli -h 127.0.0.1 -p 7001 –-bigkeys -i 0.1指令查找大key，然后重新设计。

### 线程IO模型

- 单线程！！！
  - 快：数据存在内存中，内存级别的运算所以快。
  - 并发：多路复用（事件轮循）

- 非阻塞IO： 在套接字对象上提供了一个选项 Non_Blocking，当打开时，直接读写到缓冲区就完事了，读写多少都和缓冲区有关，不用阻塞线程了。

  ![image-20210220203453696](C:\Users\fanfan\AppData\Roaming\Typora\typora-user-images\image-20210220203453696.png)

- 事件轮循：解决读写到一半缓冲区满了的问题，通过select函数不断询问处理

  ![image-20210220202530124](C:\Users\fanfan\AppData\Roaming\Typora\typora-user-images\image-20210220202530124.png)

- 指令队列：客户端的指令通过队列来排队进行顺序处理，先到先服务。

- 响应队列：Redis 服务器通过响应队列来将指令的返回结果回复给客户端。 如果队列为空，那么意味着连接暂时处于空闲状态，不需要去获取写事件，也就是可以将当前的客户端描述符从 write_fds 里面移出来。等到队列有数据了，再将描述符放进去。

- 定时任务：Redis 的定时任务会记录在一个称为最小堆的数据结构中。这个堆中，最快要执行的任
  务排在堆的最上方。在每个循环周期，Redis 都会将最小堆里面已经到点的任务立即进行处理。处理完毕后，将最快要执行的任务还需要的时间记录下来，这个时间就是 select 系统调用的timeout 参数。

### 通信协议

- RESP：5种格式

1、单行字符串 以 + 符号开头。
2、多行字符串 以 $ 符号开头，后跟字符串长度。
3、整数值 以 : 符号开头，后跟整数的字符串形式。
4、错误消息 以 - 符号开头。
5、数组 以 * 号开头，后跟数组的长度。

- 使用：客户端发给服务器，只支持单行字符串

  ​          服务器返回客户端，支持所有。

### 持久化

- 原因：保存在内存中，突然宕机数据会全部丢失
- 持久化机制：[快照和AOF日志](https://blog.csdn.net/ljheee/article/details/76284082)
  - RDB（Redis DataBase）将Redis数据生成快照备份。全量备份。快照是电脑在某个时间点上的状态，它存储了系统映像，让电脑在出现问题时能快速回复到未出问题前的状态。系统映像是系统副本，包含系统驱动器、程序、文件、设置。
  - AOF日志（Append Only File 只允许追加不允许改写的文件）将Redis执行过的所有写指令记录下来，重启时将这些指令重新执行一遍。增量备份。

### 管道

- 概念：客户端通过改变读写顺序来提升性能。

- 通信过程：

  1、客户端进程调用 write 将消息写到操作系统内核为套接字分配的发送缓冲 send
  buffer。
  2、客户端操作系统内核将发送缓冲的内容发送到网卡，网卡硬件将数据通过「网际路
  由」送到服务器的网卡。
  3、服务器操作系统内核将网卡的数据放到内核为套接字分配的接收缓冲 recv buffer。
  4、服务器进程调用 read 从接收缓冲中取出消息进行处理。
  5、服务器进程调用 write 将响应消息写到内核为套接字分配的发送缓冲 send buffer。
  6、服务器操作系统内核将发送缓冲的内容发送到网卡，网卡硬件将数据通过「网际路
  由」送到客户端的网卡。
  7、客户端操作系统内核将网卡的数据放到内核为套接字分配的接收缓冲 recv buffer。
  8、客户端进程调用 read 从接收缓冲中取出消息返回给上层业务逻辑进行处理。
  9、结束。

- 原理：对于管道来说，就只有一次网络来回的开销。write操作：只负责将数据写到本地操作系统内核的发送缓冲然后就返回了。剩下的事交给操作系统内核异步将数据送到目标机器。但是如果发送缓冲满了，那么就需要等待缓冲空出空闲空间来，这个就是写操作 IO 操作的真正耗时。

  read操作：只负责将数据从本地操作系统内核的接收缓冲中取出来就了事了。但是如果缓冲是空的，那么就需要等待数据到来，这个就是读操作 IO 操作的真正耗时。

### 事务

- 概念：为了保证多个操作的原子性，同时成功或失败，避免出现数据不一致。

- 正常事务的基本使用：begain()  开始;commit()提交;rollback()回滚; 

- Redis事务：multi开始；exec执行;discard丢弃

  ![image-20210228003520393](C:\Users\fanfan\AppData\Roaming\Typora\typora-user-images\image-20210228003520393.png)

  所有的指令在 exec 之前不执行，而是缓存在服务器的一个事务队列中（QUEUED 是一个简单字符串，同 OK 是一个形式，它表示指令已经被服务器缓存到队列里了），服务器一旦收到 exec 指令，才开执行整个事务队列，执行完毕后一次性返回所有指令的运行结果（第一条执行完返回1，又加一返回2）。因为 Redis 的单线程特性，它不用担心自己在执行队列的时候被其它指令打搅，可以保证他们能得到的「原子性」执行。上图显示了以上事务过程完整的交互效果。

- watch：一种乐观锁机制，能够解决并发修改的问题。

  在开启事务之前执行watch key，若中间被修改，exec返回null，报错watch error.

### PubSub

- 概念：发布者和订阅者模型。消息队列，生产者生产完消息放入队列中，消费者消费时从队列中取消息。在分布式系统中，要支持消息多播（一个生产者多个消费者，通过中间件复制给多个消费者，每个消费者有不同的处理逻辑，可实现解耦）
- 通过名称订阅主题，现在基本不用了。



### 小对象压缩

- 原因：Redis全部数据放在内存中，若占用过大容易崩溃，所有必须节约内存。

- 概念：当存储的数据比较小时，用紧凑存储方式进行压缩存储。

  - ziplist ：是字节数组结构，所有元素都是相邻的。存储hash，key value作为相邻的entry；存储zset，value score作为相邻entry；

  ![image-20210301213843504](C:\Users\fanfan\AppData\Roaming\Typora\typora-user-images\image-20210301213843504.png)

  - intset :是一个紧凑的整数数组结构，存储元素都是整数的并且元素个数较少的 set 集合。可以从uint16升级到uint32再升级到uint64。

  ![image-20210301215202034](C:\Users\fanfan\AppData\Roaming\Typora\typora-user-images\image-20210301215202034.png)

- 标准存储结构（hashtable?）：当不是小对象，元素数量或者元素大小超过一定界限，就会升级

  hash-max-zipmap-entries 512 # hash 的元素个数超过 512 就必须用标准结构存储
  hash-max-zipmap-value 64 # hash 的任意元素的 key/value 的长度超过 64
  list-max-ziplist-entries 512 # list 的元素个数超过 512 
  list-max-ziplist-value 64 # list 的任意元素的长度超过 64
  zset-max-ziplist-entries 128 # zset 的元素个数超过 128 
  zset-max-ziplist-value 64 # zset 的任意元素的长度超过 64
  set-max-intset-entries 512 # set 的整数元素个数超过 512 就必须用标准结构存储

- 内存回收：操作系统内存以页为单位，只能整页释放，不同的key存在不同的页上。内存分配算法： jemalloc(facebook) 库或者 tcmalloc(google)。

### 主从同步

- **CAP原理**：C - Consistent ，一致性； A - Availability ，可用性； P - Partition tolerance ，分区容忍性。网络断开叫做网络分区，网络分区发生时，一致性和可用性两难全。是分布式存储的理论基石。

- 主从同步：分布式系统有主站和从站，数据保持一致，当其中一个宕机时仍然能够继续提供服务。当宕机修复后，必须进行数据同步。

  - 增量同步：将指令流保存在环形数组中，同步时从数组中拿指令执行进行数据同步。但是数组满了后会从头覆盖，此时无法进行同步，必须通过其他方式同步。

    ![image-20210301224357167](C:\Users\fanfan\AppData\Roaming\Typora\typora-user-images\image-20210301224357167.png)

  - 快照同步：主站先用*Bgsave* 命令，在后台异步保存当前数据库的数据到磁盘，传递给从站，从站接收后清空自己的数据，全量加载接收的数据，加载完成后再进行增量同步。

    - 无盘复制：为了减少io操作，指主服务器直接通过套接字将快照内容发送到从节点，生成快照是一个遍历的过程，主节点会一边遍历内存，一遍将序列化的内容发送到从节点。

### Sentinel

- 哨兵，自动监测宕机而不是人为监控。

  ![image-20210301225637936](C:\Users\fanfan\AppData\Roaming\Typora\typora-user-images\image-20210301225637936.png)

- 监测原理：进行主从地址轮循，如果连接地址改变，则断开连接别的。