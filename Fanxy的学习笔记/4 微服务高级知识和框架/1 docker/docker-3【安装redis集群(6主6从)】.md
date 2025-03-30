# docker-3【安装redis集群(6主6从)】

## 1. 配置开始

### 1.1. 新建6个docker容器redis实例

```sh
docker run -d --name redis-node-1 --net host --privileged=true -v /fanxyuse/redis-cluster/redis-node-1:/data redis:7 --cluster-enabled yes --appendonly yes --port 6381
 
docker run -d --name redis-node-2 --net host --privileged=true -v /fanxyuse/redis-cluster/redis-node-2:/data redis:7 --cluster-enabled yes --appendonly yes --port 6382

docker run -d --name redis-node-3 --net host --privileged=true -v /fanxyuse/redis-cluster/redis-node-3:/data redis:7 --cluster-enabled yes --appendonly yes --port 6383
 
docker run -d --name redis-node-4 --net host --privileged=true -v /fanxyuse/redis-cluster/redis-node-4:/data redis:7 --cluster-enabled yes --appendonly yes --port 6384
 
docker run -d --name redis-node-5 --net host --privileged=true -v /fanxyuse/redis-cluster/redis-node-5:/data redis:7 --cluster-enabled yes --appendonly yes --port 6385
 
docker run -d --name redis-node-6 --net host --privileged=true -v /fanxyuse/redis-cluster/redis-node-6:/data redis:7 --cluster-enabled yes --appendonly yes --port 6386
```

![20.png](./docker-3【安装redis集群(6主6从)】.assets/3-1.png)

### 1.2. 进入容器redis-node-1并为6台机器构建集群关系

**进入容器**

```sh
docker exec -it redis-node-1 /bin/bash
```

**构建主从关系**

`用自己的ip`

```sh
redis-cli --cluster create 10.0.20.8:6381 10.0.20.8:6382 10.0.20.8:6383 10.0.20.8:6384 10.0.20.8:6385 10.0.20.8:6386 --cluster-replicas 1
```

**--cluster-replicas 1 表示为每个master创建一个slave节点**

![21.png](./docker-3【安装redis集群(6主6从)】.assets/3-2.png)

**一切OK的话，3主3从搞定**

### 1.3. 链接进入6381作为切入点，查看集群状态

![3-3](./docker-3【安装redis集群(6主6从)】.assets/3-3.png)

![23.png](./docker-3【安装redis集群(6主6从)】.assets/3-4.png)

```sh
cluster info
```

```sh
cluster nodes
```

## 2. 主从容错切换迁移案例

```sh
cluster nodes
```

![24.png](./docker-3【安装redis集群(6主6从)】.assets/3-5.png)

此时关系如图

```sh
M              S
6381         6385
6382         6386
6383         6384
```

**主6381和从机切换，先停止主机6381**

```sh
docker stop redis-node-1
```

**6381主机停了，对应的真实从机上位，6381作为1号主机分配的从机以实际情况为准，具体是几号机器就是几号**

**再次查看集群信息**

![25.png](./docker-3【安装redis集群(6主6从)】.assets/3-6.png)

**此时关系如同**

```sh
M              S
6381[fali]   
6385
6382         6386
6383         6384
```

**然后测试读写仍然没问题，重新运行6381，发现主从调换**

```sh
M              S   
6385         6381
6382         6386
6383         6384
```

**redis我们学过可以通过命令重新调换主从**

```sh
cluster failover
```

**查看集群状态**

```sh
redis-cli --cluster check 自己IP:6381
```

![3-7](./docker-3【安装redis集群(6主6从)】.assets/3-7.png)

## 3. 主从扩容案例

**新建6387、6388两个节点+新建后启动+查看是否8节点**

```sh
docker run -d --name redis-node-7 --net host --privileged=true -v /fanxyuse/redis-cluster/redis-node-7:/data redis:7 --cluster-enabled yes --appendonly yes --port 6387
 
docker run -d --name redis-node-8 --net host --privileged=true -v /fanxyuse/redis-cluster/redis-node-8:/data redis:7 --cluster-enabled yes --appendonly yes --port 6388
```

**进入6387容器实例内部**

```sh
docker exec -it redis-node-7 /bin/bash
```

**将新增的6387节点(空槽号)作为master节点加入原集群**

**将新增的6387作为master节点加入集群**

**<font color='bb000'>`redis-cli --cluster add-node 自己实际IP地址:6387 自己实际IP地址:6381`</font>**

**6387 就是将要作为master新增节点**

**6381 就是原来集群节点里面的领路人，相当于6387拜拜6381的码头从而找到组织加入集群**

```sh
redis-cli --cluster add-node 10.0.20.8:6387 10.0.20.8:6381
```

**这里只需要把6387加入，测试我发现全加入的时候6388直接是空master节点，无法成为6387的slave，把它移除重新添加才行**

```sh
redis-cli --cluster del-node localhost:6388 5d149074b7e57b802287d1797a874ed7a1a284a8
```

**检查集群情况第1次**

```sh
redis-cli --cluster check 真实ip地址:6381
```

**重新分派槽号**
**命令:<font color='bb000'>redis-cli --cluster reshard IP地址:端口号</font>**

```sh
redis-cli --cluster reshard 10.0.20.8:6381
```

![3-8](./docker-3【安装redis集群(6主6从)】.assets/3-8.png)

**检查集群情况第2次**

```sh
redis-cli --cluster check 真实ip地址:6381
```

![3-9](./docker-3【安装redis集群(6主6从)】.assets/3-9.png)

![29.png](./docker-3【安装redis集群(6主6从)】.assets/3-10.png)

**为主节点6387分配从节点6388**

**<font color='bb000'>redis-cli --cluster add-node ip:新slave端口 ip:新master端口 --cluster-slave --cluster-master-id 新主机节点ID</font>**

```sh
redis-cli --cluster add-node 10.0.20.8:6388 10.0.20.8:6387 --cluster-slave --cluster-master-id 2c9e0a7009374bbf2a76849b346204306d4389e5
```

**检查集群情况第3次。完成四主四从**

```sh
redis-cli --cluster check 10.0.20.8:6383
```

![](./docker-3【安装redis集群(6主6从)】.assets/3-11.png)

## 4. 主从缩容案例

**目的：6387和6388下线**

**检查集群情况1获得6388的节点ID**

```sh
redis-cli --cluster check localhost:6382
```

**从集群中将4号从节点6388删除**

**命令：<font color="bb000">redis-cli --cluster del-node ip:从机端口 从机6388节点ID</font>**

```sh
redis-cli --cluster del-node localhost:6388 5d149074b7e57b802287d1797a874ed7a1a284a8
```

![3-12](./docker-3【安装redis集群(6主6从)】.assets/3-12.png)

**将6387的槽号清空，重新分配，本着严谨的态度，这里我选择一个个还给他们原本的槽位，就按6387偷了它们的槽位一个个还回去**

```sh
redis-cli --cluster reshard localhost:6381
```

![3-13](./docker-3【安装redis集群(6主6从)】.assets/3-13.png)

**检查集群情况第二次**

```sh
redis-cli --cluster check localhost:6381
```

**将6387删除**

**命令：<font color='bb000'>redis-cli --cluster del-node ip:端口 6387节点ID</font>**

```sh
redis-cli --cluster del-node localhost:6387 e4781f644d4a4e4d4b4d107157b9ba8144631451
```

**检查集群情况第三次**

```sh
redis-cli --cluster check localhost:6381
```
