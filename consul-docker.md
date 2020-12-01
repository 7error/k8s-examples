# 1 准备
## 1.1
|  机器    |  ip                |  角色             |
| ----    | ----               | ----              |
|   n33   |  192.168.122.33    | consul server     |
|   n34   |  192.168.122.34    | consul server     |
|   n35   |  192.168.122.35    | consul server     |
|   n38   |  192.168.122.38    | consul client     |


每台机器都安装好docker


## 1.2 生成个 encrypt

```sh
docker run -it --rm consul:1.8.3 keygen
```

# 2 server节点

## 2.1 配置
```sh
mkdir -p consul/{data,consul.d}
chown -R 100:1000 consul/data
```
```sh
cat >$(pwd)/consul/consul.d/config.json <<EOL
{
  "bootstrap_expect": 3,
  "server": true,
  "client_addr": "0.0.0.0",
  "datacenter": "dc1",
  "data_dir": "/data",
  "dns_config": {
    "enable_truncate": true,
    "only_passing": true
  },
  "encrypt": "DXv9e2TI0QNB2azasK6IA3prkuOMapnKs4rSWvVjmg4=",
  "leave_on_terminate": true,
  "rejoin_after_leave": true,
  "ui": true,
  "enable_debug": false,
  "retry_join": [
    "192.168.122.33",
    "192.168.122.34",
    "192.168.122.35"
  ],
  "retry_interval": "30s",
  "start_join": [
    "192.168.122.33",
    "192.168.122.34",
    "192.168.122.35"
  ],
  "disable_update_check": true
}
EOL

```


## 2.2 启动
```sh
docker run -d --restart always --name=consul --net=host \
-v $(pwd)/consul/data:/data \
-v $(pwd)/consul/consul.d:/etc/consul.d/ \
consul:1.8.3 consul agent -config-dir /etc/consul.d/ -bind=192.168.122.33 -node="n33"
```
```sh
docker run -d --restart always --name=consul --net=host \
-v $(pwd)/consul/data:/data \
-v $(pwd)/consul/consul.d:/etc/consul.d/ \
consul:1.8.3 consul agent -config-dir /etc/consul.d/ -bind=192.168.122.34 -node="n34"
```
```sh
docker run -d --restart always --name=consul --net=host \
-v $(pwd)/consul/data:/data \
-v $(pwd)/consul/consul.d:/etc/consul.d/ \
consul:1.8.3 consul agent -config-dir /etc/consul.d/ -bind=192.168.122.35 -node="n35"
```

# 3 client节点
## 3.1 配置
```sh
mkdir -p consul/{data,consul.d}
chown -R 100:1000 consul/data
```
```sh
cat >$(pwd)/consul/consul.d/config.json <<EOL
{
  "server": false,
  "client_addr": "0.0.0.0",
  "datacenter": "dc1",
  "data_dir": "/data",
  "dns_config": {
    "enable_truncate": true,
    "only_passing": true
  },
  "encrypt": "DXv9e2TI0QNB2azasK6IA3prkuOMapnKs4rSWvVjmg4=",
  "leave_on_terminate": true,
  "rejoin_after_leave": true,
  "ui": true,
  "enable_debug": false,
  "retry_join": [
    "192.168.122.33",
    "192.168.122.34",
    "192.168.122.35"
  ],
  "retry_interval": "30s",
  "start_join": [
    "192.168.122.33",
    "192.168.122.34",
    "192.168.122.35"
  ],
  "disable_update_check": true
}
EOL
```
## 3.2 启动
```sh
docker run -d --restart always --name=consul --net=host \
-v $(pwd)/consul/data:/data \
-v $(pwd)/consul/consul.d:/etc/consul.d/ \
consul:1.8.3 consul agent -config-dir /etc/consul.d/ -bind=192.168.122.38 -node="n38"
```

# 4 测试

```sh
docker exec -it consul sh -c "consul members"
docker exec -it consul sh -c "consul operator raft list-peers"
docker exec -it consul sh -c "consul catalog nodes"
docker exec -it consul sh -c "consul catalog services"
```

```sh
# register service
## 服务dns1，业务正常，带健康检测
curl -X PUT -d '{"id": "dns1","name": "dns","address": "8.8.4.4","port": 53,"tags": ["dev"],"checks": [{"tcp": "8.8.4.4:53","interval": "10s","timeout": "5s","deregisterCriticalServiceAfter": "360m"}]}' http://192.168.122.38:8500/v1/agent/service/register?replace-existing-checks=true

## 服务dns2，业务正常，带健康检测
curl -X PUT -d '{"id": "dns2","name": "dns","address": "8.8.8.8","port": 53,"tags": ["dev"],"checks": [{"tcp": "8.8.8.8:53","interval": "10s","timeout": "5s","deregisterCriticalServiceAfter": "360m"}]}' http://192.168.122.38:8500/v1/agent/service/register?replace-existing-checks=true

## 服务dns3，业务异常，带健康检测，1m后反注册
curl -X PUT -d '{"id": "dns3","name": "dns","address": "8.8.1.1","port": 53,"tags": ["dev"],"checks": [{"tcp": "8.8.1.1:53","interval": "10s","timeout": "5s","deregisterCriticalServiceAfter": "22m"}]}' http://192.168.122.38:8500/v1/agent/service/register?replace-existing-checks=true

## 服务dns4，业务异常，不带健康检测
curl -X PUT -d '{"id": "dns4","name": "dns","address": "8.8.2.2","port": 53,"tags": ["dev"]}' http://192.168.122.38:8500/v1/agent/service/register?replace-existing-checks=true


curl -X PUT -d '{"id": "dns5","name": "dns","address": "8.8.1.1","port": 53,"tags": ["dev"],"checks": [{"http": "192.168.1.1:80","interval": "10s","timeout": "5s","deregisterCriticalServiceAfter": "22m"}]}' http://192.168.122.38:8500/v1/agent/service/register?replace-existing-checks=true


# find service

curl -s -XGET http://192.168.122.33:8500/v1/catalog/service/dns | python -mjson.tool
curl -s -XGET http://192.168.122.33:8500/v1/catalog/service/dns --data-urlencode 'filter=ServiceID == dns1' |python -mjson.tool
curl -s -XGET http://192.168.122.33:8500/v1/health/service/dns?passing=true | python -mjson.tool


curl -s -XGET http://192.168.122.38:8500/v1/agent/service/dns1 | python -mjson.tool
curl -s -XGET http://192.168.122.38:8500/v1/agent/health/service/id/dns1 | python -mjson.tool


# deregister service
curl -X PUT http://192.168.122.38:8500/v1/agent/service/deregister/dns1
curl -X PUT http://192.168.122.38:8500/v1/agent/service/deregister/dns2
curl -X PUT http://192.168.122.38:8500/v1/agent/service/deregister/dns3
curl -X PUT http://192.168.122.38:8500/v1/agent/service/deregister/dns4
```
