# Golang basic
How compiler knows which variable to protect
* In Go, the compiler automatically performs escape analysis, which determines whether a variable should be allocated on the stack or the heap. The stack is faster than the heap because it doesn't require the use of the garbage collector, so the compiler will attempt to allocate variables on the stack whenever possible.

If a variable is used within a goroutine or is passed as an argument to a function that may be called concurrently, the compiler will allocate the variable on the heap instead, and the Go runtime will use its internal mechanisms (such as mutexes or channels) to synchronize access to that variable to ensure that concurrent access is safe. In other words, the compiler doesn't know which variables need protection, but it knows which variables might be accessed concurrently based on their usage in the program, and allocates them on the heap and adds the necessary synchronization code to ensure correct behavior.

sync.mutex
* used to ensure that only one goroutine can access a shared resource at a time

sync.WaitGroup
* used to wait for a group of goroutines to complete before proceeding.  
* wg.Add(d int): add d to wg counter
* wg.Done(): counter--
* wg.Wait(): blocks the process until counter==0

# Resilient Distributed Datasets
Why in paper it says that "keeping data in memory can improve performance by an order of magnitude"?
* improve performance by an order of magnitude = the performance is improved by a factor of 10
* The reason that keeping data in memory can improve performance by an order of magnitude is that memory access times are much faster than disk access times. When data is stored in memory, it can be accessed directly by the CPU, whereas when it is stored on disk, it must be read from the disk into memory, which can take orders of magnitude longer.

Shared memory
* restricted form

main challenge in designing RDDs
* defining a programming interface that can provide fault tolerance efficiently
* Existing abstractions for in-memory storage on clusters offer an interface based on fine-grained updates to mutable state (e.g., cells in a table). With this interface, the only ways to provide fault tolerance are to replicate the data across machines or to log updates across machines. expensive for data-intensive workloads, as they require copying large amounts of data over the cluster network
* RDDs provide an interface based on coarse-grained transformations (e.g., map, filter and join) that apply the same operation to many data items.

### RDDs
transformations
*  RDDs can only be created through deterministic operations on either (1) data in stable storage or (2) other RDDs.
*  Examples of transformations include map, filter, and join.

# Raft
What is PrevLogIndex & PrevLogTerm
* PrevLogIndex represents the index of the log entry immediately preceding the new entries that the leader is attempting to append. In other words, PrevLogIndex points to the last log entry that the leader and its followers agree upon.
* If the follower's log does not contain an entry at the index indicated by PrevLogIndex, or if the term of the entry at that index does not match the term included in the leader's AppendEntries message, then the follower will refuse to append the new entries and instead respond with a rejection message.
* PrevLogTerm is a field that appears in AppendEntries messages sent by the leader to replicate log entries to its followers. 
* Example:
A's log: [1, 2, 3, 4, 5]
B's log: [1, 2, 3]
* Node A sends an AppendEntries message to Node B with the following information:

PrevLogIndex = 2 (the index of the last log entry that Node A and Node B agree upon)
PrevLogTerm = 2 (the term of the log entry at PrevLogIndex)
Entries = [4, 5] (the new entries that Node A wants to append to its log)

* When Node B receives the AppendEntries message, it checks its own log to see if it has an entry at index 2 with term 2. 
B's log: [1, 2, 3]
              ^  ^
              |  |--- index 2 (term = 2)
              |------ PrevLogIndex

What is commitIndex & lastApplied

What is nextIndex & matchIndex (leader)
* they are all maps
* nextIndex: node ID -> next time the index leader should send to that node /  leader believes that follower's log contains entries up to index nextIndex-1
* when leader sending entries to node i, it will include the entries starting from nextIndex[i]. If fail, leader will decrease nextIndex[i].
* matchIndex: node ID -> the highest index in the leader's log that follower i has replicated
