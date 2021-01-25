# Redis

### 开篇

概念：远程服务字典 remote dictionary service，是一个存储中间件。

用来做什么：缓存，分布式锁等。

- 缓存：

  - 概念：是一种高速数据交换存储器，先于内存和CPU交互，速度比较快。

  - 结构：
    - 一级缓存L1 cache：对CPU性能影响较大，静态RAM组成，容量32--256KB
    - 二级缓存L2 cache ：内部和外部两种芯片，内部速率=主频，外部=0.5主频。容量128KB--3M

  - 工作原理：CPU要读取数据，先从1级缓存中找，找到就立即读取并送给CPU处理。没找到，就从内存中读取并送给CPU，同时把数据所在的数据块调入缓存中。
  - 磁盘缓存的作用：
    - 预读取，将连续的几个簇中的数据读到缓存中
    - 写入，当写入数据到硬盘上的时候，先将数据保存在缓存中，发信号给系统继续执行，当硬盘空闲时再写入盘片上。若掉电磁头则借助惯性写入暂存区。
    - 临时存储，将频繁访问的数据放在缓存中，提升读写数据效率

- 分布式锁：
  - 概念：为了解决分布式环境下出现的并发问题，需要使用分布式锁
  - 区别：普通锁是多个线程间的并发问题，通过lock和synchronized等，使用内存标记来解决问题。分布式锁是多个设备间的多个进程下的并发问题，所以必须借助Redis等中间件实现。

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

