## docker-compose 部署Mongodb副本集6.0

1、查看mongodb镜像：

```shell
docker search mongo
```

2、拉取mongodb镜像：本次文档中拉取mongodb6.0.6

```shell
docker pull mongo:6.0.6
```

3、创建目录，分别保存3个mongodb实例数据、备份、秘钥：

```shell
mkdir -p /data/mongo-rs/{data01,data02,data03,key}
```

4、切换至创建的目录下生成key

```shell
cd /data/mongo-rs/;
 
openssl rand -base64 756 > key/mongo-rs.key;

# 文件的权限、属主、属组修改很重要,必须要设置,不设置将导致集群启动失败
chmod 400 key/mongo-rs.key;
chown polkitd:input key/mongo-rs.key;
```

5、编辑mongodb配置文件

```yml
# vim /mongo/docker/prev/mongodb01/mongod.conf
storage:
  dbPath: /data/db
 
systemLog:
  destination: file
  logAppend: true
  path: /var/log/mongodb/mongod.log
 
net:
  port: 27017
  bindIp: 0.0.0.0
 
processManagement:
  timeZoneInfo: /usr/share/zoneinfo
# 将配置文件复制到另外两个节点的配置目录下

# cp /mongo/docker/prev/mongodb01/mongod.conf /mongo/docker/prev/mongodb02/mongod.conf

# cp /mongo/docker/prev/mongodb01/mongod.conf /mongo/docker/prev/mongodb03/mongod.conf
```

6、编辑docker-compose.yml 文件

##### 提前创建docker network

```yml
# docker network create -d bridge --subnet=172.18.0.0/16 --gateway=172.18.0.1 fb-net
# vim /data/docker-compose.yml
version: '3'
services:
  mongodb01:
    image: mongo:6.0.6
    container_name: mongodb01
    ports:
      - 27017:27017
    restart: always
    environment:
      - TZ=Asia/Shanghai
      - MONGO_INITDB_ROOT_USERNAME=admin
      - MONGO_INITDB_ROOT_PASSWORD=admin123
    volumes:
      - /data/mongo-rs/data01:/data/db
      - /data/mongo-rs/key:/data/key
      - /mongo/docker/prev/mongodb01/mongod.conf:/etc/mongod.conf
    command: mongod -f /etc/mongod.conf --replSet mongo-rs --auth --keyFile /data/key/mongo-rs.key
    networks:
      fb-net:
        aliases:
          - mongodb01
 
  mongodb02:
    image: mongo:6.0.6
    container_name: mongodb02
    ports:
      - 27018:27017
    restart: always
    environment:
      - TZ=Asia/Shanghai
      - MONGO_INITDB_ROOT_USERNAME=admin
      - MONGO_INITDB_ROOT_PASSWORD=admin123
    volumes:
      - /data/mongo-rs/data02:/data/db
      - /data/mongo-rs/key:/data/key
      - /mongo/docker/prev/mongodb02/mongod.conf:/etc/mongod.conf
    command: mongod -f /etc/mongod.conf --replSet mongo-rs --auth --keyFile /data/key/mongo-rs.key
    networks:
      fb-net:
        aliases:
          - mongodb02
 
  mongodb03:
    image: mongo:6.0.6
    container_name: mongodb03
    ports:
      - 27019:27017
    restart: always
    environment:
      - TZ=Asia/Shanghai
      - MONGO_INITDB_ROOT_USERNAME=admin
      - MONGO_INITDB_ROOT_PASSWORD=admin123
    volumes:
      - /data/mongo-rs/data03:/data/db
      - /data/mongo-rs/key:/data/key
      - /mongo/docker/prev/mongodb03/mongod.conf:/etc/mongod.conf
    command: mongod -f /etc/mongod.conf --replSet mongo-rs --auth --keyFile /data/key/mongo-rs.key
    networks:
      fb-net:
        aliases:
          - mongodb03
 
networks:
  fb-net:
    external: true
```

7、进入任意节点，配置mongodb副本集（最好是主节点27017端口）

```js
mongdb客户端命令：6.0为mongosh，6.0以下为mongo
arbiterOnly:true 表示该节点为仲裁节点，只负责投票，不负责存储数据
执行rs.initiate()方法，初始化副本集，同时执行该方法的节点为主节点
docker exec -it mongodb01 bash
 
mongosh
 
use admin
 
db.auth("admin","admin123")
 
var config={
     "_id":"mongo-rs",
     "members":[
         {"_id":0,host:"172.22.217.111:27017"}, // 这里的IP是宿主机的IP,也可将IP换成域名
         {"_id":1,host:"172.22.217.111:27018"},
         {"_id":2,host:"172.22.217.111:27019",arbiterOnly:true}
]};
 
rs.initiate(config)
```

8、查看副本集节点状态

```shell
rs.status();
```

9、验证数据同步，在主节点插入数据：

```shell
use test;
 
db.test.insert({name:"mongodb rs test"});
```

10、在从节点查看数据

```shell
docker exec -it mongodb02 bash;
mongosh;
use admin;
db.auth("admin","admin123");
db.getMongo().setReadPref('secondary');
use test;
db.test.find();
```