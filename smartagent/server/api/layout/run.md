# /layout/run

执行编排文件

## 参数

1. `ids`: id列表，csv格式
2. `mode`: 执行顺序，可选，默认sequence
3. `continue`: 出错时是否继续，可选，默认为false
4. `file`: 资源文件tar包
5. `user`: 账号，可选
6. `pass`: 密码，可选

## 返回值

    {
        "code": 0,
        "payload": 任务ID
    }

## 错误，检测未通过

    {
        "code": 1,
        "msg": 错误内容
    }

## 错误，指定agentid未找到

    {
        "code": 404,
        "msg": 错误内容
    }

## 说明

1. `mode`参数支持以下执行方式:

       sequence: 顺序执行，按给定的id列表顺序一个一个agent执行
       parallel: 并发执行，按给定的id列表同时执行，此时continue参数无效
       evenodd: 奇偶模式执行，按给定的id列表顺序分为两组，分别执行

2. 上传的tar包中必须包含main.yaml或main.json文件作为执行时的编排文件
3. tar包中不允许包含软链文件，在解压时软链文件将被忽略