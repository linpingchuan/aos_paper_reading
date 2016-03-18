#Dune: Safe User-level Access to Privileged CPU Features
## Authored by Stanford

####Student Name: Zhu Li
####Student ID: 2015210959
####Major: Computer Science, Tsinghua University
####Organization: Institute of High Performance Computing
####Instructor: Prof. Jiwu Shu

I'll organize the reading note by the following structure:

1. What the paper is about?
2. What problem it is addressing?
3. What are the new ideas?
4. Technical details?
5. Anything remarkable on performance analysis?
6. What are your questions after a detailed reading?

###1. What the paper is about?
This paper is on the design and implementation of an operating system that grants application the **access to certain hardware features without compromising safety**.

###2. What kind of problems it is addressing?
By properly enabling access for certain hardware features, applications show significant improvements in performance.

Some related works done on this topic include the following strategies:

- **Modification of the kernel**: too much labor and too risky.  
- **Bundle applications into VM images**: poorl integration with host system, which limits the performance enhancement.


###3. What are the new ideas?
Instead of provide virtualization for a complete OS, this paper limit the scope to **process-level**.

The problem with conventional VM, such as VMWare or VirtualBox, is that they go to great length to provide users with a complete OS, with CPU, memory, file system, networking, etc.

Such emulation is **general-purpose**, i.e. not necessarily suitable for machine that **runs special type of applications**(e.g. computation/IO intensive, massive connections, etc).

The Dune system functions as a modules in user and kernel space, instead of a whole OS. Its main objective is to provide **processes** with access to certain **privileged hardware features**.

To put it simply, Dune aims to be a **POSIX abstraction**, not a virtual machine.

###4. Technical details?
- **Intel's VT-x Extension**: this powerful enhancement in hardware enables the CPU to run under two modes, namely the **"VMX root"** and **"VMX non-root"**. The advantage lies in that the switch between these modes **would've otherwise been handled totally by software**, which brings about a lot of overhead. A significant improvement in performance indeed.
- **Supported hardware features**: exceptions, virtual memory and privileged modes. Why do this? Because triggering such hardware features in a VM often involves context switch, VMCALL, VM exit, etc, none of which are efficient.
- **Kernel support for Dune**: Dune doesn't act quite like a hypervisor. Instead, it acts as a module in the kernel, which allows normal processes to enter **"Dune mode"**. After successful entry, they'll have access to predefined hardware features. Read chapter 3 for details.
- **User-mode environment**: Loading a Dune application is like bootstraping an OS, but the memory and CPU layout is sort of different. Also, there's a library called libDune to make development easier.
- **Applications**: sandboxing, privilege separation, garbage collection.

###5. Anything remarkable on performance analysis?
The benchmarking strategy was well-defined and rigorous. 

###6. What are your questions after a detailed reading?

Q: What about virtualization support for IO  device?

A: They haven't evaluated  this aspect, but seems interesting enough to do a study. See IOMMU, SRIOV for more inspiration.

---

Q: Will you virtualize the VMCS to make it possible for Dune to run over a hypervisor?

A: Another OSDI paper is related to this, keyword is "turtle".

---

Q: Exposing hardware feature to user-level application will inevitably make the user-level application codes more platform-dependent. What will you do to improve the portability of libDune?

A: More refinement on the interface design of libDune is needed.(I guess that's a matter of software engineering issue.)
