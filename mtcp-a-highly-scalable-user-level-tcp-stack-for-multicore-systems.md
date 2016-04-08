# mTCP: A Highly Scalable User-level TCP Stack for Multicore Systems
## Authored jointly by KAIST and Princeton

####Student Name: Zhu Li
####Student ID: 2015210959
####Major: Computer Science, Tsinghua University
####Organization: Institute of High Performance Computing
####Instructor: Prof. Jiwu Shu

## What is the paper discussing?
This paper focused on handling large number of **short TCP connections** on **multicore systems** by introducing **a user-level TCP stack**.

## What are some key ideas?
### What's the problem?
1. When a system handles a lot of short-lived TCP connections(surely the payloads are small), state-of-the-art TCP stacks tend to waste a lot of CPU cycles(**up to 80% or so**) in kernel space.
2. **Per-packet processing** brings a lot of back-and-forth between user and kernel space. **context switches can be really expensive**.
3. Connection locality, core affinity, cache friendliness. These all point to the same target: **let packets of a connection be processed by a same core**. Linux kernel is not yet fully capable of this feature.
4. fd space is shared. **File descriptor**, you know.

### How does the paper address these problems?
1. The TCP stack is completely moved up to user space.
2. Multiple TCP packets are queued up (consider this some sort of buffering), packed into a **single real TCP packet** which is later handled by the kernel space. **This ammortized the cost of context switch**. (I was wondering about the capacity of the payloads. If several packets are batched into a real one that is handled by the kernel space, these packets must be small, right? The paper didn't seem to mention it.)
3. Consider the APIs and tools we might need, say, sockets, pipes, polling, etc. Core affinity is achieved by adding a thread parameter to each function call in these tools. (Every function call is now associated with a certain thread.) **This trades compatibilty for core affinity**, as both APIs and application source codes have to be modified to make things work. (Although there're three ways to name the goal, I find "core affinity" best describe what we want.)
4. Not only **fd**, a lot of related parameters are kept in **per-core and lock-free data structures**, contibuting to core affinity and avoiding lock contention.


## Technical details?
1. The user-level TCP runs as a **thread**, which **resides on the same core** as the application thread.
2. One TCP port, one application. This limitation still persists.
3. mTCP works at per-NIC level, i.e., other NICs may work on conventional TCP stack.
4. mTCP enhanced network IO efficiency by **switching from polling to event-driven processing**, using RX and TX queues.
5. PCIe operations are expensive, so let's batch'em up.
6. mTCP thread and the corresponding application thread **are located at the same core**, which helps reduce a lot of communicaton overheads via sharing spaces.
7. However, thread context switch is yet another type of expense.
8. "We localize all resources."
9. RSS do the load-balancing at core level.
10. Large page is used to lower TLB miss rate. (Certainly this is experimental.)
11. TCP timeout problem is handled at two levels, namely fine and coarse granularity. (I haven't quite understood this part yet.)
12. Short-lived connections are handled with multiple lists. The catch is to prevent **flooding packets like SYN and ACK** to block the transmission of normal data packets.
13. **Local mempool and pre-allocation** is used to speed up **connection establishment**.
14. APIs are now thread-related.
15. Certainly security is a trade-off. User-level TCP stack can be corrupted by inappropriate application behaviors.

## What about the conclusion?
1. mTCP did well.
2. Batching is both transparent and bi-directional.
3. Lock-free data stucture, cache-aware thread, per-core resource management.

## What are your thoughts?
1. After reading several top-tier papers on design of network or computer systems, I find the ammount of work behind any of these papers simply astounding. A paper on OSDI is probably worth five on AAAI, I guess. **You chose this path, you got fubar.** Good luck to those PhDs.
2. Although mTCP required modifications of both system APIs(e.g. socket, poll), and every application they've benchmarked, they still claim to have some sort of backward compatibility. **You call that compatibility?**
3. The bechmarking part of this paper is so detailed, that I decided to skip it. I can imagine the days and nights they've spent running tests. **I salute them.**
4. "Related work" is real fun to read. You got to see how these scientists make fun of each other's work :) **Acutally, it's good to check out the papers at this part, if you really wish to delve into the area.**
5. mTCP performed well up to 8 cores, but things could be totally different at 9 or 12 cores, as the paper "Non-scalable locks are dangerous" told.
