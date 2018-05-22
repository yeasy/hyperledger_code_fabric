#### msgprocessor.go

定义消息处理接口

    // Processor provides the methods necessary to classify and process any message which
    // arrives through the Broadcast interface.
    type Processor interface {
    	// ClassifyMsg inspects the message header to determine which type of processing is necessary
    	ClassifyMsg(chdr *cb.ChannelHeader) Classification

    	// ProcessNormalMsg will check the validity of a message based on the current configuration.  It returns the current
    	// configuration sequence number and nil on success, or an error if the message is not valid
    	ProcessNormalMsg(env *cb.Envelope) (configSeq uint64, err error)

    	// ProcessConfigUpdateMsg will attempt to apply the config update to the current configuration, and if successful
    	// return the resulting config message and the configSeq the config was computed from.  If the config update message
    	// is invalid, an error is returned.
    	ProcessConfigUpdateMsg(env *cb.Envelope) (config *cb.Envelope, configSeq uint64, err error)

    	// ProcessConfigMsg takes message of type `ORDERER_TX` or `CONFIG`, unpack the ConfigUpdate envelope embedded
    	// in it, and call `ProcessConfigUpdateMsg` to produce new Config message of the same type as original message.
    	// This method is used to re-validate and reproduce config message, if it's deemed not to be valid anymore.
    	ProcessConfigMsg(env *cb.Envelope) (*cb.Envelope, uint64, error)
    }



