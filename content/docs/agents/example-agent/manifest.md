---
title: "配置项描述"
linkTitle: "配置项描述"
weight: 3
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

## 示例

```yaml
- key: basic.id
  type: string
  name:
    - zh: AgentID
  desc:
    - zh: Agent的ID，在当前服务器下的集群内唯一
- key: basic.server
  type: naddr
  name:
    - zh: 服务端地址
  desc:
    - zh: 链接服务器端的地址
  default: 127.0.0.1:13081
- key: basic.log.target
  type: csv
  name:
    - zh: 日志保存目标
  desc:
    - zh: 日志保存目标，目前仅支持stdout和文件
  default: stdout
  csv_valid: [ stdout, file ]
- key: basic.log.dir
  type: path
  name:
    - zh: 日志保存路径
  desc:
    - zh: 日志保存路径
  default: ./logs
- key: basic.log.size
  type: bytes
  name:
    - zh: 日志文件滚动大小
  desc:
    - zh: 日志文件生成时每个文件的大小
  default: 10M
  min: 1M
  max: 100M
- key: basic.log.rotate
  type: uint
  name:
    - zh: 日志文件保留份数
  desc:
    - zh: 日志文件生成时最多保留份数
  default: 7
  min: 1
  max: 100
```