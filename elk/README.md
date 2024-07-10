## elk单节点部署安装
这里使用单节点es8.14.1部署，基于filebeat+es+kibana完成日志收集存储到kibana展示统计
### 1. 目录结构
```
elk
├── docker-compose-filebeat.yml  # filebeat
├── docker-compose.yml  # es+kibana
├── elasticsearch
│   └── elasticsearch.yml  es配置
├── README.md
└── kibana
    └── kibana.yml  # kibana配置
```

### 2. 部署es跟kibana
#### 2.1. 设置es跟kibana对外暴露端口以及使用密码(非必须)
```shell
vim .env
```
#### 2.2 根据需求修改es以及kibana配置(非必须)
```shell
vim elasticsearch/elasticsearch.yml  # es配置
vim kibana/kibana.yml  # kibana配置
```
#### 2.3 设置配置文件权限
```shell
chmod 777 -R elasticsearch  # 给es授权
chmod 777 -R kibana  # 给kibana授权
```
#### 2.4 启动es跟kibana服务
```shell
docker-compose up -d
```
#### 2.5 给kibana设置链接es的用户以及密码
- 通过es的api修改密码(推荐)
  - 使用系统默认默认用户(推荐，默认kibana_system)
    ```shell
    docker-compose exec elasticsearch bash
    curl -u "elastic:你配置的ELASTIC_PASSWORD" \
     -X POST "http://127.0.0.1:9200/_security/user/kibana_system/_password" \
     -H "Content-Type: application/json" \
     -d '{"password": "zx.123"}'
    ```
  - 使用不存在的用户
    ```shell
    docker-compose exec elasticsearch bash
    curl -u "elastic:你配置的ELASTIC_PASSWORD" \
     -X POST "http://127.0.0.1:9200/_security/user/你配置的KIBANA_USER" \
     -H "Content-Type: application/json" \
     -d '{"password": "你配置的KIBANA_PASSWORD", "roles": ["kibana_system"]}'
    ```
- 进入容器执行命令修改密码
  ```shell
  docker-compose exec elasticsearch bash  # 进入es容器，根据情况调整
  cd bin/
  elasticsearch-setup-passwords interactive  # 修改密码
  ```
#### 2.6 重启kibana使
```shell
docker-compose restart kibana
```

### 3. 部署配置filebeat
#### 3.1 根据需求修改docker-compose-filebeat.yml里面的配置(需要收集日志地址)
```shell
vim docker-compose-filebeat.yml
```
#### 3.2 根据需求修改filebeat配置(日志，es等)
```shell
vim filebeat/filebeat.yml
```
#### 3.3 给配置文件增加权限
```shell
chmod go-w filebeat/filebeat.yml
```
#### 3.4 启动filebeat
```shell
docker-compose -f docker-compose-filebeat.yml up -d
```

