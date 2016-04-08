# Non-Sclalable Locks Are Dangerous
## Authored by MIT CSAIL

####Student Name: Zhu Li
####Student ID: 2015210959
####Major: Computer Science, Tsinghua University
####Organization: Institute of High Performance Computing
####Instructor: Prof. Jiwu Shu

## What is the paper discussing?
The paper first presented the statement that non scalable locks can cause **dramatic collapse** in the performance of **real workloads**, even for very **short critical sections**.

The second part focused on the mathematical model which explained the mechanism behind the phenomena, which involved Markov chain and queuing theory. (Hopefully the reader has some prerequisite knowledge of stochastic process.) The thoery was introduced to depict lock contention.

The third part compared the performance of several scalable locks on x86 multicore systems.

## What are some key ideas?
1. Non-scalable locks will cause serious contention when the core number reaches a certain threshold. Search "cache-coherent control" for more info.
2. The performance degradation is quite abrupt, which implies great risk for the performance evaluation of real workloads. For a system running smoothly under 24 core may well crash under 26 cores.
3. The phenomena can be modelled using Queuing Theory in Stochastic Process.(The model applied was actually quite simple, which worked out just fine. Math is magic, math is power.)
4. The non-scalable locks mentioned in this paper is ticket-based spin locks.
5. In a queuing system, we focus on three parameters: arriving rate(linear), servicing rate(constant), waiting time(linear). The simple model revealed the fact: with the number of cores getting closer to the limit, the system performance goes down with a faster rate(**that's what we call "positive feedback".**) **This is why non-scalable locks is not OK.**
6. The mathematical model was later validated with benchmarking, to prove the paper was not based on pure theoretical assertion.
7. When the critical section is large enough, the lock contetion problem seems relieved. (Understandable but impratical, for critical section shall always be as small as possible.)

## Technical details?
1. The benchmarks used for performance analysis include FOPS, MEMPOP, PFIND, EXIM.
2. The scalable locks introduced in the paper include MCS Lock, K42 Lock, CLH Lock, HCLH Lock.


## What about the conclusion?
1. Non-scalable locks can result in poor performance in multicore systems.
2. They can also bring a sudden collapse, given enough cores and confidence on the sysem:)
3. Scalable locks is an altenative to avoid the plummeting in performance, but a lock-free design should be the ultimate solution to such a problem.

## What are your thoughts?
I guess the quality of this paper is not as good as those we've read, which were published on top-tier conferences, in terms of the amount of workload and innovative ideas.

Still, the Markov chain is interesting, and my full respect to MIT and CSAIL.
I doubt if I'd ever be able to go to MIT, so far the chance is still a rarity of 3 sigma, I guess.
