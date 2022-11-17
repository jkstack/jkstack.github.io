---
title: "配置项描述"
linkTitle: "配置项描述"
weight: 3
description: >
    Agent配置文件的描述文件结构定义。
---

每一个Agent项目中的conf目录下必须包含一个`manifest.yaml`文件用于描述该Agent的配置项信息，该文件被用于外部工具做自动化部署时的展示。

该文件的内容最顶层为一个数组，其中每一个元素代表配置文件中的某一个字段，该字段的描述信息如下：

- `key`: 该字段在配置文件中的名称
- `name.zh`: 该字段的中文名称，用于自动化部署工具的展示
- `desc.zh`: 该字段的中文描述
- `type`: 该字段类型，目前已支持以下类型
   - `string`: 字符串
   - `csv`: 逗号分隔的字符串
   - `int`: 有符号整数
   - `uint`: 无符号整数
   - `float`: 浮点数
   - `bool`: 布尔值
   - `naddr`: 网络地址，\<ip\>:\<port\>
   - `path`: 文件路径
   - `bytes`: 可视化字节数，(n)G、(n)M、(n)KB、(n)B等
   - `duration`: 时长，(n)h、(n)m、(n)s等
- `default`: (可选)默认值
- `csv_valid`: (可选)csv字段允许输入的内容
- `str_valid`: (可选)string类型的匹配正则表达式
- `min`: (可选)最小值，支持的数据类型`int`、`uint`、`float`、`bytes`、`duration`
- `max`: (可选)最大值，支持的数据类型`int`、`uint`、`float`、`bytes`、`duration`
- `len`: (可选)最大长度，支持的数据类型`string`、`csv`、`path`
- `allow_relative`: (可选)是否允许使用相对路径，支持的数据类型`path`
- `category`: (可选)配置项分类

## 示例

```yaml
- key: basic.id
  type: string
  name:
    zh: AgentID
  desc:
    zh: Agent的ID，在当前服务器下的集群内唯一
- key: basic.server
  type: naddr
  name:
    zh: 服务端地址
  desc:
    zh: 链接服务器端的地址
  default: 127.0.0.1:13081
- key: basic.log.target
  type: csv
  name:
    zh: 日志保存目标
  desc:
    zh: 日志保存目标，目前仅支持stdout和文件
  default: stdout
  csv_valid: [ stdout, file ]
- key: basic.log.dir
  type: path
  name:
    zh: 日志保存路径
  desc:
    zh: 日志保存路径
  default: ./logs
- key: basic.log.size
  type: bytes
  name:
    zh: 日志文件滚动大小
  desc:
    zh: 日志文件生成时每个文件的大小
  default: 10M
  min: 1M
  max: 100M
- key: basic.log.rotate
  type: uint
  name:
    zh: 日志文件保留份数
  desc:
    zh: 日志文件生成时最多保留份数
  default: 7
  min: 1
  max: 100
```

## 关联关系

### 示例1

```python
log.target = stdout,file
log.dir = ./logs
log.size = 10M
log.rotate = 7
```

当`log.target`字段未设置`file`属性时，`log.dir`、`log.size`、`log.rotate`配置项是无效的。

### 示例2

```python
monitor.enabled = true
monitor.interval = 10s
```

当`monitor.enabled`字段设置为false时，`monitor.interval`配置项是无效的。

基于以上两种情况，增加配置字段如下：

```yaml
- key: basic.log.dir
  ...
  enabled:
    when:
      target: basic.log.target
      contain: file
- key: basic.monitor.interval
  ...
  enabled:
    when:
      target: basic.monitor.enabled
      equal: true
```

1. `enabled`字段用于表示当前字段的有效范围
2. `when`字段表示当条件中的描述符合要求时该字段有效
3. `target`: 该字段表示需要关联到的原始字段名称
4. `contain`: 针对于原始字段为`csv`类型时csv内容中包含该配置项时当前字段才有效
5. `equal`: 针对于原始字段为`bool`或`数值`类型时原始字段的值与要求内容相符时才有效