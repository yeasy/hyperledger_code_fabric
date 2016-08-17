### rest\_api.go

API 的入口在这里。

定义 ServerOpenchainREST 结构，负责对外的 REST 请求响应。

最关键的，路由表如下所示。

```go
func buildOpenchainRESTRouter() *web.Router {
    router := web.New(ServerOpenchainREST{})

    // Add middleware
    router.Middleware((*ServerOpenchainREST).SetOpenchainServer)
    router.Middleware((*ServerOpenchainREST).SetResponseType)

    // Add routes
    router.Post("/registrar", (*ServerOpenchainREST).Register)
    router.Get("/registrar/:id", (*ServerOpenchainREST).GetEnrollmentID)
    router.Delete("/registrar/:id", (*ServerOpenchainREST).DeleteEnrollmentID)
    router.Get("/registrar/:id/ecert", (*ServerOpenchainREST).GetEnrollmentCert)
    router.Get("/registrar/:id/tcert", (*ServerOpenchainREST).GetTransactionCert)

    router.Get("/chain", (*ServerOpenchainREST).GetBlockchainInfo)
    router.Get("/chain/blocks/:id", (*ServerOpenchainREST).GetBlockByNumber)

    // The /devops endpoint is now considered deprecated and superseded by the /chaincode endpoint
    router.Post("/devops/deploy", (*ServerOpenchainREST).Deploy)
    router.Post("/devops/invoke", (*ServerOpenchainREST).Invoke)
    router.Post("/devops/query", (*ServerOpenchainREST).Query)

    // The /chaincode endpoint which superceedes the /devops endpoint from above
    router.Post("/chaincode", (*ServerOpenchainREST).ProcessChaincode)

    router.Get("/transactions/:uuid", (*ServerOpenchainREST).GetTransactionByUUID)

    router.Get("/network/peers", (*ServerOpenchainREST).GetPeers)

    // Add not found page
    router.NotFound((*ServerOpenchainREST).NotFound)

    return router
}
```

