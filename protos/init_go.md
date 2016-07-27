## init.go
导入 protos 包时候执行的初始化操作，主要配置日志模块信息。

```
package protos

import "github.com/op/go-logging"

var logger *logging.Logger

func init() {
	// Create package logger.
	logger = logging.MustGetLogger("protos")
}
```