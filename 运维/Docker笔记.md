<h1 style="font-weight:bold;"><center>Docker学习笔记</center></h1>

# 第一章 Docker基本命令

## 1.1 查看容器相关命令

​		1.查看docker正在运行的容器情况

````
docker ps
````

​		2.查看docker中已安装的镜像情况

```
docker images
```

## 1.2 容器操作相关命令

​		1.在容器里开启一个mysql伪终端交互

```
docker exec -it [容器名] bash

实例：
docker exec -it mysql bash
```

​		2.载入本地docker镜像

```
docker load -i [镜像文件完整路径]

实例：
docker load -i /usr/local/comtom/dockerimages/alpine-java.tar
```

​		3.保存docker镜像到本地

````
docker save -o [镜像文件完整路径及命名] [当前镜像标签名]

实例：
docker save -o /usr/local/comtom/dockerimages/rabbitmq.tar registry.comtom.cn:2443/gd-v5/rabbitmq:3.8.0
````

## 1.3 删除容器及镜像(以mysql为例)

​		1.查看docker正在运行的容器情况

````
docker ps
````

![1571121219505](assets/1571121219505.png)

​		2.停止运行容器（容器名字或ID均可）

````
docker stop 1a8eb1e42729
docker stop mysql
````

​		3.删除容器（容器名字或ID均可）

````
docker rm 1a8eb1e42729
docker rm mysql

docker rm -f mysql				(强制删除容器)
````

​		4.删除镜像（ID或镜像名字均可，名称后需要加上冒号和版本号）

````
docker rmi 1a8eb1e42729
docker rmi mysql:latest

docker rmi -f mysql:latest		(强制删除镜像)
````

# 第二章 Docker安装镜像及使用

​		大前提：当前操作系统上已安装好Docker，可输入下面命令查看docker版本

````
docker -v
````

## 2.1 安装MySQL

​		1.在docker仓库中搜索mysql的镜像

```
docker search mysql
```

​		2.下载mysql镜像

```
docker pull mysql
```

​		3.运行mysql容器

- **-p 3306:3306**：将容器的 3306 端口映射到主机的 3306 端口。
- **-v $PWD/conf:/etc/mysql/conf.d**：将主机当前目录下的 conf/my.cnf 挂载到容器的 /etc/mysql/my.cnf。
- **-v $PWD/logs:/logs**：将主机当前目录下的 logs 目录挂载到容器的 /logs。
- **-v $PWD/data:/var/lib/mysql** ：将主机当前目录下的data目录挂载到容器的 /var/lib/mysql 。
- **-e MYSQL_ROOT_PASSWORD=1234：**初始化 root 用户的密码为1234。

```
docker run -p 3306:3306 --name mysql -v $PWD/conf:/etc/mysql/conf.d -v $PWD/logs:/logs -v $PWD/data:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=1234 -d mysql
21cb89213c93d805c5bacf1028a0da7b5c5852761ba81327e6b99bb3ea89930e
```

​		4.



​		7.退出docker中的mysql交互窗口

````
exit
````



## 2.5 安装Git

​		1.查看是否安装过git

````
rpm -qa|grep git
````

​		显示有以`git-`+版本号的字符，表示已经装过git

​		2.若已经安装，需要先卸载（两条命令任选其一即可）

````
rpm -e --nodeps git
rpm -e git
````

​		3.

## 2.6 安装Redis

## 2.7 安装MongoDB

## 2.8 安装erlang

​		1.安装前置依赖

````
yum install ncurses-devel
````

​		2.解压

````
tar -zxvf otp_src_20.1.tar.gz
````

​		3.进入解压后的erlang目录，然后执行下面命令（不安装javac）

````
./configure --prefix=/usr/local/erlang20 --without-javac
````
​		4.在当前目录直接执行编译，然后再执行下面的安装命令

````
make				（编译）
make install		（安装）
===============================================
make -j 4	(四核cpu编译可采取这个方法，效率更高)
````

​		5.运行erlang，执行erlang安装路径中bin路径下的erl文件即可

````
./bin/erl
````

​		6.进入系统文件（/etc/profile），添加环境变量，并重新加载此文件

````
vim /etc/profile
按a进入编辑模式，在底部添加这句话：
export PATH=$PATH:/usr/local/erlang20/bin
先按ESC退出编辑：
:wq			（保存更改并退出）
source /etc/profile		（使profile文件修改生效）
````

## 2.9 安装RabbitMQ

​		**请装好erlang后再执行下述步骤**

​		1.安装python：

````
yum install python -y
````

​		2.安装simplejson（-y是后续询问步骤直接yes）

````
yum install xmlto -y
yum install python-simplejson -y
````

​		进行步骤3之前，要求移动到统一自定义文件夹`/usr/local/rabbitmq`下

```
mv rabbitmq_server-3.6.14 /usr/local/rabbitmq
```

​		3.先解压xz，再解压tar

````
xz -d rabbitmq-server-generic-unix-3.6.14.tar.xz
tar xf rabbitmq-server-generic-unix-3.6.14.tar
````

​		4.进入rabbitmq路径下sbin目录启动rabbitMQ server

````
./rabbitmq-server
./rabbitmq-server stop		(关闭rabbitMQ服务)
````

​		5.查看rabbitMQ server服务（默认5672端口）

````
netstat -nap|grep 5672
````

​		6.进入系统文件，添加环境变量，并重新加载此文件

```
vim /etc/profile
按a进入编辑模式，在底部添加行代码：
export PATH=$PATH:/usr/local/rabbitmq/sbin

先按ESC退出编辑：
:wq			（保存更改并退出）
source /etc/profile		（使profile文件修改生效）
```

## 2.10 安装ActiveMQ

​		1.在/usr/local目录下创建activemq文件夹（为了运维规范化，建议建立在/usr/local下）

```
mkdir /usr/local/activemq
cd /usr/local/activemq
```

​		2.将下载好的ActiveMQ通过xftp（或其他ftp工具）传到linux上的`/usr/local/activemq/`文件夹下, 下载地址：http://activemq.apache.org/activemq-5155-release.html

​		3.解压tar包到当前文件夹（activemq）下

```
tar -zxvf apache-activemq-5.15.5-bin.tar.gz
```

​		4.启动/停止/查看ActiveMQ服务，首先需要进入`apache-activemq-5.15.5`目录（当前操作可跳过）

```
cd /usr/local/activemq/apache-activemq-5.15.5	（进入目录）

bin/activemq start				(启动activemq服务)
bin/activemq status				(查看activemq服务状态)
bin/activemq stop				(停止activemq服务)
```

​		5.为了在任意地方通过`service activemq start `就可以启动activemq，配置`JAVA_HOME`地址，然后建立软连接

```
vim /usr/local/activemq/apache-activemq-5.15.5/bin/activemq
按a进入编辑模式，在第一行添加这句话：
export JAVA_HOME="/usr/local/java/jdk1.8.0_211"
先按ESC退出编辑：
:wq			（保存更改并退出）

ln -s /usr/local/activemq/apache-activemq-5.15.5/bin/activemq /etc/init.d	(建立软连接)
```

​		6.配置开机启动，可通过系统指令在任意位置进行启动/停止/查看activemq服务

````
chkconfig activemq on				(设置开机启动)

service activemq start				(启动activemq服务)
service activemq status				(查看activemq服务状态)
service activemq stop				(停止activemq服务)
````

​		7.配置防火墙开放ActiveMQ端口

````
firewall-cmd --zone=public --add-port=8161/tcp --permanent		(开放8161端口)
firewall-cmd --reload											(重启防火墙)
````

​		8.打开浏览器访问`http://虚拟机IP地址:8161`，账号密码均为`admin`

## 2.11 安装Docker

​		Docker 要求 CentOS 系统的内核版本高于 3.10 。

​		1.查看操作系统当前的内核版本

````
uname -r
````

​		2.更新yum包到最新

````
yum update
````

​		3.卸载旧版本（如果存在旧版本，不存在则跳过）

````
yum remove docker docker-common docker-selinux docker-engine -y
````

​		4.安装docker需要的软件，yum-util提供yum-config-manager功能，其他两个是devicemapper驱动依赖的包

````
yum install -y yum-utils device-mapper-persistent-data lvm2
````

​		5.配置yum源

````
yum-config-manager --add-repo http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo			（阿里云）

yum-config-manager --add-repo http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo			（官方源）

yum-config-manager --add-repo https://mirrors.ustc.edu.cn/docker-ce/linux/centos/docker-ce.repo				（中国科技大学）
````

​		6.查看所有仓库中所有docker版本，并选择特定版本进行安装

````
yum list docker-ce --showduplicates | sort -r
````

​		7.安装docker

````
yum -y install docker-ce			(默认装最新稳定版)
````

​		8.启动docker服务

````
systemctl start docker		(启动)
systemctl stop docker		(关闭,安装过程不要执行)
````

​		9.开机启动docker服务

````
systemctl enable docker		(启动)
systemctl disable docker	(关闭,安装过程不要执行)
````

​		10.查看docker版本（有client和server表示docker安装成功）

````
docker version
````

## 2.12 安装Jenkins

​		1.下载Jenkins库

```
wget -O /etc/yum.repos.d/jenkins.repo https://pkg.jenkins.io/redhat-stable/jenkins.repo
```

​		2.导入key

````
rpm --import https://pkg.jenkins.io/redhat-stable/jenkins.io.key
````

​		3.安装Jenkins

```
yum install -y jenkins
```

​		4.启动/停止/查看Jenkins服务（执行启动即可）

```
systemctl start jenkins				(启动activemq服务)
systemctl status jenkins				(查看activemq服务状态)
systemctl stop jenkins				(停止activemq服务)
```

​		5.因为Jenkins默认端口是8080，可能会导致端口冲突，修改Jenkins的默认端口即可

```
vim /etc/sysconfig/jenkins

直接输入（搜索关键字）：
/JENKINS_PORT

按a进入编辑模式，将JENKINS_PORT后的端口改为需要的：
JENKINS_PORT=8888

先按ESC退出编辑：
:wq			（保存更改并退出）
```

​		6.配置防火墙开放Jenkins端口

```
firewall-cmd --zone=public --add-port=8888/tcp --permanent		(开放8888端口)
firewall-cmd --reload											(重启防火墙)
```

​		7.打开浏览器访问`http://虚拟机IP地址:8888`(192.168.241.129:8888)

​		8.在服务器上查看当前的初始化密码

````
cat /var/lib/jenkins/secrets/initialAdminPassword
````

## 2.13 安装fastDFS

​		查看是否安装成功

```
查看storage日志：
docker logs -f -t --tail 666 storage

进入fastdfs容器：
docker exec -it fastdfs /bin/bash

现场写入一个html文件：
echo "Hello FastDFS!">index.html

上传此html到fastdfs：
fdfs_test /etc/fdfs/client.conf upload index.html

浏览器输入example file url访问
```

# 第三章 Docker镜像操作

## 3.1 打包镜像至私有仓库

​		1.登陆私有仓库，输入账号密码即可

````
docker login [地址]

实例：
docker login registry.comtom.cn:2443/gd-v5
````

​		2.构建镜像（需要提前写好Dockerfile，且需要进入到Dockerfile所在目录下执行）

```
docker build -t [镜像名]:[镜像版本号] .

实例：
docker build -t rabbitmq:management .
```

​		3.标记镜像

````
docker tag [原镜像名] [私有仓库镜像名]

实例：
docker tag rabbitmq:management registry.comtom.cn:2443/gd-v5/rabbitmq:1.0
````

​		4.推送镜像

````
docker push [私有仓库镜像名]

实例：
docker push registry.comtom.cn:2443/gd-v5/rabbitmq:1.0
````

​		5.拉取镜像

````
docker pull [私有仓库镜像名]

实例：
docker pull registry.comtom.cn:2443/gd-v5/rabbitmq:1.0
````

