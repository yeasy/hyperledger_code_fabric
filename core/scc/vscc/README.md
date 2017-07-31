### vscc

Validate System Chaincode

负责背书的校验。

相对于目前escc只做签名，vscc的工作也就是验证（每个）签名的有效性，以及是否符合背书策略（有效签名个数是否满足）。

特殊地，vscc中有一部分专门针对lscc的校验。

进一步的分析请查阅`validator_onevalidsignature_go.md`。
