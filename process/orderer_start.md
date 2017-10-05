## Orderer 节点启动

Orderer 节点启动通过 `orderer` 包下的 main() 方法实现，会进一步调用到 `orderer/common/server 包中的 Main() 方法`。

核心代码如下所示。

```go
// Main is the entry point of orderer process
func Main() {
	fullCmd := kingpin.MustParse(app.Parse(os.Args[1:]))

	// "version" command
	if fullCmd == version.FullCommand() {
		fmt.Println(metadata.GetVersionInfo())
		return
	}

	conf := config.Load()
	initializeLoggingLevel(conf)
	initializeLocalMsp(conf)

	Start(fullCmd, conf)
}
```


包括配置初始化和核心的启动过程：
* config.Load()：从本地配置文件和环境变量中读取配置信息，构建配置树结构。
* initializeLoggingLevel(conf)：配置日志级别。
* initializeLocalMsp(conf)：配置 MSP 结构。
* Start()：完成启动后的核心工作。

核心启动过程如下图所示。



![orderer.common.server 包中的 Main() 方法](_images/orderer_common_server_Start.png)



