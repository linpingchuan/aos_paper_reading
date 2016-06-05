# SDF: Software-Defined Flash for Web-Scale Internet Storage Systems
## Authored jointly by Baidu and Peking University

#### Student Name: Zhu Li
#### Student ID: 2015210959
#### Major: Computer Science, Tsinghua University
#### Organization: Institute of High Performance Computing
#### Instructor: Prof. Jiwu Shu

### What is the paper discussing?
Flash-based storage has seen increasingly wider application in large scale datacenters. However, the over-provisioning due to driver- or kernel-level software designs makes a considerable portion(30% ~ 50%) of raw capacity unusable. To address this problem Baidu proposed a software-defined flash storage system with hardware/software co-design.

In order to achieve higher aggregate bandwidth and better utilization of raw flash capacity, the newly-designed flash storage exposes certain hardware features to allow for software-level optimization. Also the design has taken heavily into account the web-based characteristics of the Baidu datacenters, thus tuning the design and configuration accordingly.

The new design exhibits good performance, as it has to be.

Currently the newly-designed software-defined flash storage has been mass-produced and deployed widely in the storage backend of Baidu, with more than 3000 operational.

### Some background knowledge here:
1. Flash storage is essentially a pure electronic storage device, which can be viewed in a hierarchical chain of device, chip, die, channel, block, page, string, cell, MOSFET, floating gate, electric capacitor, etc. Among which you need to consider chips, channels as possible elements of parallelism.

2. Flash storage is read/written by pages, erased by blocks and must be erased prior to an in-place rewrite. Try to think of a page as 8KB, a block as 2MB if you need a rough idea.

3. Over-provisioning is to reserve a certain amount of raw capacity for necessary device buffering and caching. You'll see your HDD begin to slow down noticeably when you've used up more than 95% of its space. For flash storage this percentage could be lower, maybe 70% usage is enough to slow the world down.

4. SATA, SCSI, PCIe are different interfaces for storage device to connect to computers, of which PCIe is currently the top performer.

5. The FTL is a layer between OS and hardware, whose job responsibility has been a research focus.

### What are some key ideas?
1. Over-provisioning of flash storage has to be reduced to a more reasonable level to save money, as higher space utilization means fewer disks to be purchased.

2. Traditional research focus on flash storage mustn't be traded-off for mere raw space. Concerns include wear levelling, write amplification, parallelism, etc.

3. "Software-defined" often goes with predictable performance, which means being configurable and stable.

4. The access patterns of web-based storage should be analyzed and accommodated by the design.

5. Not only software-level optimization is needed, the new system requires customized hardware to be designed with FPGA logics and actually manufactured.

6. Whenever significant enhancement rises, aggressive trade-off follows. Though the overall weighted cost-benefit has to be positive enough, so as to impress people.

### Motivation:
1. Over-provisioning too high, a waste of space is a waste of money.

2. Bandwidth utility can be higher.

3. Commodity SSD is not parallel enough.

4. Performance fluctuates.

5. The topics of old: r/w balance, wear levelling, garbage collection, write amplification, etc.

6. Tuned for Baidu datacenters.

### Design:
1. FTL(the software layer) is customized to provide asymmetric r/w size. With read of 8KB (page) and write of 8MB(as large as a block actually). Apparently this makes erasing much easier, as well as eliminating write e amplification. It's obvious that software must do some scheduling and batching to get that 8MB writing to work.

2. Right below the FTL is an FPGA-based hardware controller, tuned to work with software layer, while providing good channel-level parallelism.

3. IO causes interrupts. Therefore for big writes up to tens or hundreds of MB, the writes from the chips are merged to reduce the interrupt overhead.

4. After carefully studying commodity SSDs, some unnecessary features are stripped away. Static wear levelling, garbage collection, inter-chip parity check are not used.    

5. Channel is exposed to software for the sake of high parallelism.

6. Manually erase is allowed at application level. That's how they make the performance predictable.

7. ioctl() is used to bypass a lot of syscall overheads at kernel space.

### Technical details:
1. Too many specific product name and numbers to be listed in detail, would otherwise be too boring as well.

2. The hardware controller has a central datapath chip and a group of per-chip controller. The central controller is responsible for PCIe DMA and chip bridging, whereas each per-chip controller has its own FTL(the FTL is what is needed to make a flash array officially an independent flash storage). Thus it's more like a bunch of independent flash devices grouped into one.

3. Each channel is presented to the OS as a device, which is the key to high parallelism. Conventional SSD contains a lot of chips, with every chip containing a lot of channels. Such fine granularity design surely is parallelism-oriented. The rest is up to software.

4. The upper stream of the flash storage is a KV store, using LSM tree to manage the K-V pairs.

### Performance analysis:
1. Bandwidth saturation is good.

2. Scales linearly with respect to channels.

3. Performance stable and as expected.

### Contributions:
1. Reducing the overhead of random writes.

2. Supporting high IO concurrency.

3. Reducing access latency.

### Conclusion:
1. Co-design of hardware and software is the key to turning the ideas into final product.

2. Ideas strictly follow the internal workload characteristics.

3. Many more refinements are on the road, including load-balancing, data reliability, etc.

### What's on your mind?
1. Tycoons like IBM, Intel and Qualcomm have the core technology on chips, thus it seems quite difficult for our universities to conduct hardware-level research by actually making our own chips. Too high a price.

2. Every feature left out will have a substitute somewhere higher.

3. Without fundamental innovation from down below(mind-blowing ones), all you can do is trade-off here, there and everywhere.

4. If a very slow operation is uncontrollable, your performance shall thereby be unpredictable.

5. I read this paper long ago. Without videos or slides, to write a summary on something you've already forgotten is a real pain.
