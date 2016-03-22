# Mars Code
Authored by Gerard J. Holzmann, Doi:10.1145/2560217.2560218

####Student Name: Zhu Li
####Student ID: 2015210959
####Major: Computer Science, Tsinghua University
####Organization: Institute of High Performance Computing
####Instructor: Prof. Jiwu Shu

## What is paper discussing?
This article discusses some of the precautions the JPL flight software team took to improve its reliability.

## What are some key ideas?
#### Key insights:
1. The software that controls an interplanetary spacecraft must be designed to a **high standard of reliability**; any small mistake can lead to the loss of the mission and its unique opportunity to expand human knowledge.
2. Extraordinary measures are taken in both hardware and software design to ensure spacecraft reliability and that the system can be **debugged and repaired from millions of miles away**.
3. Formal methods help verify(**formal verification**) intricate software subsystems for the potential of race conditions and deadlocks; new model-checking techniques automate the verification process.

#### On reducing risk:
1. Design a good software architecture.
2. Maintain a good development process.
3. **Risk-based coding rules.**
4. **Tool-based cdoe reviews.**
5. **Model checking.**

#### On peer review:
Peer reviewers can excel at identifying design flaws but are **much less reliable** at the more **down-to-earth job** of checking for **mundane issues** like rule-compliance and avoidance of common coding errors.

## What about the conclusion?
1. The successful landing on Mars by the rover is a solid proof for the success of the whole space project.
2. Redundancy is always a simple and good precaution, both in hardware and software.

## What are your thoughts?
Conventional design principles in software are not enough for large systems that require **absolute reliability**. System verification and validation needs to be more automated and systematic.

Also, redundancy is an all-time option, if you've got someone to pay for the extra costs. Considering it a game of probability, it works by increase the **number of independent repeated trials** to ensure victory.
