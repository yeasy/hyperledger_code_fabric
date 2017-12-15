#### config.go

```go
// Load parses the orderer.yaml file and environment, producing a struct suitable for config use
func Load() *TopLevel {
	config := viper.New()
	cf.InitViper(config, configName)

	// for environment variables
	config.SetEnvPrefix(Prefix)
	config.AutomaticEnv()
	replacer := strings.NewReplacer(".", "_")
	config.SetEnvKeyReplacer(replacer)

	err := config.ReadInConfig()
	if err != nil {
		logger.Panic("Error reading configuration:", err)
	}

	var uconf TopLevel
	err = viperutil.EnhancedExactUnmarshal(config, &uconf)
	if err != nil {
		logger.Panic("Error unmarshaling config into struct:", err)
	}

	uconf.completeInitialization(filepath.Dir(config.ConfigFileUsed()))

	return &uconf
}
```

从 orderer.yaml 和环境变量中读取 Orderer 的配置信息，并构建一棵配置树结构。