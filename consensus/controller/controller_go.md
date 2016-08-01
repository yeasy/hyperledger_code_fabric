### controller.go

主要包括根据配置中信息，创建一个新的 Consenter。

```go
func NewConsenter(stack consensus.Stack) consensus.Consenter {

	plugin := strings.ToLower(viper.GetString("peer.validator.consensus.plugin"))
	if plugin == "pbft" {
		logger.Infof("Creating consensus plugin %s", plugin)
		return pbft.GetPlugin(stack)
	}
	logger.Info("Creating default consensus plugin (noops)")
	return noops.GetNoops(stack)

}
```