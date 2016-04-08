# Scalable Kernel TCP Design and Implementation for Short-Lived Connections
## Authored jointly by Tsinghua and Sina

####Student Name: Zhu Li
####Student ID: 2015210959
####Major: Computer Science, Tsinghua University
####Organization: Institute of High Performance Computing
####Instructor: Prof. Jiwu Shu

## What is the paper discussing?
The paper is mainly about redesigning the TCP stack to enable multicore system to handle intensive network requests that comprises short-lived connections, without sacrificing compatibility to BSD socket API.

## What are some key ideas?
1. The paper focuses on network applications that involve intensive requests which are estalished on short-lived connections.
2. State-of-the-art Linux kernel TCP stack can't fully utilize multicore resources(20+ cores) under such circumstances.
3. Socket management involves globally shared data structure, which caused noticeable decline in peformance, thus can be recognized as a bottleneck.
4. Socket is treated as a **special file** in Linux VFS, which turned out inappropriate for the situation, as unnecessary overhead on **inode** and **dentry** is otherwise avoidable.
5. Compatiility to exsisting applications is maintained, which further strengthens the applicability of this paper.
6. Performance is verified through benchmarking, which proved convincing and satisfactory.
7. Also, related works are mentioned and compared on the dimensions above, among whom, most lack the compatibility to existing applications, due to their major modification to the structure of TCP stack.

## Technical details?
1. Partitioning the **listen socket table** and **established socket table** into local copies. This takes care of the passive connection partitioning.
2. Use "Receive Flow Deliver"(with hardware support) to enable active connection partitioning.
3. The FastSocket-aware VFS is compatible with BSD socket API, allowing deployment of existing applications.

## What about the conclusion?
Solved problems on shared data structure, lock contention.
Scaled almost linearly.
Maintained compatibility.
Performed well.


## What are your thoughts?
Interesting.
