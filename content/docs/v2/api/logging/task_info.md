---
title: "/logging/task_info"
linkTitle: "/logging/task_info"
weight: 5
description: >
    获取采集任务详情
---

## 参数

1. `pid`: 项目ID

## 返回值

```json
{
  "code": 0,
    "payload": {
      "type": 任务类型,
      "exclude": 去除日志的正则,
      "batch": 批量上报条数,
      "buffer": 批量上报数据量,
      "interval": 批量上报间隔超时时间,
      "created": 创建时间戳,
      "nodes": [
        {
          "id": 节点ID,
          "status": running或stoped,
          "info": [
            { // k8s
              "ns": namespace,
              "name": 项目名,
              "dir": 文件路径,
              "pods": 上一次采集时的pod数量，定期上报
            },
            { // docker
            },
            { // file
              "dir": 文件路径
            }, ...
          ]
        }, ...
      ]
    }
  }
}
```

## 错误，任务不存在

```json
{
  "code": 404,
  "msg": 错误内容
}
```
