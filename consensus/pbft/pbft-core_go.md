### pbft-core.go
pbft 算法核心引擎。

最主要结构式 pbftCore。

```go
type pbftCore struct {
	// internal data
	internalLock sync.Mutex
	executing    bool // signals that application is executing

	idleChan   chan struct{} // Used to detect idleness for testing
	injectChan chan func()   // Used as a hack to inject work onto the PBFT thread, to be removed eventually

	consumer innerStack

	// PBFT data
	activeView    bool              // view change happening
	byzantine     bool              // whether this node is intentionally acting as Byzantine; useful for debugging on the testnet
	f             int               // max. number of faults we can tolerate
	N             int               // max.number of validators in the network
	h             uint64            // low watermark
	id            uint64            // replica ID; PBFT `i`
	K             uint64            // checkpoint period
	logMultiplier uint64            // use this value to calculate log size : k*logMultiplier
	L             uint64            // log size
	lastExec      uint64            // last request we executed
	replicaCount  int               // number of replicas; PBFT `|R|`
	seqNo         uint64            // PBFT "n", strictly monotonic increasing sequence number
	view          uint64            // current view
	chkpts        map[uint64]string // state checkpoints; map lastExec to global hash
	pset          map[uint64]*ViewChange_PQ
	qset          map[qidx]*ViewChange_PQ

	skipInProgress    bool               // Set when we have detected a fall behind scenario until we pick a new starting point
	stateTransferring bool               // Set when state transfer is executing
	highStateTarget   *stateUpdateTarget // Set to the highest weak checkpoint cert we have observed
	hChkpts           map[uint64]uint64  // highest checkpoint sequence number observed for each replica

	currentExec           *uint64                  // currently executing request
	timerActive           bool                     // is the timer running?
	vcResendTimer         events.Timer             // timer triggering resend of a view change
	newViewTimer          events.Timer             // timeout triggering a view change
	requestTimeout        time.Duration            // progress timeout for requests
	vcResendTimeout       time.Duration            // timeout before resending view change
	newViewTimeout        time.Duration            // progress timeout for new views
	newViewTimerReason    string                   // what triggered the timer
	lastNewViewTimeout    time.Duration            // last timeout we used during this view change
	outstandingReqBatches map[string]*RequestBatch // track whether we are waiting for request batches to execute

	nullRequestTimer   events.Timer  // timeout triggering a null request
	nullRequestTimeout time.Duration // duration for this timeout
	viewChangePeriod   uint64        // period between automatic view changes
	viewChangeSeqNo    uint64        // next seqNo to perform view change

	missingReqBatches map[string]bool // for all the assigned, non-checkpointed request batches we might be missing during view-change

	// implementation of PBFT `in`
	reqBatchStore   map[string]*RequestBatch // track request batches
	certStore       map[msgID]*msgCert       // track quorum certificates for requests
	checkpointStore map[Checkpoint]bool      // track checkpoints as set
	viewChangeStore map[vcidx]*ViewChange    // track view-change messages
	newViewStore    map[uint64]*NewView      // track last new-view we received or sent
}
```