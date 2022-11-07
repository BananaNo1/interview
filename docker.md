#### 虚拟机和容器的区别

- Docker容器是在操作系统层面上实现虚拟化，直接复用本地主机的操作系统，而传统虚拟机则是在硬件层面实现虚拟化。docker优势体现为启动速度快，占用体积小。

##### 不同之处

- 传统虚拟机技术是虚拟出一套硬件后，在其上运行一个完整的操作系统，在该系统上再运行所需应用进程
- 容器内的应用进程直接运行与宿主的内核，容器内没有自己的内核==也没有进行硬件虚拟==。因此容器要比传统虚拟机**更为轻便**。
- 每个容器之间相互隔离，每个容器有自己的文件系统，容器之间进行不会相互影响，能区分计算资源。

#### Docker的基本组成

##### 镜像

- 一个只读模板，可以用来创建Docker容器，一个镜像可以创建很多容器 

##### 容器

- 容器是用镜像创建的运行实例

##### 仓库

- 放一堆镜像的地方

#### 命令

##### 启动

- `systemctl start docker` (stop ,restart,status,enable)

##### 镜像命令

- docker images
- docker search --limit
- docker pull 
- docker system df 查看镜像/容器/数据卷所占空间
- docker rmi  

###### docker的虚悬镜像是什么？

  仓库名，标签都是<none>的镜像，俗称虚悬镜像

##### 容器命令

- docker run -it ubuntu  /bin/bash(bash)

- docker ps

- 退出容器

  - exit 直接退出容器
  - ctrl+p+q 退出交互 不退容器

- docker logs 查看日志

- docker inspect 查看内部内部细节

- docker exec -it   *** /bin/bash 进入容器

  - docker attach 

  ```
  1.attach直接进入容器启动命令的终端，不会启动新的进程，用exit退出会导致容器的停止。
  2.exec是在容器中打开新的终端，并且可以启动新的进程，用exit退出，不会导致容器的停止。
  ```

- docker export 容器 > **.tar 导出

- cat **.tar | docker import -镜像用户/镜像名:版本号

#### 本地镜像

##### 联合文件系统

- 镜像分层最大的一个好处就是共享资源，方便复制迁移，就是为了复用。
- **Docker镜像层都是只读的，容器层是可写的**

##### docker commit

- docker commit 提交容器副本使之成为一个新的镜像
- docker commit  -m ="提交的描述信息"  -a="作者" 容器ID 要创建的目标镜像名:标签名

##### 镜像推送到私服

1. `docker pull registry`

2. `docker run -d -p 5000:5000 -v /zzyyuse/myregistry/:/tmp/registry --privileged=true registry`

3. 查看私服仓库 `curl -XGET  http://192.168.5.128:5000/v2/_catalog` 主机ip

4. 将新镜像修改成符合私服规范的tag

    `docker tag 镜像:tag  Host:Port/Repository:Tag`

   `docker tag ubuntu-net:1.0 192.168.5.128:5000/ubuntu-net:1.0`

5. 修改配置文件使之支持http `"insecure-registries":["192.168.5.128:5000"]` 

   `vim /etc/docker/daemon.json`  添加上述语句 ==不生效可重启docker==

6. `docker push 192.168.5.128:5000/ubuntu-net:1.0`

7. 查看私服仓库 `curl -XGET  http://192.168.5.128:5000/v2/_catalog`

8. 删除本地镜像 测试私服仓库`docker pull 192.168.5.128:5000/ubuntu-net:1.0`

#### 容器数据卷

#####  解决问题 

- 如果出现cannot open directory:Permission denied

  在挂载目录多加一个 `--privileged=true`

##### 规则

​	1. `docker run -it --privileged=true -v /宿主机绝对路径目录:/容器内目录:rw 镜像名`  默认就是`rw`

​	 只能读取不能写 `ro`  宿主机可读可写，容器内部被限制只能读取不能写

##### 继承

 --volumes-from 父类   继承容器卷映射关系

#### 安装简介

##### 总体步骤

1. 搜索镜像  `docker hub`  `docker search tomcat`
2. 拉取镜像
3. 查看镜像
4. 启动镜像
5. 停止镜像
6. 移除镜像

##### 安装tomcat

1. `docker pull tomcat`
2. `docker run -d -p 8080:8080 --name=t1 tomcat`
3. 查看`localhost:8080`  404
   1. 进入容器 docker exec -it ID /bin/bash
   2. 删除`webapps` `mv webapps.dist webapps`
   3. 新版(10.0)webapps中没有文件 
   4. 可以换用 tomcat8 

##### 安装mysql

```shell
docker run -d -p 3306:3306 --privileged=true -v /mydata/mysql/log:/var/log/mysql -v /mydata/mysql/data:/var/lib/mysql -v /mydata/mysql/conf:/etc/mysql/conf.d -e MYSQL_ROOT_PASSWORD=123456 --name mysql mysql:5.7


conf文件夹创建my.cnf文件
写入
[client]
default_character_set=utf8


[mysqld]
collation_server=utf8_general_ci
character_set_server=utf8
```

##### 安装redis

```shell
docker run -p 6379:6379 --name redis --privileged=true -v /mydata/redis/conf/redis.conf:/etc/redis/redis.conf -v /mydata/redis/data:/data -d redis:6.0.8 redis-server /etc/redis/redis.conf

redis.conf
#注释 bind 127.0.0.1
#appendonly yes

```

##### 安装es

```shell
docker pull elasticsearch:7.4.2
docker pull kibana:7.4.2


mkdir -p /mydata/elasticsearch/config
mkdir -p /mydata/elasticsearch/data

echo "http.host: 0.0.0.0" >> /mydata/elasticsearch/config/elasticsearch.yml

docker run --name elasticsearch -p 9200:9200 -p 9300:9300 \
-e "discovery.type=single-node" \
-e ES_JAVA_OPTS="-Xms128m -Xmx512m" \
-v /mydata/elasticsearch/config/elasticsearch.yml:/usr/share/elasticsearch/config/elasticsearch.yml \
-v /mydata/elasticsearch/data:/usr/share/elasticsearch/data \
-v /mydata/elasticsearch/plugins:/usr/share/elasticsearch/plugins \
-d elasticsearch:7.4.2


docker run --name kibana -e ELASTICSEARCH_HOSTS=http://122.205.95.110:9200 -p 5601:5601 -d kibana:7.4.2
```



```
docker run -p 80:80 --name nginx \
-v /mydata/nginx/html:/usr/share/nginx/html \
-v /mydata/nginx/logs:/var/log/nginx \
-v /mydata/nginx/conf:/etc/nginx \
-d nginx:1.10


```



### 进阶

#### mysql主从复制

##### 新建主服务器容器实例3307

```shell
docker run -p 3307:3306 --name mysql-server --privileged=true -v /mydata/mysql-master/log:/var/log/mysql \
-v /mydata/mysql-master/data:/var/lib/mysql \
-v /mydata/mysql-master/conf:/etc/mysql \
-e MYSQL_ROOT_PASSWORD=123456 \
-d mysql:5.7
```

##### conf中新建my.cnf

```sql
[mysqld]
server_id=101
#自带数据库
binlog-ignore-db=mysql
log-bin=mall-mysql-bin
binlog_cache_size=1M
binlog_format=mixed
expire_logs_days=7
slave_skip_errors=1062
```

##### 重启master

##### 进入容器 

`docker exec -it mysql-server /bin/bash`

`mysql -uroot -p`

##### 创建数据同步用户

```sql
create user 'slave'@'%' identified by '123456';
grant replication slave,replication client on *.* to 'slave'@'%';
```

##### 新建从服务器 3308

```
docker run -p 3308:3306 --name mysql-slave --privileged=true -v /mydata/mysql-slave/log:/var/log/mysql \
-v /mydata/mysql-slave/data:/var/lib/mysql \
-v /mydata/mysql-slave/conf:/etc/mysql \
-e MYSQL_ROOT_PASSWORD=123456 \
-d mysql:5.7
```

##### conf中新建my.cnf

```sql
[mysqld]
server_id=102
binlog-ignore-db=mysql
log-bin=mall-mysql-slave1-bin
binlog_cache_size=1M
binlog_format=mixed
expire_logs_days=7
slave_skip_errors=1062
relay_log=mall-mysql-relay-bin
log_slave_updates=1
read_only=1
```

##### 重启slave

##### 在主数据库中查看同步状态

`show master status`

##### 进入从机

##### 在从数据库中配置主从复制

`change master to master_host='192.168.5.128', master_user='slave', master_password='123456', master_port=3307, master_log_file='mall-mysql-bin.000001', master_log_pos= 617, master_connect_retry=30;`

**注意同步文件名称**

#####  在从数据库中查看同步状态

`show slave status \G`     \G 可加可不加

#####  在从数据库中开启主从同步

  `start slave`

### 分布式存储

#### 解决方案

	1. 分布式哈希取余分区     问题:扩容缩容
	2. 一致性哈希算法分区   形成环   问题:数据倾斜
	3. 哈希槽分区  redis最多16384个

#### redis集群

##### 启动redis

```
docker run -d --name redis-node-1 --net host --privileged=true -v /mydata/redis/share/redis-node-1:/data redis --cluster-enabled yes --appendonly yes --port 6381

docker run -d --name redis-node-2 --net host --privileged=true -v /mydata/redis/share/redis-node-2:/data redis --cluster-enabled yes --appendonly yes --port 6382

docker run -d --name redis-node-3 --net host --privileged=true -v /mydata/redis/share/redis-node-3:/data redis --cluster-enabled yes --appendonly yes --port 6383

docker run -d --name redis-node-4 --net host --privileged=true -v /mydata/redis/share/redis-node-4:/data redis --cluster-enabled yes --appendonly yes --port 6384

docker run -d --name redis-node-5 --net host --privileged=true -v /mydata/redis/share/redis-node-5:/data redis --cluster-enabled yes --appendonly yes --port 6385

docker run -d --name redis-node-6 --net host --privileged=true -v /mydata/redis/share/redis-node-6:/data redis --cluster-enabled yes --appendonly yes --port 6386

## 扩容缩容
docker run -d --name redis-node-7 --net host --privileged=true -v /mydata/redis/share/redis-node7:/data redis --cluster-enabled yes --appendonly yes --port 6387

docker run -d --name redis-node-8 --net host --privileged=true -v /mydata/redis/share/redis-node-8:/data redis --cluster-enabled yes --appendonly yes --port 6388
```

##### 进入node1构建主从关系

`redis-cli --cluster create 192.168.5.128:6381 192.168.5.128:6382 192.168.5.128:6383 192.168.5.128:6384 192.168.5.128:6385 192.168.5.128:6386 --cluster-replicas 1`

`--cluster-replicas 1`  为每个master创建一个slave

##### 进入节点查看节点信息

1. cluster info 
2. cluster nodes

##### 连接集群环境

`redis-cli -p 6381 -c`

 不加 `-c`会出现集群读写error 

##### 查看集群信息

`redis-cli --cluster check 192.168.5.128:6381`

#### 扩容

1. 启动节点7，节点8 

2. 进入节点7 

3. 节点7加入集群  `redis-cli --cluster add-node IP:6387 IP:6381`  `redis-cli --cluster add-node 192.168.5.128:6387 192.168.5.128:6381`

4. 查看集群状态 `redis-cli --cluster check 192.168.5.128:6381`

5. 分配槽号 `redis-cli --cluster reshard 192.168.5.128:6381`

   6381分配前 0-5460  分配后 1365-5460

   6387分配前 0  分配后 [0-1364],[5461-6826],[10923-12287]

6. 查看集群信息

   前三家各自匀出一部分槽号

7. 给6387添加从机6388 `redis-cli --cluster add-node ip:新slave端口 ip:新master端口 --cluster-slave --cluster-master-id 新节点主机` 

   `redis-cli --cluster add-node 192.168.5.128:6388 192.168.5.128:6387 --cluster-slave --cluster-master-id 73e740eef903d8509a6f7841ede760ae09fdcee0`

8. 查看集群信息  6388已加入成功



#### 缩容(下线6387 6388)

​	6387是master 6388是slave  先下线从机 再下线主机

1. 检查集群信息 `redis-cli --cluster check 192.168.5.128:6382`
2. 删除6388 `redis-cli --cluster del-node ip:从机端口 从机节点ID`
3. 将6387的槽号清空，重新分配 (统一分配给6381) `redis-cli --cluster reshard 192.168.5.128:6381`
4. 查看集群信息 `redis-cli --cluster check 192.168.5.128:6382`
5. 删除6387

### dockerfile

#### 基础命令

  **大写**

1. FROM 指定一个镜像作为模板  **第一条必须是FROM**
2. MAINTAINER 镜像维护者的姓名和邮箱地址
3. RUN 容器构建时需要运行的命令
   1. shell格式  `RUN yum install vim -y`
   2. exec格式 `RUN["可执行文件","参数1","参数2"]`
   3. 在==docker build==时运行
4. EXPOSE 当前容器对外暴露的端口
5. WORKDIR 指定容器创建后，终端默认登录进来的工作目录，一个落脚点
6. USER 指定该镜像以什么样的用户去执行，如果都不指定，默认是root
7. ENV 用来在构建镜像过程中设置环境变量
8. ADD  将宿主机下的文件拷贝进镜像且会自动处理URL和解压tar压缩包
9. COPY 拷贝文件和目录到镜像中 ` COPY ["src" "dest"]`
10. VOLUME 容器数据卷，用于数据保存和持久化工作
11. CMD 指定容器启动后要干的事情
    1. 可以有多个CMD，但只有最后一个生效，CMD会被docker run之后的参数替换
    2. 在==docker run==时运行 **注意和RUN区别**
12. ENPRYPOINT 指定容器启动时要运行的命令
    1. ENPRYPOINT **不会**被docker run后面的命令覆盖，而且这些命令行参数会被当作参数送给ENPRYPOINT指令指定的程序

#### Dockerfile 编写 centos

```dockerfile

# 下载jdk
# wget https://mirrors.huaweicloud.com/java/jdk/8u171-b11/jdk-8u171-linux-x64.tar.gz
FROM centos:7
MAINTAINER sl<.com>

ENV MYPATH /usr/local
WORKDIR $MYPATH

#安装vim
RUN yum -y install vim
#安装ifconfig
RUN yum -y install net-tools
#安装JAVA8 以及lib库
RUN yum -y install glibc.i686
RUN mkdir /usr/local/java
ADD jdk-8u171-linux-x64.tar.gz    /usr/local/java/

#配置java环境变量
ENV JAVA_HOME /usr/local/java/jdk1.8.0_171
ENV JRE_HOME $JAVA_HOME/jre
ENV CLASS_PATH $JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar:$JAVA_HOME/lib:$CLASS_PATH
ENV PATH $JAVA_HOME/bin:$PATH

EXPOSE 80

CMD echo $MYPATH
CMD echo "success---------------ok"
CMD /bin/bash
```

`docker build -t centosjava8:1.5 .`   这个 `.`，实际上是在指定上下文的目录，`docker build` 命令会将该目录下的内容打包交给 Docker 引擎以帮助构建镜像。

#### 虚悬镜像

**respository tag全部为空的镜像**

`docker image ls -f dangling=true` 查看虚悬镜像

`docker image prune` 删除虚悬镜像

### Docker 网络

##### 自定义桥接镜像

​	可以通过名称访问实例 不用写死ip

### Docker compose

```yaml
version:"3"

services:
	microService:
		image:
		container_name:   # --name
		ports:
			-
		volumes:
			-
		networks:
			- sl_network
		depends_on:
			- redis
			- mysql
	
	redis:
		image: redis:6.0.8
		ports:
		 	- "6379:6379"
		volumes:
			- /app/redis/redis.conf:/etc/redis/redis.conf
			- /app/redis/data:/data
		networks:
			- sl_network
		command: redis-server /etc/redis/redis.conf
	
	mysql:
		image: mysql:5.7
		enviroment:
			MYSQL_ROOT_PASSWORD: '123456'
			MYSQL_ALLOW_EMPTY_PASSWORD: 'no'
			MYSQL_DATABASE: 'db'
			MYSQL_USER: 'zzyy'
			MYSQL_PASSWORD: 'zzyy123'
			ports:
				- "3306":3306
			volumes:
				- /app/mysql/db:/var/lib/mysql
				- /app/mysql/conf/my.cnf:/etc/my.cnf
				- /app/mysql/init:/docker-entrypoint-initdb.d
			networks:
				- sl_network
			command:	--default-authentication-plugin=mysql_native_password #解决外部无法访问
			
networks:
	sl_network:
```

1. docker-compose config -q

2. docker-compose up -d
