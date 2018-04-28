### channel.go

`peer channel` 相关命令的入口。

进一步支持create\|fetch\|join\|list\|update\|signconfigtx\|getinfo.等子命令。

```go
// Cmd returns the cobra command for Node
func Cmd(cf *ChannelCmdFactory) *cobra.Command {

	AddFlags(channelCmd)
	channelCmd.AddCommand(createCmd(cf))
	channelCmd.AddCommand(fetchCmd(cf))
	channelCmd.AddCommand(joinCmd(cf))
	channelCmd.AddCommand(listCmd(cf))
	channelCmd.AddCommand(updateCmd(cf))
	channelCmd.AddCommand(signconfigtxCmd(cf))
	channelCmd.AddCommand(getinfoCmd(cf))

	return channelCmd
}
```

所有 channel 子命令都会先调用 InitCmdFactory 来进行必要的初始化，根据命令需求来生成 endorserClient、BroadcastClient 和 DeliverClient。

