#### expiration.go

判断消息是否过期的过滤器。

```
type expirationRejectRule struct {
    filterSupport resources
}
```

#### Apply

从消息envelope中取出identity，判断identity标识是否过期。

