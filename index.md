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
