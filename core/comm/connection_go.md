### connection.go
跟 GRPC 连接相关的一些方法。

```go
func NewClientConnectionWithAddress(peerAddress string, block bool, tslEnabled bool, creds credentials.TransportAuthenticator) (*grpc.ClientConn, error)
```

向指定地址建立一条 grpc 通道连接。

```go
func InitTLSForPeer() credentials.TransportAuthenticator
```

返回 peer 的 TLS 客户端认证信息，从 peer.tls.cert.file 文件中读取生成。