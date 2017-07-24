#### validator.go

主要实现 txValidator 结构体。

```go
type txValidator struct {
	support Support
	vscc    vsccValidator
}
```

该结构体提供了 `func (v *txValidator) Validate(block *common.Block) error` 方法，对给定区块进行合法性检查。