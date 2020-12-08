第四章 对象与类

面向对象：数据第一位，算法第二位

类：构造对象的模板，通过类构造对象——类的实例化

​    封装：数据隐藏。通过访问修饰符，隐藏具体实现细节，私有字段

​    继承：通过一个类扩展一个新类，复用原来的方法和实现，只需要添加新的数据域和实现，重写

对象：行为、状态、标识

类关系 ：依赖use-a、聚合has-a、继承is-a

构造器：与类同名，初始化对象状态，new，多个，无返回值，可有可无参数

访问器、更改器：set、get，是否修改实例域

final：修饰不可变字段，线程安全性

静态域和静态方法：static  静态方法访问静态域，属于类但是不属于类对象，与对象状态无关，不需要对象调用

工厂设计模式3种：

- 简单工厂:一个工厂，一个产品。接口，interface shape；实现，implement circle、square、rectangle；工厂类， ShapeFactory 、getshape 、class newInstance();测试类，new工厂对象，依据传入的shape类型调用不同的实现方法
- 工厂方法：多个工厂，多个产品。
- 抽象工厂：多个工厂，多种产品，工厂也需要选择，抽象类

lock内部原理：

包装类：初始值是否为null；缓存

模板方法：

方法参数：值引用不可更改，对象引用可以修改状态，但是不能引用新对象

对象构造：

- 方法重载：同名方法，参数类型不同，方法签名add(int a)
- 默认域初始值，值=0，bool false，对象 null
- 参数名：_name,aName,this.name=name
- this(...)调用另一个构造器

包：确保类名唯一性，域名逆序

​       import：引用包中类，导入静态方法、静态域

​       包与目录不匹配，虚拟机运行时找不到类

类设计技巧：

1. 数据私有

2. 数据初始化

3. 过多的基本类型放到一个类里

4. 类职责多拆分

5. 类名方法名camel命名且有意义

6. 考虑并发

   

# 第五章 继承

super关键字：子类调用父类构造器初始化，super(参数); 子类调用父类的方法，super.;

语法差异：for ——foreach，System.out.printIn()——Console.write()，string.format，重写不用关键字

多态：一个对象变量可以指示多种类型

​		置换法则：is-a 将子类对象变量赋值给父类变量，父类引用子类对象

​		重写是子类对父类的重载覆盖

final：修饰不可变字段，线程安全性，修饰类，不可继承，方法不可重写

对象转换：继承层次内转换，大向小转换需要instanceof判断

抽象类abstract：更高层次的父类，不具体实现方法，不能实例化

Object类：

- equals :引用,null ,类型,基本类型域
- hashCode:散列码,int,唯一
- toString:最好重写

ArrayList:泛型数组,先add,set(位置,值),get(i)=a[i]

包装类——包装器wrapper：int——integer char——character

变参：Object...args,参数列表最后

枚举enum：ordinal——int，toString——string

# 第六章 接口、内部类

内部类：

string.intern()函数使用：它先看常量池中有没有，没有的话才创建然后复制引用，有的话直接用

# 第七章 异常

异常分类：

RuntimeException——类型转换错误，数组越界，访问null指针

IOException——文件不存在，找不到名称的资源

# 第九章 集合

两个基本接口：Collection和Map

基本方法：Add和Iterator，有序集合，访问两种方法，整数索引和迭代器;Put,Get

具体的集合：

ArrayList  一种可以动态增长和缩减的索引序列
LinkedList  一种可以在任何位置进行高效地插人和删除操作的有序序列
ArrayDeque  一种用循环数组实现的双端队列
HashSet   一种没有重复元素的无序集合
TreeSet  — 种有序集——按元素大小
EnumSet  一种包含枚举类型值的集
LinkedHashSet 一种可以记住元素插人次序的集
PriorityQueue  一种允许高效删除最小元素的集合
HashMap  一种存储键 / 值关联的数据结构
TreeMap  — 种键值有序排列的映射表
EnumMap  一种键值属于枚举类型的映射表
LinkedHashMap  一种可以记住键 / 值项添加次序的映射表
WeakHashMap  一种其值无用武之地后可以被垃圾回收器回收的映射表
IdentityHashMap  一种用 = 而不是用 equals 比较键值的映射表

- 链表 linked list 双向链接 data previous next 插入删除只需要改变相邻的结构

- 数组链表 arraylist  从中间插入删除元素时效率低，后面所有元素的索引都需要改变

- 散列表 hashMap 底层基于数组链表实现，定义数组大小为桶，

  ```
  /**
   * The hash table data.
   */
  private transient Entry<?,?>[] table;
  ```

- 每个元素生成一个哈希码，通过和桶的大小（默认16）

  ```
  /**
   * The default initial capacity - MUST be a power of two.
   */
  static final int DEFAULT_INITIAL_CAPACITY = 1 << 4; // aka 16
  ```

  取余放入对应的数组位置中。当不同元素放入数组同一个位置时叫做哈希（散列）冲突，此时可以通过两种方法来解决，第一种是通过链表实现，将同一个位置不同元素通过链表存起来；第二种是扩大桶的大小

- 集合 set 存储不能重复的元素，底层依据hashmap的key不能重复的规则实现 



# 第十四章 并发

1. 进程和线程 

   进程：操作系统对正在运行的程序一种抽象，比如打印文件和下载右键是两个进程，进程是操作系统分配资源的基本单位

   线程：是进程中分解的任务，进程包含多个线程，线程共享进程内的所有资源，CPU调度和分配的基本单位

   串行、并行、并发：串行，多任务按顺序执行，执行完一个执行下一个；并行：多个CPU或者单个CPU多核内的线程同时执行；并发：单个CPU内的线程不断切换，微观上是一个个执行的，宏观看起来是一起执行的，叫做并发。

2. 线程状态

   • New ( 新创建 ）
   • Runnable( 可运行 ）——还需要分配时间片
   • Blocked( 被阻塞 ）——获取对象锁，但是锁被占用
   • Waiting( 等待 ）
   • Timed waiting( 计时等待 ）——sleep wait join 
   • Terminated( 被终止 ）——run执行完成，或发生未处理异常

![image-20201127153013027](C:\Users\fanfan\AppData\Roaming\Typora\typora-user-images\image-20201127153013027.png)

3. 线程属性
   - 优先级 1-10 默认5 
   - 守护线程 setDaemon 线程启动前标识 未其他线程服务 只有它自己时关闭
   - 线程组和异常处理器—— UncaughtException

4. 同步
   - 锁对象：synchronized关键字；reentrantlock——try之前lock ,finaly unlock
   - 条件对象 ：newCondition
   - synchronized 关键字 ——CAS 流程 改变数据，乐观锁 悲观锁compareAndSwap
   - volatile 修饰域 实例域免锁。不能确保翻转域中的值。 不能保证读取 、 翻转和写入不被中断 。
   - 原子性  atomic类  compareAndSet 
   - 死锁  只有一个signal ThreadLocal 辅助类为各个线程提供一个单独的生成器,返回线程局部变量，只属于这个线程的实例

5. 阻塞队列

   生产者 消费者模型，中间通过阻塞队列作为缓冲区，保证安全的传递数据。当试图向队列添加元素而队列已满 ， 或是想从队列移出元素而队列为空的时候 ， 阻塞队列 （ blocking queue ) 导致线程阻塞

   ![image-20201128223830235](C:\Users\fanfan\AppData\Roaming\Typora\typora-user-images\image-20201128223830235.png)

# 卷2 第一章 流

流的概念：将输入输出中的数据抽象成“流”，包括文件File、内存、网络Socket

### File 类

- 概述：对文件（目录）本身的创建、删除等，不能对内容进行操作

- 构造：

  - `public File(String pathname)` ：通过将给定的**路径名字符串**转换为抽象路径名来创建新的 File实例。

  - `public File(String parent, String child)` ：从**父路径名字符串和子路径名字符串**创建新的 File实例。

  - `public File(File parent, String child)` ：从**父抽象路径名和子路径名字符串**创建新的 File实例。

- 常用方法
  - getPath、getName、isDirectory、isFile、exists、delete、mdirs、createNewFile
  - list、listFiles遍历文件，配合isDirectory递归

### I/O流

|        | 输入流                     | 输出流                      |
| ------ | -------------------------- | --------------------------- |
| 字节流 | 字节输入流 **InputStream** | 字节输出流 **OutputStream** |
| 字符流 | 字符输入流 **Reader**      | 字符输出流 **Writer**       |

字节流适合读取视频、声音、图片等二进制文件

字符流适合纯文本文件，字符流=字节流+编码表，主要为了中文乱码问题。

#### FileOutputStream 文件输出流

- 构造对象：

  1、 `public FileOutputStream(File file)`：根据File对象为参数创建对象。
  2、 `public FileOutputStream(String name)`： 根据名称字符串为参数创建对象。

  直接或间接传入文件路径，在该路径下，如果没有这个文件，会创建该文件。如果有这个文件，会清空这个文件的数据。

  3、`public FileOutputStream(File file, boolean append)`

  4、`public FileOutputStream(String name, boolean append)`

  为了不清空数据，从后面继续继续追加数据

- 写出字节

  - `write(int b)` 方法，每次可以写出一个字节数据。虽然int是4个字节，但是只写出一个字节数据

    ```
    fos.write(97);//文件内容a
    ```

  - `write(byte[] b)`，每次可以写出数组中的数据。

    ```
    byte[] b="test".getBytes();
    fos.write(b);//输出文件内容test
    ```

  - `write(byte[] b, int off, int len)` ,每次写出从`off`索引开始，`len`个字节

    ```
    byte[] b="test".getBytes();
    fos.write(b,0,2);//输出文件内容te
    ```

#### FileInputStream 文件输入流

- 构造对象：

  1、 `public FileInputStream(File file)`：通过打开与实际文件的连接来创建一个对象。
  2、 `public FileInputStream(String name)`： 根据名称字符串为参数创建对象。

- 读出字节

  - `read`方法，每次可以读取一个字节的数据，提升为int类型，读取到文件末尾，返回`-1`。

    ```
    int read= fis.read();//文件中内容为abc,输出97，a;若为汉字，不同的int值和符号
    System.out.println(read);
    System.out.println((char)read);
    ```

  - `read(byte[] b)`，每次读取b的长度个字节到数组b中，返回读取到的有效字节个数，读取到末尾时，返回`-1` 。由于可能最后一次读取到的字节不够b的长度，会造成数组中后面的数据没有替换。需要在字节转成字符串的时候加上有效长度

    ```
    FileInputStream fis=new FileInputStream(f);
    byte[] b=new byte[2];
    int len;
    while ((len=fis.read(b))!=-1)
    {
    System.out.println(new String(b,0,len));
    }
    ```

复制图片：

```java
File fP=new File("C:\\Users\\fanfan\\Desktop\\临时文件\\test.png");
File fPCopy=new File("C:\\Users\\fanfan\\Desktop\\临时文件\\testCopy.png");
try {
    FileInputStream fis=new FileInputStream(fP);
    FileOutputStream fos=new FileOutputStream(fPCopy);
    byte[] b=new byte[1024];
    int len;
    while ((len=fis.read(b))!=-1)
    {
        System.out.println(new String(b,0,len));
        fos.write(b,0,len);
    }
    fis.close();
    fos.close();
}
catch (FileNotFoundException e)
{
    e.printStackTrace();
}
catch (IOException e){
    e.printStackTrace();
}
```

### FileReader字符输入流

- 构造：

  1、`FileReader(File file)`： 创建一个新的 FileReader ，给定要读取的**File对象**。
  2、 `FileReader(String fileName)`： 创建一个新的 FileReader ，给定要读取的文件的**字符串名称**。

- 读取字符数据

  `read`方法，每次可以读取一个字符的数据，提升为int类型，读取到文件末尾，返回`-1`，循环读取

#### FileWriter字符输出流

- 构造：

  1、 `FileWriter(File file)`： 创建一个新的 FileWriter，给定要读取的File对象。
  2、`FileWriter(String fileName)`： 创建一个新的 FileWriter，给定要读取的文件的名称。

- 写入字符数据

  `write(int b)` 方法，每次可以写出一个字符数据。fw.write("\r\n");换行直接写就行

【注意】**关闭资源时,与FileOutputStream不同。 如果不关闭,数据只是保存到缓冲区，并未保存到文件。**

可以通过flush()刷新缓存，然后在关闭释放资源。

#### 缓冲流（高效流）

- 构造：

  1、创建字节缓冲输入流 BufferedInputStream bis = new BufferedInputStream(new FileInputStream("b.txt")); 

  2、创建字节缓冲输出流 BufferedOutputStream bos = new BufferedOutputStream(new FileOutputStream("b.txt"));

  3、创建字符缓冲输入流 BufferedReader br = new BufferedReader(new FileReader("b.txt")); 

  4、创建字符缓冲输出流 BufferedWriter bw = new BufferedWriter(new FileWriter("b.txt"));

- 方法：

  - 基本读取写入数据方式和基本流一样。
  - 

  