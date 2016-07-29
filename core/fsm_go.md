## fsm.go
peer connection 的状态机。

```go
func NewPeerConnectionFSM(to string) *PeerConnectionFSM {
	d := &PeerConnectionFSM{
		To: to,
	}

	d.FSM = fsm.NewFSM(
		"created",
		fsm.Events{
			{Name: "HELLO", Src: []string{"created"}, Dst: "established"},
			{Name: "GET_PEERS", Src: []string{"established"}, Dst: "established"},
			{Name: "PEERS", Src: []string{"established"}, Dst: "established"},
			{Name: "PING", Src: []string{"established"}, Dst: "established"},
			{Name: "DISCONNECT", Src: []string{"created", "established"}, Dst: "closed"},
		},
		fsm.Callbacks{
			"enter_state":  func(e *fsm.Event) { d.enterState(e) },
			"before_HELLO": func(e *fsm.Event) { d.beforeHello(e) },
			"after_HELLO":  func(e *fsm.Event) { d.afterHello(e) },
			"before_PING":  func(e *fsm.Event) { d.beforePing(e) },
			"after_PING":   func(e *fsm.Event) { d.afterPing(e) },
		},
	)

	return d
}
```