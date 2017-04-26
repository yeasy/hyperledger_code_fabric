## localconfig

Orderer 的配置管理，默认的配置结构如下。

```golang
var defaults = TopLevel{
	General: General{
		LedgerType:     "ram",
		ListenAddress:  "127.0.0.1",
		ListenPort:     7050,
		GenesisMethod:  "provisional",
		GenesisProfile: "SampleSingleMSPSolo",
		GenesisFile:    "genesisblock",
		Profile: Profile{
			Enabled: false,
			Address: "0.0.0.0:6060",
		},
		LogLevel:    "INFO",
		LocalMSPDir: "msp",
		LocalMSPID:  "DEFAULT",
		BCCSP:       &bccsp.DefaultOpts,
	},
	RAMLedger: RAMLedger{
		HistorySize: 10000,
	},
	FileLedger: FileLedger{
		Location: "",
		Prefix:   "hyperledger-fabric-ordererledger",
	},
	Kafka: Kafka{
		Retry: Retry{
			Period: 3 * time.Second,
			Stop:   60 * time.Second,
		},
		Verbose: false,
		Version: sarama.V0_9_0_1,
		TLS: TLS{
			Enabled: false,
		},
	},
	Genesis: Genesis{
		SbftShared: SbftShared{
			N:                  1,
			F:                  0,
			RequestTimeoutNsec: uint64(time.Second.Nanoseconds()),
			Peers:              map[string]string{":6101": "sbft/testdata/cert1.pem"},
		},
	},
	SbftLocal: SbftLocal{
		PeerCommAddr: ":6101",
		CertFile:     "sbft/testdata/cert1.pem",
		KeyFile:      "sbft/testdata/key.pem",
		DataDir:      "/tmp",
	},
}
```