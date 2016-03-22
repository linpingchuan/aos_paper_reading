# The Scalable Commutativity Rule: Designing Scalable Software for Multicore Processors
Authored jointly by MIT and Havard, Doi:10.1145/2517349.2522712  

####Student Name: Zhu Li
####Student ID: 2015210959
####Major: Computer Science, Tsinghua University
####Organization: Institute of High Performance Computing
####Instructor: Prof. Jiwu Shu

## What is the paper discussing?
The paper talks about the scalablity, but not just an implementation issue, but even before implementation.

It introduces a rule, "Whenever interface operations commute, they can be implemented in a way that scales."

By this rule, the paper claims that the system can be **designed to be** scalable, more than just **implemented to be** so.

To illustrate their idea, the research group devised a tool named **COMMUTER**, that automatically generates tests that verifies the scalability of high-level design of interfaces.

## What are some key ideas?
Scalability evaluation on large applications usually involves high pressure, various workload, multiple cores. When problems arises, they're usually distributed among different workloads, which exhibit different type of bottlenecks.

Developers tend to resolve the issue by one-at-a-time debugging and fixing, thus making it too late to find a scalable solution.

Should the verification be executed during an earlier phase, e.g. the designing of software interfaces, things would've be different.

### Related Work:
1. Thinking about scalability:
2. Commutativity:
3. Test case generation:

### The Rule:
1. Actions
2. Commutativity
3. Implementations
4. Rule and Proof

### Designing Commutative Interfaces:
To be refined.

### The COMMUTER:
To be refined.

### Finding Scalability Opportunities:
To be refined.

### Performance Evaluation:
To be refined.

## What about the conclusion?
This paper introduced the idea of **commutative rule**, illustrated it with rigorous proof, implemented it with the **COMMUTER** toolkit and applied it on sv6.

It proivides new insights to developers, especially for those developers of a large software projects, that requires excellent **multicore scalability**.

## What are your thoughts?
I haven't got enough time to read it in detail. It shall be done soon.
