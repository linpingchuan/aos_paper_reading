# IOFlow: a Software-Defined Storage Architecture
##Authored by Microsoft Research

####Student Name: Zhu Li
####Student ID: 2015210959
####Major: Computer Science, Tsinghua University
####Organization: Institute of High Performance Computing
####Instructor: Prof. Jiwu Shu

## What is the paper discussing?
This paper by Microsoft Research presents a software-defined storage architecture for **small-to-middle scale datacenters**, called IOFlow.

## What are some key ideas?
### Some background knowledge here:
1. Software defined networking: [software defined networking](https://en.wikipedia.org/wiki/Software-defined_networking "software defined networking")
2. Software defined storage: [software defined storage](https://en.wikipedia.org/wiki/Software-defined_storage "software defined storage")
3. Hypervisor, virtual machine, tenant. (Hopefully you're familiar with these terms.)

### Motivation:
1. **Predictability** of system performance and application behaviors is desirable.
2. **End-to-end SLA** (service-level agreement) is hard to achieve today:
	- Guaranteed storage bandwidth
	- Guaranteed high IOPS and priority
	- Per-application control over decisions along the IO path

### What's an IO path?
Let me show you an example:

1. There's a host machine. (Call it "hypervisor" if you like.)
2. It runs two virtual machines, named vm1 an vm2.
3. The host machine has two disks, one HDD, one SSD.
4. vm1's virtual disk resides on the HDD, namely vmdk1.vhd (Yeah, a virtual disk is simply represented as a file on the host machine).
5. vm2's virtual disk resides on the SSD, namely vmdk2.vhd.
6. What happens when there is an IO request from vmdk1.vhd to vmdk2.vhd?
7. What about an IO request from the hypervisor to the VM?
8. Think about the path a request has to traverse to finish an IO request, that's the IO path.
9. With a more complex datacenter structure, the path could be long, long enough to cause noticeable performance decline. (The paper says 18 maybe, possibly:()

### Ideas borrowed from software defined networking:
1. **Virtualization and high-level abstraction**: apparently, softwares are introduced to deal with heterogeneous protocols, OSes, hardwares, etc.
2. **Data plane and control plane**: we've seen this pair of concepts quite often. They're just concepts on system design, perhaps.
3. **Centralized control**: I think things doesn't have to be centralized, but being so makes the research easier to begin with. (You have to build it first and make it distributed later.) Both SDN and SDS starts from a centralized control plane. The same applieds for IOFlow as well.
4. Directly programmable: Instead of "configurable", they use "programmable". I think this means both being **configurable at parameter level** and with **a full set of APIs** to do the coding.

### Design Issues:
1. **Global visibility**: The IOFlow architecture comprises a centralized control plane, with global visibility. (That is to say, it sees all, commands all, and are always watching over the whole datacenter.) I guess that's why the author claimed that IOFlow is currently only suitable for small-to-middle scale datacenters, with tens of host machines and hundreds of VMs. (You just don't let one machine watch over thousands of storage servers, the IOs could be a real flood.)
2. **Topology, nodes, edges, graph**: IOFlow has a discovery component (quite an important part) that discovers the topology of the datacenter, whereas the nodes may be host machines, guest machines, SSDs, HDDs (or their virtual counterpart), even RAMs. The edges are the links (physical or logical) in-between. **All are abstracted into G=(V,E)**. It's beautiful.

### Contributions:

1. Defined and built storage control plane
2. Controllable queues in data plane
3. Interface between control and data plane (IOFlow API)
4. Built centralized control applications that demonstrate power of architecture

## Technical details?
1. Each IOFlow requests are represented as a tuple of four: **<{VMs}, {File Operations}, {Files}, {Shares}> ---> SLA**
2. IOFlow use queue as the main data structure on each "node". (Scheduling leads to a queue, or a bunch of queues. It seems quite natural:) )
3. IOFlow APIs allows three type of behavior:
	- Classification of requests
	- Queue servicing
	- Routing of requests
4. Rate limiting is a challenge: **how and why it's difficult**. See more details in the paper. So an empirical heuristic solution is applied. (To discover and adjust periodically.)
5. SLA enforcement: To put SLA into effect. **But where to put it?** At the hypervior? At physical disks? At virtual machines? **This is a distributed system problem.** So, think accordingly.
6. **Dynamic bandwidth management**: an algorithmic problem. With a centralized controller, it's actually simplified.
7. The project was developed and deployed under Windows, with ca. **22k lines of C/C++ and thousands of lines of C#**. The IOFlow architecture is deployed as system drivers. (**Thus no modification of upper level applications and virtual machines is needed.**)
8. They plan to move on to **larger datacenters**: e.g. public cloud.

## About the performance analysis?
1. Good, of course.
2. They ran the benchmark on their 10-host and 120-vm cluster, clearly specified, well illustrated and convincing enough.
3. One thing was quite interesting: the centralized control had a CPU usage of only 0.3%, and a network overhead of merely 2MB/s with over 13000 flows running. (If the central controller is sitting idle as such, **there must be some bottleneck elsewhere.**)

## What about the conclusion?
1. High-level end-to-end policies
2. Programmable dataplane queues
3. Inspired by SDN
4. Good applicability
5. Useful in real action

## What are your thoughts?
1. Prior to reading the paper, I had no idea about SDN or SDS. So it took me four days to digest the full paper, quite a headache actually.
2. Though a tough one to read, the paper is of truely high quality. (It's the first top-tier paper from which I haven't found a single grammatical error.) You can tell which paper is written by native English speakers by the sentences and vocabulary.
3. I got a binary of the toolkit containing the IOFlow API, but only applicable on Windows Server 2012. Constructing an SDS network may require another two or three days. It's a shame I couldn't work fast enough to run the demo.
4. Code is definitely not open-source (it's microsoft). Next time I'd find a paper with easily accessible code.
5. I wish I could be a faster reader. Too time-consuming :(
6. Maybe I should read the paper more than once to get a clearer picture. (This reading note is a mess.)
