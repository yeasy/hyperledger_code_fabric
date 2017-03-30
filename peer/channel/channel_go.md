### channel.go

`
peer channel` 相关命令的入口。

进一步支持 join、create、fetch、list 等子命令。

```go
// Cmd returns the cobra command for Node
func Cmd(cf *ChannelCmdFactory) *cobra.Command {

AddFlags(channelCmd)
channelCmd.AddCommand(joinCmd(cf))
channelCmd.AddCommand(createCmd(cf))
channelCmd.AddCommand(fetchCmd(cf))
channelCmd.AddCommand(listCmd(cf))

return channelCmd
}
```
