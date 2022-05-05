# /install/status

安装任务详情

## 参数

1. `task_id`: 任务ID

## 返回值

    {
        "code": 0,
        "payload": {
            "done": 是否完成,
            "actions": [
                {
                    "action": 任务类型,
                    "name": 名称,
                    "time": 时间戳,
                    "ok": 是否成功,
                    "msg": 失败信息
                }, ...
            ]
        }
    }

## 错误，任务不存在

    {
        "code": 404,
        "msg": 错误信息
    }

## 说明

1. 任务类型包含以下几种：
   - `download`: 下载安装包
   - `install`: 安装或解压安装包
   - `file`: 附加文件
   - `done`: 完成
2. `name`字段定义：
   - `download`: 安装包下载路径
   - `install`: 安装包下载路径
   - `file`: 附加文件保存路径
