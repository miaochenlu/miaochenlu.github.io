因为最近在做Hardware Transactional Memory的相关工作，因此在这里整理一下logging相关的知识。

根据不同的类型分:

### undo log and redo log

In volatile transactions, undo logging supports faster commits because, on transaction completion all the in-place updates would have already taken place (in the cache); commit therefore only requires two simple steps: discarding the undo log and ﬂash-clearing the speculative write bits to make the write set visible to other threads. Durable transactions, however, impose additional constraints. Both the undo log and the write set (data) have to be written to persistent memory – only then, can the transaction be committed. While techniques have been proposed for minimizing the ﬁne grained ordering overheads while writing log entries [20], ﬂushing the write set can signiﬁcantly increase commit time.

Redo logging, in contrast, requires only the redo log to be written to persistent memory at commit time. This is because the redo log, in addition to serving as a recovery log in case of a failure, can also provide the up-to-date values on commit.

### cacheable and uncacheable logging

<img src="/Users/jones/Library/Application Support/typora-user-images/image-20200410001015488.png" alt="image-20200410001015488" style="zoom:50%;" />

cacheable logging和uncacheable logging这两者区别在于log的写回经不经过cache.

很多研究认为log updates绕过cache, 直接到达memory比较好。原因之一是log的存在是为了系统的故障恢复，因此不会被反复用到，不需要cache的时间空间局部性原理的加成。其二，cache的大小有限，log在cache中的存在占用了cache的资源，导致更多的cache conflicts, 使得性能下降。

但是呢，实现发现uncacheable logging在persistent memory systems中的性能表现的比cacheable logging要好。原因之一是cacheable write指令的完成需要更多的clock cycles。 x86处理器提供了实现cacheable write的指令，如`movnti`, `movntq`, 以及实现uncacheable write的指令，如`movl`, `movq`。由于数据在寄存器之间的移动以及写回memory比写回cache有更大的延迟这两个原因，uncacheable logging需要更长的时间。其二，系统通常会缓存uncacheable writes，利用write coalescing减少I/O次数以提高效率。但是如果缓存没有充分利用，数据就写回的话，就会造成资源的浪费，I/O次数增加，性能下降

<img src="/Users/jones/Library/Application Support/typora-user-images/image-20200410001128169.png" alt="image-20200410001128169" style="zoom:50%;" />

