# Spring

### 基本使用

1. 简介：

   - 2002  interface21
   - 2004 Rod Johson 
   - 简化开发
   - SSH：struct2 + Spring + Hibernate
   - SSM：SpringMVC +Spring  +Mybatis
   - 导入：Spring Maven ——选择Spring-webMVC 和spring-jdbc
   - 重点：控制反转（IOC）和面向切面编程（AOP）

   定义：一个轻量级的控制反转和面向切面编程的框架

2. 控制反转（IOC）和依赖注入（DI）

   - 设值注入：通过成员变量的setter方法
   - 构造注入：通过构造器（带参——配置子标签constructor-arg）

- 三个基本点：

  1. 面向接口编程，更改实现，易于扩展

  						2. 不在自己new对象，而是通过spring创建和初始化
     
     3. spring根据配置文件或注解，利用反射来创建实例并注入依赖关系

3. spring 管理bean

   - 配置xml文件：id唯一，class实现类全限定名。

     ​						别名：name 和 alias 可能在和别的框架整合时使用

   - 不同值类型注入：基本类型--value；引用其他bean--ref; 集合--套一层集合名称 list--value、map--entry key,value；

     ```xml
     <!--需要依赖的类的id和全限定类名-->
     <bean id = "person" class = "" >
         <!--需要注入的set方法的属性名和注入的bean的引用名作为参数-->
     	<propery name = "axe" ref = "steelAxe" />
     </bean>
     <!--list-->
     <bean id = "person" class = "" >
     	<propery name = "axe">
             <list>
                  <!--注意值写中间-->
                 <value>"1"</value>
                 <value>"2"</value>
             </list>
         </propery>
     </bean>
     <!--map-->
     <bean id = "person" class = "" >
     	<propery name = "axe">
             <map>
                 <entry key = "k1" value = "v1"/>
                 <entry key = "k2" value-ref = "v2"/>
             </map>
         </propery>
     </bean>
     ```

   - 自动装配：autowire
     - no : 不使用，必须通过ref注入--ref显示优先
     - byName : 根据setter 方法名 - - 驼峰命名法，有就注入，没有就不动作
     - byType : 根据setter 方法的形参类型 --有一个，注入；没有不动作；多个抛异常
     - constructor : 根据构造器参数

   - 注解 ：annotion
     - configuration：修饰类，实现类
     - bean ：修饰方法
     - value ：修饰字段

   

4. 创建bean的三种方式：
   - 构造器 ：id ，class， 无参构造
   - 静态工厂：class为静态工厂类，factory-method指定方法，constructor-arg传参
   - 实例工厂：id，class工厂实例，配置一个工厂bean。 factory-bean-工厂实例id，factory-method指定方法，constructor-arg传参

5. 工厂bean：

   实现了FactoryBean接口，叫做工厂bean。

   方法：

   - T getObject() : 实现该方法负责返回工厂bean生成的java实例
   - Class<?>getObjectType() : 返回java实例的实现类
   - boolean isSingleton : java实例是否为单例

   和静态工厂实例工厂的区别：

   - 上述只是spring调用工厂方法，这个是特殊的springbean。
   - 上述通过getBean()返回的是一个实例，这个返回的是工厂中的产品getObject()的返回值。getObject方法可以重写，控制返回什么。

6.  容器中的bean：
   - 获得bean本身id：实现BeanNameAware接口，setBeanName方法
   - 强制初始化：depends - on="a",必须在a之前初始化

7. spring中bean的生命周期：

   单例：创建、初始化、销毁

   prototype：不管理

   - 依赖注入之后

     - init-method属性：设置依赖关系结束后自动执行

     - InitializingBean接口----void afterPropertiesSet()实现这个接口，调用这个方法

     执行过程：先实例化依赖bean，实例化主调bean，调用setter方法进行依赖注入，执行setBeanName和setApplicationContext方法，执行bean初始化。

     同时写上面两个方法，先执行接口方法，后执行属性方法。

   - 销毁之前——jvm注册关闭钩子

     - destroy-method属性--后
     - disposableBean接口----void destroy()方法--先

8. 方法注入：

   当单例bean依赖prototype时，依赖注入仍为第一次的bean，造成不同步现象。

   lookup方法注入原理：让spring容器重写容器中bean的抽象或具体方法，返回查找的bean结果。——spring通过JDK动态代理或cglib库修改客户端二进制码实现。（有没有实现过接口，有jdk,无cglib）。

   使用： a. 调用bean 类变成抽象类，增加抽象方法

   ​			b. 配置文件中，调用bean上增加lookup-method  name =getDog ,bean=111,

   ​            c. 依赖bean 111需要设置为scope = prototype

   spring 执行代码为：

   public  依赖bean类型  抽象方法名（）

   获取spring容器cxt

   return cxt. getbean(依赖bean的id)

9. 其他依赖关系配置

   - 获取其他bean的属性值

   - 获取field值

   - 获取方法返回值

10. 简化配置：
    - p: 属性注入setter方法
    - c: 属性注入构造器参数
    - util：静态field值；getter返回值；其他集合

### 深入使用

1. 后处理：
   - bean 后处理：BeanPostProcessor
     - beanNameAutoProxyCreator,创建Bean实例的代理
     - DefaultAdvisorAutoProxyCreator:根据提供的
   - 容器后处理:BeanFactoryPostProcessor
     - 属性占位符配置器
     - 重写占位符配置器

2. annotation注解

   bean注解b：修饰类

   - @component --普通组件
   - @controller("别名"正常不写就是驼峰) 或@restController--控制层
   - @service ---业务层
   - @Repository --dao层

   规定作用域：bean注解下

   - @scope（“prototype”）

   配置依赖：set方法或者field

   - @Resource(name = "id")

   生命周期：

   - PostConstruct ：初始化后
   - PreDestroy: 销毁前

   自动装配：

   - @Autowired: Bytype过类型找对应的bean作为参数注入
     - 标注setter方法
     - 标注普通方法，参数类型
     - 标注实例变量和构造器
- @qualifier：ByID beanName

# MyBatis

### 基本使用

#### 步骤：

1. 查找官网官方文档
2. 连接数据库，建表等
3. 导入依赖
4. 获取sqlSession对象——编写工具类
5. 配置Mybatis-config.xml文件
6. 编写数据库实体类
7. 编写dao接口
8. 配置usemapper.xml——类似于实现类

#### 注意问题：

1. mapper注册问题
2. Maven资源文件过滤问题

#### 核心接口

1.SQLSessionFactoryBuilder ，通过流加载xml文件，创建工厂实例，用完就扔释放xml资源，局部变量就行。

2.SQLSessionFactory ，创建产品实例，单例模式

3.SqlSession , 操作数据库方法，非线程安全的，不能共享，每次请求后都要关闭（放在finally块中）

```java
try(SqlSession sqlSession = new SqlSessionFactory.openSession（）){
    //TODO
}
```

### CRUD

1. Select: 查询语句

   - id :对应的namespace中的方法名

   - resultType： sql返回值

   - parameterType：参数类型。

     xml标签中编写SQL语句，用#{id}取变量名(传入的参数名)

     ```xml
     select * from Mybatis.user where id = #{id}
     ```

2. insert: 后面都需要提交事务 session.commit();

   ```xml 
   insert into mybatis.user(id,name,pwd) values(#{id},#{name},#{pwd})
   ```

3. update

   ```xml
   update mybatis.user set name=#{name},pwd=#{pwd} where id=#{id}
   ```

4. delete

   ```xml
   delete from mybatis.user where id=#{id}
   ```


