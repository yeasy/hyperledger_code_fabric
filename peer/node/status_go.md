### status.go

负责 `peer node status` 命令。

主要包括 status 方法，通过 admin 服务获取 peer 状态。

```golang
func status() (err error) {

	adminClient, err := common.GetAdminClient()
	if err != nil {
		logger.Warningf("%s", err)
		fmt.Println(&pb.ServerStatus{Status: pb.ServerStatus_UNKNOWN})
		return err
	}

	status, err := adminClient.GetStatus(context.Background(), &empty.Empty{})
	if err != nil {
		logger.Infof("Error trying to get status from local peer: %s", err)
		err = fmt.Errorf("Error trying to connect to local peer: %s", err)
		fmt.Println(&pb.ServerStatus{Status: pb.ServerStatus_UNKNOWN})
		return err
	}
	fmt.Println(status)
	return nil
}
```