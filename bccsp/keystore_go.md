## keystore.go

定义 KeyStore 接口，存储秘钥。

```golang
type KeyStore interface {

	// ReadOnly returns true if this KeyStore is read only, false otherwise.
	// If ReadOnly is true then StoreKey will fail.
	ReadOnly() bool

	// GetKey returns a key object whose SKI is the one passed.
	GetKey(ski []byte) (k Key, err error)

	// StoreKey stores the key k in this KeyStore.
	// If this KeyStore is read only then the method will fail.
	StoreKey(k Key) (err error)
}
```
