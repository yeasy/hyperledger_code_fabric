### chaincode.go
chaincode子命令初始化和注册。

#### Cmd(cf *ChaincodeCmdFactory) *cobra.Command
注册chaincode子命令，包含install|instantiate|invoke|package|query|signpackage|upgrade|list。
此函数会被上级命令peer注册时调用，见peer/main.go mainCmd.AddCommand()。

#### init()
初始化chaincode子命令的一些flag标志。