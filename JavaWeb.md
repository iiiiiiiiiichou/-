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

找端口号：cmd--netstate -ano--pid进程id--任务管理器--找到进程杀死

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



