### stop.go
负责 `peer node stop` 命令。

主要包括 stop 方法，通过 admin 服务停止节点。

```golang
func stop() (err error) {
	clientConn, err := peer.NewPeerClientConnection()
	if err != nil {
		pidFile := stopPidFile + "/peer.pid"
		//fmt.Printf("Stopping local peer using process pid from %s \n", pidFile)
		logger.Infof("Error trying to connect to local peer: %s", err)
		logger.Infof("Stopping local peer using process pid from %s", pidFile)
		pid, ferr := readPid(pidFile)
		if ferr != nil {
			err = fmt.Errorf("Error trying to read pid from %s: %s", pidFile, ferr)
			return
		}
		killerr := syscall.Kill(pid, syscall.SIGTERM)
		if killerr != nil {
			err = fmt.Errorf("Error trying to kill -9 pid %d: %s", pid, killerr)
			return
		}
		return nil
	}
	logger.Info("Stopping peer using grpc")
	serverClient := pb.NewAdminClient(clientConn)

	status, err := serverClient.StopServer(context.Background(), &empty.Empty{})
	if err != nil {
		fmt.Println(&pb.ServerStatus{Status: pb.ServerStatus_STOPPED})
		return nil
	}

	err = fmt.Errorf("Connection remain opened, peer process doesn't exit")
	fmt.Println(status)
	return err
}
```
