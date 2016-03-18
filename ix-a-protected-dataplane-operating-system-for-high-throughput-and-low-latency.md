#IX: A Protected Dataplane Operating System for High Throughput and Low Latency
## Authored jointly by Stanford and EPFL

####Student Name: Zhu Li
####Student ID: 2015210959
####Major: Computer Science, Tsinghua University
####Organization: Institute of High Performance Computing
####Instructor: Prof. Jiwu Shu

This is the first time I wrote a reading note on a serious top-tier paper. Since this paper is focuses on system design, I'll organize the reading note by the following structure:

1. What the paper is about?
2. What problem it is addressing?
3. What are the new ideas?
4. Technical details?
5. Anything remarkable on performance analysis?
6. What are your questions after a detailed reading?

Since it's the first time to write a reading note on research papers, I expect there'll be a lot of space for improvement, please feel free to give suggestions. Any advice is appeciated.

###1. What the paper is about?
This paper is on the design and implementation of an operating system specially tuned for **application deployed on distributed system with intensive I/O demands**.

###2. What kind of problems it is addressing?
To put it simply, the targeted application requires four criteria to run smoothly, namely **high packet rate, low latency, system protection, resouce efficiency**.

**High packet rate**. The system constantly sustains intensive IO requests, with each request having only a fairly small request and response ammount. Compared with CDN, which handles massive IO of large video files(mostly, I guess), this is quite a different type of headache. **Facebook is the first example I can think of, that faces such an emminent problem.** Every time you search for a person on facebook, you send a small request and get a small response. Think about millions of folks doing so at the same time.

**Low latency**. It's simple enough: make it fast.

**System protection**. However the system is designed, it must be secure, with critical privileged features isolated from untrusted application code.

**Resource efficiency**. Normally resources refer to software and hardware, this one here, I guess it's mostly referred to hardware. The reason is that we've got more and better machines today. It used to be a different picture.

###3. What are the new ideas?
Hardware used to be precious resource, and network application and distributed system were never so popular in the old days. Today operation engineers often worry about low utility rate of hardware resources. (As well as imbalance, with some part of the machines going hungry, while others full)

**So the conventional commodity OS were mostly tuned for better scheduling.** Whereas such design performs poorly for IO intensive network applications(datacenter applications).

That's why this paper aimed for a new design. It turns out the performance analysis indicates that about **3/4 of time were consumed on kernel space**, which involved I/O interrupt, CPU scheduling, context switch, etc.

The key idea is to **extract certain privileged kernel features to user space**.

The **kernel-user** structure is changed to **controlplane-dataplane-user**.

I would think of the three-tier structure like the following.
**User space**: untrusted application code.
**Dataplane**: normal APIs, some privileged CPU features, system calls.
**Controlplane**: the real kernel.

###4. Technical details?
I don't know how detailed this part should be. I can't just copy the whole paper here after all.

Other related papers involve redesigns of network stack, transmission model(from TCP to RDMA), system level API(replacing POSIX APIs) or even kernel modification(too much labor, too much risk).

IX embraced the following strategies to reach its goal:

- **Library OS**: The dataplane is like a library OS, see "Exokernel" for more idea.

- **From kernel space to user space**: By extracting some features from kernel to user. With its own networking stack, page table and certain prilvileged instruction, IX is able to run faster.

- **Assychonous, non-blocking**: To make the system more responsive.

- **Batching system calls and zero-copy APIs**: To improve IO performance.

- **libix**: To make life easier for developers. (Life is so hard already.)

- **multicore scalabily**: optimized in syscalls.

- **security model**: root ring 0, non-root ring 0, non-root ring 3.

###5. Anything remarkable on performance analysis?
It's the first time I see how a rigorous benchmarking is done, all the parameters are just dazzling. IX's ability to sustain up to 250000 connections at the same time is indeed somewhere near industrial level, amazing.

###6. What are your questions after a detailed reading?
I guess this is the most important part of paper reading. I've read the paper twice, one rough scan and one detailed workout, yet still having trouble digesting the whole paper.

After watching the talk video, here're the questions given by the audience(all are famous computer scientists).

----------
Q: "With the Ethernet NIC having broader bandwidth, how will you adjust your batching strategy?"

A: Adaptively, and to carefully balance the tradeoff between latency and overhead.

----------
Q: "Now you mentioned run-to-completion strategy, how will you handle network IO requests that take too long to finish?"

A: We hope to use interrupt, drop packet(better to avoid both), or other measures to terminate such requests, as run-to-completion is not adapted for this. (Still space for improvement?)

----------
Q: "How would you handle a failure in the middle of a batching system call?"

A: We intend to keep multiple operations running for each batching. (Trade resources for reliability?)

----------
Watching the video without caption was a bit of a challenge, luckily my English is OK:)

I think the ability to raise critical questions requires a thorough understanding of the paper in the first place. That's way beyond my grasp for now.
