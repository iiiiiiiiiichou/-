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
   - mobaXterm [相关学习]( https://blog.csdn.net/Fighting_Boss/article/details/79103822)