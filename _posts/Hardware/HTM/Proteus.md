# Proteus: A Flexible and Fast Software Supported Hardware Logging approach for NVM



Our approach introduces two new instructions that a `log-load` instruction creates a log entry by loading the original data, and a `log-ﬂush` instruction writes the log entry to NVMM.



We also consider integrating Proteus with a battery backed WPQ, allowing the WPQ to be considered part of the persistency domain. Once writes reach the WPQ they are considered durable. The presence of a battery-backed WPQ is consequential: it presents a new opportunity to avoid writes to the NVMM. A key observation that we exploit is that most log entries are created and discarded, because failures are rare.





#### Memory Persistency Models

| -                          | -                                                            | -                                                            | -                                                            |
| -------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| strict persistency         | strict persistency piggy-backs on the sequential consistency model by specifying that a store that is globally visible must also have persisted. Before each store persists to NVMM, all previous stores must have persisted to NVMM. | It's easier to reason about failure safety.                  | It comes with significant persormance costs of not allowing write reordering and write coalescing that natuarally occur in write back caches. |
| Epoch persistency          | Relax the ordering constraint in strict persistency. It allows the programmer to put a persist barrier which deﬁnes an epoch in the program. Stores from an epoch (i.e. between two persist barriers) can persist in any order, but they must all persist before the persist barrier. | At the persist barrier, the processor may stall waiting for all stores from the epoch to persist | Write coalescing can occur for stores from the same epoch    |
| Buffered Epoch Persistency | Relaxes the persist barrier by not forcing prior stores to persist right away (hence the processor may not stall), as long as stores from one epoch persist prior to any stores from the next epoch |                                                              |                                                              |
| Strand Persistency         | Relaxes the ordering constraints for persists separated by a strand barrier. No ordering is enforced on persists in different strands other than those implied by persist atomicity. |                                                              |                                                              |



`clwb` and `clflushopt` flush a dirty block from caches to a write pending queue (WPQ) in the memory controller.   `clflushopt` is ordered with respect to other stores on the same cache block, however `clwb` is only ordered with respect to stores to the same address

`pcommit` flushes the dirty block from WPQ to the NVMM

`sfence` prevents stores and PMEM instructions from executing until the sfence completes.





#### Compare copy-on-write (COW) and write-ahead logging (WAL)

a write triggers data to be copied to a new location where the write will occur. The original data is left intact. COW requires ***address remapping*** so that future reads can be redirected to the new location. This remapping is generally considered expensive. For block-based storage, the cost can be amortized by applying copying to a large granularity such as a page, and committing updates infrequently [9]. However, NVMM access is byte-based and stores occur much more frequently than ﬁle writes. Thus, COW may be prohibitively expensive to use in NVMM.



 