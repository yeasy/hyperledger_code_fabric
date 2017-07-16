### commonutils.go

主要的函数：

字节数据解码为Payload：UnmarshalPayload([]byte)
	return：*cb.Payload, err

字节数据解码为Envelope：UnmarshalEnvelope([]byte)
	return：*cb.Envelope, err

从区块中提取指定序号的Envelope：ExtractEnvelope(*cb.Block, int)
	return：*cb.Envelope, err

从Envelope中提取Payload：ExtractPayload(*cb.Envelope)
	return：*cb.Payload, err

字节数据解码为ChannelHeader：UnmarshalChannelHeader([]byte)
	return：*cb.ChannelHeader, err

字节数据解码为ChaincodeID：UnmarshalChaincodeID([]byte)
	return：*cb.ChaincodeID, err

对消息签名：SignOrPanic(*LocalSigner, []byte)
	return：[]byte
	Ps：实际上LocalSigner的Sign()方法直接返回原数据

判断区块是否包含配置更新交易：IsConfigBlock(*cb.Block)
	return：bool
	解释：提取块的第一个Envelope，提取该Envelope的Payload，解码ChannelHeader，判断类型

其他的函数：创建新ChannelHeader、SignatureHeader等

