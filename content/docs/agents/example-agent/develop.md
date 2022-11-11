---
title: "Agent开发"
linkTitle: "Agent开发"
weight: 2
---

## agent开发

1. 创建项目并导入[libagent](github.com/jkstack/libagent)库

    ```
    go get github.com/jkstack/libagent
    ```
2. 定义一个结构体并实现[App](https://pkg.go.dev/github.com/jkstack/libagent#App)接口
3. 在main函数中添加RegisterService、UnregisterService和Run等libagent库中的接口调用

    ```go
    switch *act {
    case "install":
        agent.RegisterService(app)
    case "uninstall":
        agent.UnregisterService(app)
    default:
        agent.Run(app)
    }
    ```

## 研发与打包规范

1. 项目根目录中必须包含一个`CHANGELOG.md`文件，在打包过程中会自动读取该文件中的内容并自动填写到Release的描述信息中，格式可参考[样例](https://raw.githubusercontent.com/jkstack/example-agent/master/CHANGELOG.md)
2. 项目根目录中必须包含一个`Makefile`文件，在打包过程中会使用该文件中的第一个目标进行打包，打包成功后的产物必须存放到release目录下
3. 开发的代码在原则上需要支持谷歌维护的最新的两个go语言版本，因此在go.mod文件中目前会被设置为1.18版本
4. 在项目初始化的过程中需要修改contrib目录下的文件，如修改正确的打包路径等
5. 为方便于外部工具化部署，需要正确修改`manifest.yaml`文件，使其符合配置文件中的配置项[描述](../manifest)

## 打包方法

通过在项目中引入[workflows](https://github.com/jkstack/example-agent/tree/master/.github/workflows)目录下的build.yml和release.yml可实现项目的自动化打包。

1. build.yml中定义了当master分支发生合并时会进行代码的lint检查及go build测试
2. release.yml中定义了当该项目被打上了`v*.*.*`的标签后将会触发release动作，首先会进行各种系统下的跨平台编译与打包，然后读取`CHANGELOG.md`中对应版本的描述信息进行创建release操作，并将release目录下的文件进行上传

## 更多示例

1. [exec-agent](https://github.com/jkstack/exec-agent): 远程执行脚本以及文件传输
2. [metrics-agent](https://github.com/jkstack/metrics-agent): 监控数据采集