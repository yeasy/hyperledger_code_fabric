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

#### InitCmdFactory

所有 channel 子命令都会先调用 InitCmdFactory 来进行必要的初始化，根据命令需求来生成ChannelCmdFactory。

```go
type ChannelCmdFactory struct {
    EndorserClient   pb.EndorserClient
    Signer           msp.SigningIdentity
    BroadcastClient  common.BroadcastClient
    DeliverClient    deliverClientIntf
    BroadcastFactory BroadcastClientFactory
}
```

调用GetDefaultSignerFnc创建signer，赋值BroadcastFactory（BroadcastClient可以由此factory获得），调用GetEndorserClientFnc创建与endorser通信EndorserClient，join和list子命令需要；调用newDeliverClient获得与orderer通信DeliverClient，create和fetch。子命令需要。

