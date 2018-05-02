### logging.go

`peer `logging 相关命令的入口。

进一步支持getlevel\|setlevel\|revertlevels等子命令。

```go
// Cmd returns the cobra command for Logging
func Cmd(cf *LoggingCmdFactory) *cobra.Command {
	loggingCmd.AddCommand(getLevelCmd(cf))
	loggingCmd.AddCommand(setLevelCmd(cf))
	loggingCmd.AddCommand(revertLevelsCmd(cf))

	return loggingCmd
}
```



