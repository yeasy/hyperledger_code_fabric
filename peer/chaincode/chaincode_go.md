### chaincode.go

chaincode子命令初始化和注册。

#### Cmd\(cf \_ChaincodeCmdFactory\) \_cobra.Command

注册chaincode子命令，包含install\|instantiate\|invoke\|package\|query\|signpackage\|upgrade\|list。  
此函数会被上级命令peer注册时调用，见peer/main.go mainCmd.AddCommand\(\)。

#### init\(\)

初始化chaincode子命令的一些变量flag标志，很多变量都给了默认值。注意在真正命令执行时会用cli cmd里面的参数值来替换这些变量值，这些变量值都是放在本文件里面的全局变量。

