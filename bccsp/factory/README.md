## factory
提供工厂模式支持，包括若干类型的 BCCSP 工厂实现。

通用工厂接口为：

```go
type BCCSPFactory interface {

	// Name returns the name of this factory
	Name() string

	// Get returns an instance of BCCSP using opts.
	Get(opts *FactoryOpts) (bccsp.BCCSP, error)
}
```

实现了两个工厂结构：

* SWFactory：软件 bccsp 的工厂；
* PKCS11Factory：基于高安全模块的实现。
