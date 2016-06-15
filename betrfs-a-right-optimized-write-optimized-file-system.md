# BetrFS: A Right-Optimized Write-Optimized File System
## Authored jointly by Stony Brook University, Tokutek Inc., Rutgers University and MIT

#### Student Name: Zhu Li
#### Student ID: 2015210959
#### Major: Computer Science, Tsinghua University
#### Organization: Institute of High Performance Computing
#### Instructor: Prof. Jiwu Shu

### What is the paper discussing?
This paper is posted on FAST 2015, which introduced a Write-Optimized Index(abbreviated as WOI) called Betr-Tree, and a file system built upon it called BetrFS.

As the title suggests, this file system mainly addresses microwrite problems while trying to maintain its competitiveness again state-of-the-art counterparts on other workload features.

### Some background knowledge here:

1. Amongst all kinds of file systems, indexed file systems are built on top of search trees (B+ Tree being a proper example we learnt from textbooks). Such elegant(thereby complicated) data structures provides excellent overall performance while leaving a huge space for optimization on various delicate matters.

2. Microwrites can be interpreted as "intensive small random writes", which is usually a headache for plain indexed file systems.

3. WOI is a file index optimized for microwrites.

4. **FUSE** means file system in user space, which is known for its good read and bad write performance. This paper focuses on in-kernel optimization. Thus FUSE is mentioned here as a bad example :(

5. **LSM Tree** is one of the most cited WOI these days, it's been proposed 20 years or so. It must be really excellent to survive that long as a topic among scholars and engineers.

6. We're talking about file system for block devices and this paper never mentioned flash storage or other NVMs. You can then assume that this paper is for **hard disk drives**.

7. This paper is accepted by FAST 2015. Their continuing work on the system got accepted by FAST 2016 and won the best paper award. Gods, that's awfully impressive! When they say **future work** they mean it.

### What are some key ideas?
Since it's **key ideas**, I'll try to be brief.

1. Microwrite is a headache.

2. FUSE is bad.

3. Caching, buffering and flushing forever.

4. Wise is the man to cooperate with a DB company and modify an existent file system, rather than to implement your own one from scratch. (You might even borrow their machines)

5. Everything in the experiment shall be well-explained.

### Motivation:
1. Microwrite causes performance when metadata are often modified, which adds a lot to the overhead.

2. Redesigning the data structure for file index is a matter of algorithmic optimization. Therefore, you get better asymptotical complexity if you do make some breakthrough.

### Design:
1. Betr-Tree is a search tree which act as underlying data structure for the file index. It has much larger nodes than traditional a B+ Tree.

2. LSM Tree is again mentioned in the "Motivation" section, I believe they also drew inspiration from it as well.

3. The **overall schema** is in **key-value** form, where keys are organized in the nodes of the search tree.

4. For metadata, keys represent full paths while values represent metadata.

5. For data, keys represent full path along with block number while values represent the true data.

6. **Upserts** are kind of operations that optimize updates by using insertion, buffering and flushing. **"Upsert" is a shining point of this paper.**

7. **Blind write** is favored in BetrFS, as extra reads bring undesired caching behaviors.

8. Most metadata operation is expected to be implemented with "upserts". But some are currently not available or impossible.

9. **Microwrites are done with "upserts", terribly efficient :)**

10. **Transaction and compression** mechanisms are inherited from TokuDB, they find them sufficient without further optimization. Not the main focus here.

11. A dirty cache means write-backs. They choose to keep the cache clean, i.e. **only to use the cache for reads**. Writes are done with "upserts".

12. Why is FUSE bad? Because it reads before writes. Too many metadata operations.

13. Ext4 is chosen to be the block manager. It's just unnecessary to reinvent the wheels.

### Technical details:
1. Tokutek is a database company, with TokuDB being their product. Its source code helps saving a lot of work.

2. The **BetrFS** file system is implemented as a **kernel module** (.ko file).

3. Certain kernel-level modifications are encapsulated into a lib called **"klibc"**.

4. **Code-level optimization is done**: avoid reckless stack allocations, rewrite recursions, etc.

### Performance analysis:
1. Most experiments (covering every basic operation, several featured workloads, benchmarks at different vertical levels) showed excellent performance, **with microwrites being the greatest selling point, two orders of magnitude faster.**.

2. The test machine they used seemed rather poorly configured. (Did they run the experiments on commodity PC? Now that's not quite professional.)

3. Given only 4 cores, scalability is not to be judged here.

4. Large reads and writes are supposed to be at least competitive. It turns out they're OK, but can be improved.

5. **Renaming is a real pain spot**. It's actually a copy-delete operation. That hurts.

6. Tree nodes are cached in a cache table. On exceeding a low watermark, they're flushed (to maintain consistency with memory). On exceeding a high watermark, they're written back (to maintain consistency with disk). **This background behavior causes performance fluctuation, but is acceptable**.

7. To recap, reads are competitive, small writes are truly excellent, large writes can be improved, renames are bad, deletes are not so satisfactory.

8. Application runs well on BetrFS. Only those **"read-before-write"** ones performed poorly. This weakness has been mentioned before.

### Conclusion:
1. Optimization for microwrites is beautifully done.

2. More to be done in the future.

### What's on your mind?
This paper is so informative, took me long enough just to read it through.

Life is hard.
