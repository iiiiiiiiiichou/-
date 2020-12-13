### 第一章 Java Web结构

目录结构：

WEB-INF:文件夹内包含编译好的class文件，lib中包含依赖的jar包，tags包含JSP的标签文件，tld中包含标签库描述符；

META-INF:应用程序清单文件，



### 第三章 Servlet

1. Maven：

POM( Project Object Model，项目对象模型 ) 是 Maven 工程的基本工作单元，是一个XML文件，包含了项目的基本信息，用于描述项目如何构建，声明项目依赖，等等。

| 节点         | 描述                                                         |
| :----------- | :----------------------------------------------------------- |
| project      | 工程的根标签。                                               |
| modelVersion | 模型版本需要设置为 4.0。                                     |
| groupId      | 这是工程组的标识。它在一个组织或者项目中通常是唯一的。例如，一个银行组织 com.companyname.project-group 拥有所有的和银行相关的项目。 |
| artifactId   | 这是工程的标识。它通常是工程的名称。例如，消费者银行。groupId 和 artifactId 一起定义了 artifact 在仓库中的位置。 |
| version      | 这是工程的版本号。在 artifact 的仓库中，它用来区分不同的版本。例如：`com.company.bank:consumer-banking:1.0 com.company.bank:consumer-banking:1.1` |

每个用户在自己的用户目录下都有一个路径名为 .m2/respository/ 的仓库目录，本地仓库。一般从远程仓库或者中央仓库下载到本地。

pom.xml和setting.xml

可传递性依赖

导入IDE

2. html css js
3. servlet jstl jsp webxml http 
4. 框架 hibernate   mybatis spring springmvc springboot
5. 数据库 mysql部署   redis部署  sql语句 
6. linux鸟哥的私房菜 io 文件操作/进程/top

2. Tomcat

web服务器 ：容器，能够部署WEB程序，目录结构分别是干什么的

 ip：电子设备唯一标识

端口号：应用程序唯一标识

找端口号：cmd--netstat -ano--pid进程id--任务管理器--找到进程杀死

访问路径：虚拟目录，可以修改文件夹名称

部署方式：——直接文件夹放入webApps中或war包自动解压删除，不需要重启

​				——通过配置文件，server.xml--host--添加Context标签--docBase和path

​				——Catalina--host--添加xml--Context标签--docBase——热处理，不用关闭服务

3.  Servlet：server applet 运行在服务器端的小程序 是一个接口，定义了Java类被浏览器访问（Tomcat识别）的规则。

    创建javaee项目

   定义类

   实现接口方法

   配置

4. http协议

   数据格式：请求行，请求头，请求空行，请求体

   request和response

   通用方法获取请求参数

   设置编码

   重定向和转发

5. cookie

   记住登录时间案例：

   - 需求：
     * 1.第一次访问，显示您好，欢迎您首次访问
     * 2.不是第一次，显示欢迎回来，您上次访问时间为：时间字符串

   - 问题：
     - 怎么判断是不是第一次？——请求头中是否有cookie
     - 怎么在页面中显示参数字符串——从相应中写入页面
     - 怎么确定上次登录时间——以刚登录为准还是退出时间为准。

   - 方案：
     1. 创建servlet类，使用request和response对象
     2. 判断cookie是否存在
     3. 页面处理逻辑

6. JSP
   - <%%>可以写java代码
   - 内置对象 9

7. session

   验证码案例：

   - 需求

     - 带有验证码的登录页面 login.jsp

     - 用户输入用户名、密码、验证码

       若输入错误，登录页面提示若用户名、密码或验证码错误；

     ​       若输入正确，跳转success.jsp页面，显示：用户名，欢迎您；



8. el表达式

   ${表达式}，能够从域对象中取值判断

9. jstl 

   通过taglib命令导入标签库，简化java代码

   if choose foreach

### 补充笔记：

1. Servlet 执行原理：

- 当浏览器发送请求给服务器时，Tomcat解析URL，获取资源路径
- 在web.xml中的配置，查找是否有对应的<URL pattern>标签
- 有则寻找到对应的全类名<servlet-class>
- 通过反射class.forName(),解析字节码文件进内存，创建Servlet对象NewInstance()
- 调用Service()方法——执行具体处理

2. 转发和重定向

   - 重定向：地址栏发生变化，URL资源跳转，可以访问任意资源，两次请求，不能使用request域对象共享数据

   ![image-20201213212651697](C:\Users\fanfan\AppData\Roaming\Typora\typora-user-images\image-20201213212651697.png)

    - 转发：地址栏不发生变化，只能访问内部资源，一次请求

      ![image-20201213215725516](C:\Users\fanfan\AppData\Roaming\Typora\typora-user-images\image-20201213215725516.png)

      

3. 响应状态码：

   1XX：传输未完成，服务器接收客户端消息未完成

   200：成功

   3XX：重定向302，304访问缓存

   4XX：客户端错误  404资源不存在，405找不到对应方法

   500：服务器错误

4. Cookie和Session

   Cookie保存在客户端浏览器中，一般4KB大小，通过响应头中添加Cookie（set-cookie：msg=hello），再次访问时在请求头中也有此Cookie信息（cookie：msg=hello），可以设置失效时间setMaxAge

   ![image-20201213233644378](C:\Users\fanfan\AppData\Roaming\Typora\typora-user-images\image-20201213233644378.png)

   Session保存在服务器中，可以存储很多内容，相对安全实现原理其实是基于Cookie的，在第一次会话时生成JSESSIONID=238472736放到响应头中的set-cookie中，再次请求时在请求头中带入，保证同一个Session对象。可以通过cookie设置时间来解决浏览器关闭造成的Session失效，Tomcat做了Session的钝化和活化。

   ![image-20201213233948624](C:\Users\fanfan\AppData\Roaming\Typora\typora-user-images\image-20201213233948624.png)

   session的销毁：服务器关闭；invalidate；默认失效时间30min<session-timeout>

5. JSP原理：本质是个servlet,Tomcat内部service方法将HTML标签通过out对象输出到页面上

![image-20201214000051637](C:\Users\fanfan\AppData\Roaming\Typora\typora-user-images\image-20201214000051637.png)