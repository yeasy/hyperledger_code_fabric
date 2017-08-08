#### main.go

orderer 启动后的 main 方法会调用到这里的 `Main()` 方法。

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

整体的调用流程如下图所示。

![orderer.common.server 包中的 Main() 方法](../../_images/orderer_common_server_Start.png)