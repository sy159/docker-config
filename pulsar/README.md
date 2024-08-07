## pulsar单节点部署
这里基于单节点部署pulsar，用于测试使用
### 1. 目录结构
```
pulsar
├── README.md
└── docker-compose.yml
```
### 2. 根据情况修改pulsar配置
```shell
# 数据持久化授权
sudo mkdir -p ./data/zookeeper ./data/bookkeeper
sudo chown -R 10000 data
```

### 3. 部署pulsar
```shell
docker-compose up -d
```

### 4. 常用命令
```shell
# 进入admin可执行文件目录
docker-compose exec broker bash
# 可执行文件目录
cd bin/
# 创建租户
pulsar-admin tenants create snow --admin-roles adminRole
# 查看集群列表
pulsar-admin clusters list
# 查看租户对应的集群
pulsar-admin tenants list-allowed-clusters snow
# 更新租户的集群权限
pulsar-admin tenants update snow --allowed-clusters pulsar-cluster
# 创建命名空间
pulsar-admin namespaces create snow/test
# 创建一个持久化，租户为snow，命名空间为test的非分区的test-topic主题
cd bin/ && pulsar-admin topics create persistent://snow/test/test-topic
```
