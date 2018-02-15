# 关于Docker

---
![](https://gss2.bdstatic.com/9fo3dSag_xI4khGkpoWK1HF6hhy/baike/crop%3D0%2C156%2C1354%2C894%3Bc0%3Dbaike180%2C5%2C5%2C180%2C60/sign=c97c7c9b9b13b07ea9f20a4831e7bd12/f703738da977391281957edbf0198618377ae2dd.jpg)
### 简介 (introduction)
 Docker运用的是集装箱的思想，将各个应用分别封装在容器中，相互隔离。比如.net开发网站和php开发网站依赖的软件不同，将它们放一个服务器上调试就很麻烦，还可能造成端口访问冲突等问题。就好比动物园里的动物需要生活在不同的环境一样。docker实现了虚拟机隔离应用环境的功能，开销也比虚拟机小，内存利用率自然提高。同时，它的轻量性和兼容性也让使用者能进行高效的部署。
#### **传统虚拟方式**              
 ![](http://hainiubl.com/images/2016/virtualization.png)
#### **容器虚拟方式** 
 ![](http://hainiubl.com/images/2016/docker.png)
 
**比较可以看出docker engine管理的容器是在操作系统上实现虚拟化，而传统虚拟是在硬件层上实现的。**
###  安装(Install)
 Docker可以下载到linux,centos,ubuntus和windows环境，官方的教程很详细，就不介绍了。**值得一提**，如果是windows的话，要注意Docker for windows 是只支持win10e版和p版的，如果你是家庭版，可以去网上 搜索升级专业版的产品密钥，但如果你不知道升级后如何激活系统的话，建议下载Docker toolbox。*（Ps:不是很推荐toolbox，因为网友吐槽不怎么稳定）*
### 镜像-仓库-容器
![](http://wx1.sinaimg.cn/mw690/006AGGiwgy1foggventvuj316k0ni4d7.jpg)
* **镜像（image）**
是一个只读层模板（only-read-layer),可以用来创建Docker容器。用户可以从[Docker Hub](https://hub.docker.com/)或者其他人直接pull做好的镜像来使用
* **仓库（repository）**
 是集中存放镜像文件的场所,分为公开仓库（Public）和私有仓库（Private）。前面说的Docker Hub就是最大的公开仓库。概念类似于Git。
* **容器（container）**
Docker 利用容器（Container）来运行应用。容器是从镜像创建的运行实例。它可以被启动、开始、停止、删除。每个容器都是相互隔离的、保证安全的平台

### 基本命令(CLI)
![](http://wx3.sinaimg.cn/mw690/006AGGiwgy1foghe2hclbj30xw098q7a.jpg)
#### 示范
 *  以redis为例用pull拉取镜像
  ![](http://wx2.sinaimg.cn/mw690/006AGGiwgy1foghe7tzxgj311l09zwfp.jpg)
  *(Ps:由于国外的镜像pull公认的特别慢 可以在阿里，腾讯，Daocloud 上获取免费的Docker加速器。 我第一次pull时，40M的镜像我傻等了20分钟...)*
 * 查看自己的镜像
   ![](http://wx4.sinaimg.cn/mw690/006AGGiwgy1foghe5la23j313003p0t5.jpg)
 * 运行镜像创建名为redis 0的容器并映射默认端口(redis是6379) 
   ![](http://wx3.sinaimg.cn/mw690/006AGGiwgy1foghy4h9aqj30ym01v0sv.jpg)
 * 可以用redis desktop manager检测一下 连接成功~
 ![](http://wx4.sinaimg.cn/mw690/006AGGiwgy1fogheajrfoj30xg13dq5p.jpg)
 * 这时候查看运行状况
 ![](http://wx1.sinaimg.cn/mw690/006AGGiwgy1foghedriu4j31he04m0t7.jpg)
 可以看到2min 前创建并运行了1min。

当然还有一些基本命令我就不介绍了，这给了中文版的官方介绍https://docs.docker-cn.com/get-started/。

### 数据卷（Data Volume）
![](http://wx4.sinaimg.cn/mw690/006AGGiwgy1fogkb9q5esj318g0tnwf8.jpg)

* 什么是数据卷？
 Docker中存储在类似于虚拟机磁盘中的数据
* 特性
 *  一个数据卷是一个特别指定的目录，该目录利用容器的UFS文件系统可以为多个容器提供一些稳定的特性或者数据共享。
 *  对数据卷的修改会立马生效 ，且卷会一直存在，直到没有容器使用数据卷。
 *  容器在运行期间产生的数据是不会写在镜像里面的，重新用此镜像启动新的容器就会初始化镜像，会加一个全新的读写入层来保存数据。 从而做到Docker数据持久化。
*  实现
  * 数据卷在容器间共享，重用数据。在宿主和容器见共享数据 。
  * 绕过写时拷贝（[Copy on write](http://blog.csdn.net/yangshuangtao/article/details/51496615)）系统，以达到本地磁盘IO的性能。 比如运行一个容器，在容器中对数据卷修改内容，会直接改变宿主机上的数据卷中的内容。

*大致框架如图*：

![](http://wx3.sinaimg.cn/mw690/006AGGiwgy1fogkqr7807j30jm0cme3d.jpg)

###在Mysql下挂载Host Volume

#### **环境的部署**
1 .拉取mysql镜像
```
docker pull mysql/mysql-server`
```    
2 .映射默认端口3306为容器(mysql01）
```
docker run -d -p 3306:3306 --name mysql01 mysql/mysql-server
```

3 .检查下是否运行，并查看日志
```
docker ps
docker logs mysql01
```
可以看到如图
![](http://wx2.sinaimg.cn/mw690/006AGGiwgy1foh3rd0p2gj31ag0j7jua.jpg)

copy下来默认的mysql password.
这时可以用Navicat连接, 发现连接不上,因为没有配置Mysql的环境变量和用户.
![](http://wx2.sinaimg.cn/mw690/006AGGiwgy1foh3xn28dzj318g0oj76i.jpg)

4 .所以接下来我们得进入容器，先修改密码
```
docker exec -it mysql01 bash// exec -it +name 进入容器
mysql -uroot -p
```
 
 然后粘贴刚才的password 进入Mysql
``` 
SET PASSWORD FOR 'root'@'localhost' = PASSWORD('password123');//修改密码`
use mysql
```

这时成功的进入Mysql，我们再看看User表
```
select user,host from user;
```

![](http://wx2.sinaimg.cn/small/006AGGiwgy1foh4prgykoj30dt07dq30.jpg)
嗯 可以看到root 只能通过Localhost来登录，这就是为什么我们不通过Host来连接的原因。

5 .创建User
```
CREATE USER 'Zarc'@'localhost' IDENTIFIED BY 'pwd123456';
 GRANT ALL PRIVILEGES ON *.* TO 'Zarc'@'localhost'
    ->     WITH GRANT OPTION;
CREATE USER 'Zarc'@'%' IDENTIFIED BY 'pwd123456';
GRANT ALL PRIVILEGES ON *.* TO 'Zarc'@'%'
    ->     WITH GRANT OPTION;
```

再看User表 ，发现多了两个User 区别就是host为 **%** ,可以被外部访问
```
select user,host from user;
```
![](http://wx2.sinaimg.cn/small/006AGGiwgy1foh5gasvswj30dc08tdg0.jpg)

![](http://wx3.sinaimg.cn/mw690/006AGGiwgy1foh5gdq9aej313r0m80ul.jpg)

6 .这还有个小问题，就是如果创建的Mysql没用配置默认的字符集，会导致出现中文乱码的情况，所以我们还要在my.cnf中加点配置，改成utf8的形式。

在刚才的基础上执行
```
show variables like '%char%';
```
可以查看字符集表， 后面我们要改这些配置
```
 exit //退出Mysql
 cd /etc
 ls //可以看到my.cnf
``` 
 接下来 我们用vim编辑它 但目前环境没有 
```
 yum install vim// 安装都选y即可`
```
 安装好后
```
 cd /etc
 vim my.cnf
```
 按C进入insert模式 ，
 然后将下列配置加到[mysqld]下
```
character-set-server=utf8 
[client]
default-character-set=utf8 
[mysql]
default-character-set=utf8
```
Ok 接着Esc退出模式 shift+: 输入wq 保存退出vim
这时cat一下，可以看到修改的配置
这时候重启mysql就行了
7. 每次这样手动配置都很麻烦，但官方给了环境配置的方法，可以在创建容器时，直接完成一系列操作。

```
 docker run -d -p 3306:3306 -e"MYSQL_USER=Zarc" -e"MYSQL_PASSWORD=pwd123456" -e"MYSQL_ROOT_PASSWORD=password123" -e "MYSQL_RANDOM_ROOT_PASSWORD=true" --name mysql01 mysql/mysql-server --character-set-server=utf8 --collation-server=utf8_general_ci
备注： 当这个MYSQL_RANDOM_ROOT_PASSWORD 为true 时 为root用户设置密码是没有作用的还是会生成随机的密码，设置为false 或者不加这个环境变量 
```
环境的部署都可以在官网找到 比如Mysql的https://dev.mysql.com/doc/mysql-installation-excerpt/5.7/en/
你也可以试试部署Mongo 

#### **挂载数据卷**
1. 创建数据卷有两种形式mount 和 volume
mount 更倾向于 集群（新版本也可以单机） ，volume 适用于单机 （*PS：现在用的不是很多。*）
(接下来我使用Putty在腾讯云的centos上操作 ，windows不造出了什么问题...)
2. 创建时我们先生成一个放配置的文件夹
docker/mysql/config/my.cnf和data
```
 mkdir docker
 cd docker/
 mkdir mysql
 cd mysql/
 mkdir config
 mk data
 cd config/
 vi config
```
 配置config信息
```
 [mysqld]
 user = mysql
```
保存退出。接下来输入外部volume的创建代码
```
docker run -d -p 3306:3306 --name mysql01 -v=/docker/mysql/config/my.cnf:/etc/my.cnf -v=/docker/mysql/data:/var/lib/mysql mysql/mysql-server
```
返回容器id，然后像之前一样找到password
```
docker logs mysql01
docker exec -it mysql01 bash
mysql -uroot -p
//登录 跟之前操作一样
SET PASSWORD FOR 'root'@'localhost' = PASSWORD('password123');//修改密码`
use mysql
show variables like '%char%';
```
这个时候 就是默认没修改的字符集配置
我们接下来做的就是 不在docker中修改 而在Host中修改 my.cnf 再删除后 运行新的容器 可以发现 配置已经改变 达到与Host挂载的效果。
```
exit //退出mysql
exit //退出Bash
cd /docker/mysql 
vi my.cnf //像之前那样加入字符集

character-set-server=utf8 
[client]
default-character-set=utf8 
[mysql]
default-character-set=utf8
//修改后
docker restart mysql01
```
这时再进入容器，查看字符集表，发现已经更改为utf8了
![](http://wx4.sinaimg.cn/mw690/006AGGiwgy1fohfc4hrb7j30iw071n0q.jpg)
然后再退回到Host 删除容器
```
docker stop mysql01
docker rm mysql01
docker ps -a//检查进程
//创建新的容器
docker run -d -p 3306:3306 --name mysql01 -v=/docker/mysql/config/my.cnf:/etc/my.cnf -v=/docker/mysql/data:/var/lib/mysql mysql/mysql-server
//再进入容器 和mysql password是之前配置好的密码 
show variables like '%char%';
//发现还是之前的utf8编码
```





 


  
