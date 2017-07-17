#### config_util.go
提供一些辅助方法，如：

* TemplateGroupMSPWithAdminRolePrincipal(configPath []string, mspConfig *mspprotos.MSPConfig, admin bool) *cb.ConfigGroup：创建在指定 config 路径上的 config 项，以及该项相关的 Admins、Readers、Writers 的策略。
* TemplateGroupMSP(configPath []string, mspConfig *mspprotos.MSPConfig) *cb.ConfigGroup：在指定配置路径上创建 MSP 配置值，调用的 TemplateGroupMSPWithAdminRolePrincipal() 方法。