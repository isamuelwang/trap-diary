# 《踩坑日志 TapDiary》

## 2019.1

#### ==编号：2020.1.8.1==

#### 标题：正则表达式参考表

​	. 	- Any Character Except New Line
\d 	- Digit (0-9)
\D 	- Not a Digit (0-9)
\w 	- Word Character (a-z, A-Z, 0-9, _)
\W	 - Not a Word Character
\s	 - Whitespace (space, tab, newline)
\S	 - Not Whitespace (space, tab, newline)

\b	 - Word Boundary
\B	 - Not a Word Boundary
^	 - Beginning of a String
$	 - End of a String

【】	 - Matches Characters in brackets
【^ 】	 - Matches Characters NOT in brackets
| 	- Either Or
( )	 - Group

Quantifiers:
\*	 - 0 or More
\+ 	- 1 or More
? 	- 0 or One
{3}	 - Exact Number
{3,4}	 - Range of Numbers (Minimum, Maximum)#### Sample Regexs ####

【a-zA-Z0-9_.+-】+@【a-zA-Z0-9-】+\.【a-zA-Z0-9-.】+



#### ==编号：2020.1.12.1==

#### 标题：docker命令（持续积累）

1. **删除容器命令**

   删除docker中的容器可以使用如下命令：

   ```` 
   docker rm 容器id
   ````

   使用如下命令可以查看当前正在运行的容器

   ````
   docker ps
   ````

   对于已退出的容器，可以使用如下命令进行查看：

   ````
   docker ps -a
   ````

2. **创建docker中mysql容器**

   创建用于挂载的目录

   ````
   mkdir -p /usr/data/mysql/logs /usr/data/mysql/data /usr/data/mysql/conf
   ````

   创建容器

   ````
   docker run ‐di --name=mysql -p 3306:3306 -e MYSQL_ROOT_PASSWORD=123456 centos/mysql-57-centos7
   (不行可尝试去掉-di)
   ````

   进入mysql

   ````
   docker exec -it mysql bash
   ````

   找到docker容器的ID

   ````
   #查找正在运行
   docker ps
   #查找全部
   docker ps -a
   ````

   停止docker容器

   ````
   docker stop id
   ````

   启动docker容器

   ````shell
   docker start id
   ````
   
   查看日志
   
   ````
   docker logs -f -t dockerid
   ````

#### ==编号：2020.1.12.2==

#### 标题：Error:(1, 1) java: 非法字符: ‘\ufeff’

1. **问题**

   用IDEA打开eclipse java项目编译时，出现以下错误：

   Error:(1, 1) java: 非法字符: '\ufeff'
   Error:(1, 10) java: 需要class, interface或enum

2. **原因**

   Eclipse可以智能的把UTF-8+BOM文件转为普通的UTF-8文件，IDEA没有这个智能转换

3. **解决方法**

    用IDEA转换，先转换为GBK，再转回UTF-8

   ![img](https://img2018.cnblogs.com/blog/1456626/201811/1456626-20181122113722374-815814386.png)

   ![img](https://img2018.cnblogs.com/blog/1456626/201811/1456626-20181122113816342-1399366033.png)

#### ==编号：2020.1.12.3==

#### 标题：SpringBoot RedisTemplate乱码解决方案

1. **问题**

   使用SpringBoot RedisTemplate保存数据出现乱码

   ````
   	127.0.0.1:6379> keys *  
   1) "\xac\xed\x00\x05t\x00\x04pass"  
   2) "\xac\xed\x00\x05t\x00\x04name"  
   3) "name"  
   ````

2. **解决方法**

   ````java
   @Autowired
   private RedisTemplate redisTemplate;
   ````

   改成

   ````java
   @Autowired
   private StringRedisTemplate redisTemplate;
   ````

3. **RedisTemplate和StringRedisTemplate区别**

   （1）两者的关系是StringRedisTemplate继承RedisTemplate。

   （2）两者的数据是不共通的；也就是说StringRedisTemplate只能管理StringRedisTemplate里面的数据，RedisTemplate只能管理RedisTemplate中的数据。

   （3）SDR默认采用的序列化策略有两种，一种是String的序列化策略，一种是JDK的序列化策略。

   StringRedisTemplate默认采用的是String的序列化策略，保存的key和value都是采用此策略序列化保存的。

   RedisTemplate默认采用的是JDK的序列化策略，保存的key和value都是采用此策略序列化保存的。

   两者总结：

   当你的redis数据库里面本来存的是字符串数据或者你要存取的数据就是字符串类型数据的时候，那么你就使用StringRedisTemplate即可，但是如果你的数据是复杂的对象类型，而取出的时候又不想做任何的数据转换，直接从Redis里面取出一个对象，那么使用RedisTemplate是更好的选择。
   
   docker run --name redis -p 6379:6379 -d --restart=always redis:latest redis-server --appendonly yes --requirepass "redis123456aB"

#### ==编号：2020.1.19.1==

#### 标题：docker安装Redis

1. docker安装

   ````
   # 安装
   curl -fsSL https://get.docker.com | bash -s docker --mirror Aliyun
   # 启动
   sudo service docker start
   ````

2. 查询Redis镜像

````
docker search redis
````

3. 下载Redis镜像(这里用的是最新版redis)

````
docker pull redis
````

4. 创建目录

````
mkdir -p /root/docker/redis/data
mkdir -p /root/docker/redis/conf
````

5. 启动Redis镜像（顺便设置密码）

````
# 挂载文件方式
docker run -d --privileged=true -p 6379:6379 --restart always -v /root/docker/redis/conf/redis.conf:/etc/redis/redis.conf -v /root/docker/redis/data:/data --name Redis redis redis-server /etc/redis/redis.conf --appendonly yes --requirepass "这里填写密码"
# 不挂载文件方式
docker run --name redis -p 6379:6379 -d --restart=always redis:latest redis-server --appendonly yes  --requirepass "这里填写密码"
````

6. 参数说明

   ````
   -d                                                  -> 以守护进程的方式启动容器
   -p 6379:6379                                        -> 绑定宿主机端口
   --name myredis                                      -> 指定容器名称
   --restart always                                    -> 开机启动
   --privileged=true                                   -> 提升容器内权限
   -v /root/docker/redis/conf:/etc/redis/redis.conf    -> 映射配置文件
   -v /root/docker/redis/data:/data                    -> 映射数据目录
   --appendonly yes                                    -> 开启数据持久化
   ````

#### ==编号：2020.1.19.2==

#### **标题：docker安装Redisrabbitmq**

1. 安装镜像（使用management版本）

````
docker pull rabbitmq:management
````

2. 创建外挂文件夹

````
mkdir -p /root/docker/rabbitmq/etc
mkdir -p /root/docker/rabbitmq/lib
mkdir -p /root/docker/rabbitmq/log
````

2. 启动(顺便设置帐号密码)

`````
docker run -d -p 15672:15672 -p 5672:5672 --restart always --name rabbitmq --privileged=true -v /root/docker/rabbitmq/data:/var/lib/rabbitmq -v /root/docker/rabbitmq/log:/var/log/rabbitmq  -v /root/docker/rabbitmq/etc:/var/etc/rabbitmq -e RABBITMQ_DEFAULT_USER=admin -e RABBITMQ_DEFAULT_PASS=123456 rabbitmq:management
`````

#### ==编号：2020.1.19.3==

#### **标题：docker安装MongoDB**

1. 配置安装

````
docker run -itd --name mongo --restart=on-failure:10 -p 27017:27017 -v /home/db  mongo --auth
# 挂载文件方式
docker run -d -p 27017:27017 --restart always -v /root/docker/mongo/config:/data/configdb -v /root/docker/mongo/data:/data/db --name mongoDB mongo --auth
````

- restart=on-failure:10:为mongo添加重启策略，在非零状态下退出，重试次数设置为10次
- p 27017:27017:映射容器服务的 27017 端口到宿主机的 27017 端口。外部可以直接通过 宿主机 ip:27017 访问到 mongo 的服务。
- auth:需要密码才能访问容器服务。
- v:映射文件到本地文件夹,这样数据库文件就会在本地存储,即使删除了容器或镜像至少数据库文件还在,数据不会丢失。
- d:表示守护态运行

2. 配置权限

````
# 进入MongoDB
$ docker exec -it mongo mongo admin
# 创建一个名为 admin，密码为 123456 的用户。
db.createUser({ user:'admin',pwd:'123456',roles:[ { role:'userAdminAnyDatabase', db: 'admin'}]});
# 尝试使用上面创建的用户信息进行连接。
db.auth('admin', '123456')
# 创建超级用户
db.createUser({ user: "root" , pwd: "root", roles: ["root"]})
````

3. 基于角色的权限说明

内置角色

- 数据库用户角色

read: 只读数据权限
readWrite:学些数据权限

- 数据库管理角色

dbAdmin: 在当前db中执行管理操作的权限
dbOwner: 在当前db中执行任意操作
userADmin: 在当前db中管理user的权限

- 备份和还原角色

backup
restore

- 跨库角色

readAnyDatabase: 在所有数据库上都有读取数据的权限
readWriteAnyDatabase: 在所有数据库上都有读写数据的权限
userAdminAnyDatabase: 在所有数据库上都有管理user的权限
dbAdminAnyDatabase: 管理所有数据库的权限

- 集群管理

clusterAdmin: 管理机器的最高权限
clusterManager: 管理和监控集群的权限
clusterMonitor: 监控集群的权限
hostManager: 管理Server

- 超级权限

root: 超级用户

#### ==编号：2020.1.19.4==

#### **标题：docker配置JDK1.8镜像**

（以下步骤可使用DockerMaven代替，简单快捷）

1. 创建目录

   ````
   mkdir -p /usr/local/dockerjdk8
   ````

2. 安装jdk1.8压缩包

   下载jdk-8u171-linux-x64.tar.gz并上传到服务器（虚拟机）中
   的/usr/local/dockerjdk8目录

3. 创建文件Dockerfile

   ````
   vi Dockerfile
   ````

   内容如下

   ````
   #依赖镜像名称和ID
   FROM centos:7
   #指定镜像创建者信息
   MAINTAINER ITCAST
   #切换工作目录
   WORKDIR /usr
   RUN mkdir /usr/local/java
   #ADD 是相对路径jar,把java添加到容器中
   ADD jdk‐8u171‐linux‐x64.tar.gz /usr/local/java/
   #配置java环境变量
   ENV JAVA_HOME /usr/local/java/jdk1.8.0_171
   ENV JRE_HOME $JAVA_HOME/jre
   ENV CLASSPATH
   $JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar:$JRE_HOME/lib:$CLASSPATH
   ENV PATH $JAVA_HOME/bin:$PATH
   ````

4. 执行命令构建镜像

   ````
   docker build -t='jdk1.8' .
   # 注意后边的空格和点，不要省略
   ````

5. 创建容器

   ````
   docker run ‐it --name=myjdk8 jdk1.8 /bin/bash
   ````

#### ==编号：2020.1.19.4==

#### **标题：docker私有库**

1. 拉取私有仓库镜像

   ````
   docker pull registry
   ````

2. 启动私有仓库容器

   ````
   docker run ‐di ‐‐name=registry ‐p 5000:5000 registry
   ````

3. 仓库访问地址

   ````
   http://192.168.184.135:5000/v2/_catalog
   ````

4. 修改daemon.json(增加本地仓库镜像地址)

   ````
   vi /etc/docker/daemon.json
   ````

   添加如下内容，让 docker信任私有仓库地址

   ````
   {"insecure‐registries":["122.51.105.58:5000"]}
   ````

5. 让 docker信任私有仓库地址

   ````
   systemctl restart docker
   ````

6. 上传镜像到私有库（以下步骤可使用DockerMaven代替，简单快捷）

   标记此镜像为私有仓库的镜像

   ````
   docker tag jdk1.8 192.168.184.135:5000/jdk1.8
   ````

   再次启动私服容器

   ````
   docker start registry
   ````

   上传标记的镜像

   ````
   docker push 192.168.184.135:5000/jdk1.8
   ````

   echo '{ "insecure-registries":["122.51.105.58:5000"] }' > /etc/docker/daemon.json

   推送镜像到私有仓库

   ````
   docker push 192.168.184.135:5000/jdk1.8
   ````

   可能会出现无法访问http，只能访问https的错误

   解决：

   ````
   echo '{ "insecure-registries":["xxx.xxx.xxx.xxx:5000"] }' > /etc/docker/daemon.json
    
   systemctl restart docker
   ````

   #### ==编号：2020.1.20.1==

   #### 标题：项目安装成docker镜像并部署

   1. **pom文件写入插件**

      插入以下内容

      ````
          <build>
              <finalName>app</finalName>
              <plugins>
                  <plugin>
                      <groupId>org.springframework.boot</groupId>
                      <artifactId>spring-boot-maven-plugin</artifactId>
                  </plugin>
                  <!-- docker的maven插件，官网：
                  https://github.com/spotify/docker‐maven‐plugin -->
                  <plugin>
                      <groupId>com.spotify</groupId>
                      <artifactId>docker-maven-plugin</artifactId>
                      <version>0.4.13</version>
                      <configuration>
                          <imageName>122.51.105.58:5000/${project.artifactId}:${project.version}</imageName>
                          <baseImage>jdk1.8</baseImage>
                          <entryPoint>["java", "-jar", "/${project.build.finalName}.jar"]</entryPoint>
                          <resources>
                              <resource>
                                  <targetPath>/</targetPath>
                                  <directory>${project.build.directory}</directory>
                                  <include>${project.build.finalName}.jar</include>
                              </resource>
                          </resources>
                          <dockerHost>http://122.51.105.58:2375</dockerHost>
                      </configuration>
                  </plugin>
              </plugins>
          </build>
      ````

   2. **输入命令开始输出镜像**

      ````
      mvn clean package docker:build -DpushImage
      ````

   3. **服务器中启动镜像**

      ````
      docker run ‐d ‐‐name=eureka ‐p 6868:6868 192.168.184.135:5000/tensquare_eureka:1.0‐SNAPSHOT
      ````

#### ==编号：2020.1.20.1==

#### 标题：docker相关命令

1. **启动**

   ````
   systemctl start docker
   ````

2. **停止**

   ````
   systemctl stop docker
   ````

#### ==编号：2020.1.20.2==

#### 标题：docker MYsql registry安装

1. **安装命令**

   ````
   docker run -d -p 3306:3306 --restart always --privileged=true --name mysql -e MYSQL_ROOT_PASSWORD=123456 -v=/root/docker/mysql/config/my.cnf:/etc/mysql/conf.d/mysql.cnf -v=/root/docker/mysql/data:/var/lib/mysql centos/mysql-57-centos7
   ````

2. registry安装

   ````
   docker run -d -p 5000:5000 --restart=always --name registry -v /root/docker/registry:/var/lib/registry registry
   ````

## 2019.1.1

#### ==编号：2020.1.x==

#### 标题：xxxxx

1. **问题**

   

2. **原因**

   

3. **解决方法**

   

4. **总结**







 
