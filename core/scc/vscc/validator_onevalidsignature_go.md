#### validator_onevalidsignature.go

##### 参数

| 序号 | 内容 | 备注 |
| --- | --- | --- |
| 1 | 函数名 | 未使用 |
| 2 | 序列化的Env | 无 |
| 3 | 序列化的policy | 无 |

##### Invoke()

* 检查参数。
* 获取policy，`NewPolicy()`会把传入的签名策略编译成验证函数。签名策略有两种类型，一种是NOutOf（就是我们看到的AND和OR组合的背书策略，本质上是多个NOutOf的组合，表达一定个数中至少要有几个的意思），一种是SignedBy（由特定的身份签名），对于编译出来的验证函数，SignedBy就是校验给定的SignedData数组中有没有该特定身份签名的，有就返回TRUE，而NOutOf相当于SignedBy的数组，根据里面所有SignedBy返回的TRUE的个数是否满足设定的数值来返回TRUE或FAUSE。
* 从交易中抽取出所有的背书endorsements，去掉重复的身份identity（背书节点），构建一个签名集signatureSet，实质上是一个SignedData的数组，SignedData由原始数据、身份和签名组成。
* 执行`policy.Evaluate(signatureSet)`，就是去执行编译出来的验证函数，其验证思路参考上面获取policy步骤。
* 另外如果被调用的cc是LSCC的话，则执行特殊的validate过程，`ValidateLSCCInvocation()`，主要检查调用LSCC的参数，如果是实例化或更新操作则另外检查cc是否install，写集是否有且只有两个（一个LSCC，一个cc），且符合LSCC写入的规范（Key必须是要实例化或更新的cc的名字，Value必须是ChaincodeData且名字版本都匹配，对LSCC只能putstate()一次），最后检查instantiatePolicy。
* 返回shim.Success(nil)。
