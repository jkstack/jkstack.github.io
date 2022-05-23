# /logging/config

下发日志采集配置

## 参数

1. `pid`: 项目ID
2. `type`: k8s、docker、logtail中的任意一个
3. `exclude`: 去除日志的正则，可选
4. `batch`: 批量上报条数，默认1000
5. `buffer`: 批量上报数据量，单位字节，默认4096
6. `interval`: 批量上报间隔超时时间，单位秒，默认30

### k8s参数

1. `ns`: 所需采集的namespace
2. `name`: 所需采集的项目名，csv
3. `dir`: 文件路径，支持通配符
3. `api`: k8s接口地址
4. `token`: k8s采集时所需的token信息

### docker参数

1. `ids`: 目标机器id，csv
2. `ct_name`: 容器名称
3. `ct_tag`: 容器tag，可选
4. `dir`: 文件路径，支持通配符

### logtail参数

1. `ids`: 目标机器id，csv
2. `dir`: 文件路径，支持通配符

## 返回值

```json
{
  "code": 0
}
```

## 错误：没有可用的采集节点

```json
{
  "code": 1,
  "msg": 错误内容
}
```

## 说明

1. 当`type`参数为`k8s`或`docker`且`dir`参数为空时表示采集该容器的stdout输出信息
