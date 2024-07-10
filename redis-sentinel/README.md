## redis sentinel 6x版本部署
这里基于两台服务器node1(192.168.0.10) node2(192.168.0.11)实现redis一主两从+三哨兵模式实现redis高可用部署。node1节点部署一主一从两个哨兵，node2节点部署一从一哨兵。
### 1. 目录结构
```
redis-sentinel
├── README.md
├── docker-compose-slave.yml  # 一从一哨兵配置(node2节点)
├── docker-compose.yml  # 一主一从两哨兵配置(node1节点)
├── redis-master.conf  # 主redis配置
├── redis-slave.conf  # 从redis配置
└── sentinel.conf  # 哨兵配置
```

### 2. 根据需求修改配置文件
#### 修改配置
redis-master.conf
~~~
requirepass xxx # 修改访问密码
~~~

redis-slave.conf
~~~
requirepass xxx # 修改访问密码
slaveof 192.168.1.10 6379  # 修改主节点地址和端口
masterauth zx.123  # 设置为主节点的密码requirepass
~~~

sentinel.conf
~~~
# 监控主节点 mymaster，参数依次为：名称、IP、端口、最少需要几个Sentinel同意主节点就失效
sentinel monitor mymaster 192.168.1.10 6379 2

# 设置主节点失效判断时间（毫秒）
sentinel down-after-milliseconds mymaster 5000

# 设置故障转移超时时间（毫秒）
sentinel failover-timeout mymaster 10000

# 设置Sentinel的访问密码(前面设置的requirepass)
sentinel auth-pass mymaster yourpassword
~~~
### 3. 部署
#### 3.1 node1节点部署
```shell
docker-compose -f docker-compose.yml up -d
```

#### 3.2 node2节点部署
```shell
docker-compose -f docker-compose-slave.yml up -d
```

#### 3.2 校验是否成功
```shell
# -a 后面为你的密码
docker exec -it redis-master redis-cli -a zx.123 INFO replication
```
