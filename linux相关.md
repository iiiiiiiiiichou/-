# Linux虚拟机安装

1. 下载VMwareWorkstation和Cenos7镜像文件
2. 安装虚拟机 [教程]( https://blog.csdn.net/swf_shixinshou/article/details/80311796)
3. 遇到了密码输入问题：输入密码时候不显示，输错了会显示 login incorrect，输入正确界面

![image-20210112170219409](C:\Users\fanfan\AppData\Roaming\Typora\typora-user-images\image-20210112170219409.png)

4. 配置ip
   - 查看ip ：#ip addr show 
   - 手动配置ip [配置IP步骤](https://blog.csdn.net/qq_40784783/article/details/79025704)
   - 没有eth0，将ens33修改成  [eth0](https://my.oschina.net/u/4403820/blog/4036424)
   - 修改内容，vi 编辑，i输入，esc退出，：wq保存退出，注意“：”！！！
   - 关闭防火墙报错：unit iptables.service not loaded
   - yum install iptables-services
   - ping ip 要加次数，ping -c 4 www.baidu.com 表示4次。否则通过ctrl + c中断

5. 最经遇到的问题——敲错命令怎么搞 ctrl + c中断，返回有用户名的状态

# Linux安装Mysql

1. 查询是否自带：rpm -qa|grep mysql
2. 下载Mysql,怎么把安装包拷到虚拟机上：

   - mobaXterm 下载，登录session上不去。。。ip不对。重新检查ip后可以了

     ![20210114111624](C:\Users\fanfan\Documents\Scrshot\20210114111624.png)

   - linux创建文件夹，失败。不存在目录。![img](C:\Users\fanfan\AppData\Local\Temp\企业微信截图_16105911748073.png)

   - 查看目录：[命令](https://blog.csdn.net/qq_35732963/article/details/53082527)

   - linux目录结构：[菜鸟教程](https://www.runoob.com/linux/linux-system-contents.html)

     ![img](https://www.runoob.com/wp-content/uploads/2014/06/d0c50-linux2bfile2bsystem2bhierarchy.jpg)

   - /usr,共享资源文件夹，不是自己随便写的user！！！usr内包含如下几种：

     ![20210114104056](C:\Users\fanfan\Documents\Scrshot\20210114104056.png)

   - 最终新建/usr/local/mysql

3. 安装Mysql,更改配置

   - 通过mobaXterm上传压缩包到/usr/local/mysql目录下

   - 当前目录下解压缩，完成后删除安装包

     ```shell
     cd /usr/local/mysql
     tar -xvf mysql-8.0.21-linux-glibc2.12-x86_64.tar.xz 
     rm  rm mysql-8.0.21-linux-glibc2.12-x86_64.tar.xz
     ```

   - 重命名

     ```shell
     mv /usr/local/mysql/mysql-8.0.21-linux-glibc2.12-x86_64 /usr/local/mysql/mysql
     ```

   - 创建mysql用户和组，并将用户添加到组

     为啥：安全策略，一个单独用户跑单独服务，防止root权限泄露

     ```shell
     groupadd mysql
     useradd -r -g mysql mysql
     ```

     命令参数：useradd 参数选项 参数

     ```xml
     -c<备注>：加上备注文字。备注文字会保存在passwd的备注栏位中；
     -d<登入目录>：指定用户登入时的启始目录；
     -D：变更预设值；
     -e<有效期限>：指定帐号的有效期限；
     -f<缓冲天数>：指定在密码过期后多少天即关闭该帐号；
     -g<群组>：指定用户所属的群组；
     -G<群组>：指定用户所属的附加群组；
     -m：自动建立用户的登入目录；
     -M：不要自动建立用户的登入目录；
     -n：取消建立以用户名称为名的群组；
     -r：建立系统帐号；
     -s<shell>：指定用户登入后所使用的shell；
     -u<uid>：指定用户id。
     ```

   - 检查libaio

     - 为啥：libaio是Linux下的一个异步非阻塞接口，它提供了以异步非阻塞方式来读写文件的方式，读写效率比较高。缺少可能会导致安装失败“MySQL初始化数据时报错：error while loading shared libraries: libaio.so.1”

     - ```shell
       rpm -qa|grep libaio
       #或者
       whereis libaio.so.1
       ```

   - 配置my.cnf文件

     ```shell
     [mysqld_safe]
     log-error=/var/log/mariadb/mariadb.log
     pid-file=/var/run/mariadb/mariadb.pid
     ```

     没改这几句，后面重启服务的时候报错

   - 修改文件权限，给mysql

     ```shell
     chown -R mysql:mysql /usr/local/mysql/mysql
     chmod _R 755 /usr/local/mysql/mysql
     ```

   - 初始化mysqld 生成初始化密码

     ```shell
     ./bin/mysqld --initialize --user=mysql --datadir=/usr/local/mysql/data --basedir=/usr/local/mysql
     ```

     临时密码：eGw9Baek2S>C

   - 添加系统服务并设置开机启动

     ```shell
     cp ./support-files/mysql.server /etc/init.d/mysqld
     chmod 777 /etc/init.d/mysqld
     chkconfig --add mysqld
     chkconfig --list mysqld
     ```

   - 启动mysqld：

     报错了，应该是上面配置文件修改错误，然后网络断开连接。。。
     
     重启虚拟机，修改my.cnf配置文件，注释不需要的代码。重启mysqld服务成功
   
   - 配置环境变量
   
     ```shell
     mysql]# vi /etc/profile
     #配置内容
     PATH=$PATH:/usr/local/mysql/mysql/bin
     export PATH
     #使配置文件生效
     source /etc/profile
     #查看是否配置成功
     echo $PATH
     ```
   
   - 登录mysql
   
     ```shell
     mysql -u root -p
     #密码为初始化mysqld时生成的临时密码
     ```
   
   - 修改登录密码
   
     ```shell
     ALTER USER 'root'@'localhost' identified by '123456' ;
     ```
   
   - 开放远程连接
   
     ```shell
     mysql>use mysql;
     msyql>update user set user.Host='%' where user.User='root';
     mysql>flush privileges;
     ```
   
   - 退出重启
   
     ```shell
     exit;
     service mysqld start
     ```
   
   - 关闭防火墙
   
     ```shell
     systemctl start firewalld # 开启防火墙
     systemctl stop firewalld  # 关闭防火墙
     systemctl status firewalld  #检查防火墙状态
     ```
   
   - 下载Navicat连接
   
     ![20210115142348](C:\Users\fanfan\Documents\Scrshot\20210115142348.png)