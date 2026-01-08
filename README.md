# CXL is All You Need

> A comprehensive, curated repository for Compute Express Link (CXL)
> Covering Research Papers, Specifications, Simulation/Emulation tools, Benchmarks, and Resources for Type 1, 2, & 3 Devices.

## Introduction

Inspired by the transformative impact of the "Attention Is All You Need" paper in AI, "CXL is All You Need" aims to be the definitive hub for the next revolution in computer architecture: Compute Express Link (CXL).

As data centers evolve towards disaggregated and heterogeneous computing, CXL has emerged as the industry-standard interconnect offering high-bandwidth, low-latency connectivity with memory coherency. This repository aggregates scattered resources into a single, organized knowledge base for researchers, system architects, and developers.

Whether you are looking for the latest IEEE/ACM research papers, setting up a QEMU-based simulation environment, exploring Type 3 memory expansion, or benchmarking latency and throughput, you will find the essential resources here.

## Why CXL?

The traditional server architecture is facing a bottleneck known as the "Memory Wall" and efficient resource utilization challenges. CXL addresses these critical issues through three main pillars:

### 1. Breaking the Memory Wall
Modern workloads (AI/ML, In-memory DB) demand more memory capacity and bandwidth than traditional DRAM slots can provide.
* Expansion: CXL allows CPUs to access memory attached via PCIe-like interfaces with near-DRAM latency.
* Bandwidth: It enables scaling memory bandwidth independently of the main memory channels.

### 2. Cache Coherency & Heterogeneity
In a heterogeneous computing era (CPU, GPU, FPGA, SmartNIC), efficient data sharing is key.
* Coherency: CXL maintains cache coherency between the host CPU and accelerators without complex software overhead.
* Unified Interface: It supports three protocols (CXL.io, CXL.cache, CXL.mem) to cover various use cases from caching devices (Type 1) to memory buffers (Type 3).

### 3. Resource Disaggregation & Pooling
CXL enables the shift from server-centric to data-centric architecture.
* Memory Pooling: Unused memory in one server can be pooled and assigned to another host dynamically, reducing stranded memory and TCO (Total Cost of Ownership).
* Composable Infrastructure: Hardware resources can be composed on-the-fly based on workload requirements.

---


## Official Specifications
[CXL consortium](https://computeexpresslink.org/)
<img width="1557" height="871" alt="Image" src="https://github.com/user-attachments/assets/4d791456-0bfc-482d-82fb-63298593005e" />


### CXL 1.x
* CXL 1.0 and 1.1 represent the nascent stage of the technology, based on the PCIe 5.0 physical layer (32 GT/s). A key characteristic of this phase is that it is limited to Single Node configurations. That is, it supports only a 1:1 connection structure between one host processor and one CXL device. Therefore, CXL 1.1 is primarily used for Direct Attached memory expansion or simple accelerator connections without switches.   


### CXL 2.x
* CXL 2.0, announced in 2020, is a pivotal specification driving commercial adoption. While retaining the PCIe 5.0 (32 GT/s) physical layer, it introduced CXL Switching to significantly enhance system flexibility.

* Switching & Pooling: Single-level switches allow a host to access multiple devices, or multiple hosts to share a memory pool. This is the core technology enabling dynamic resource allocation.   

* Multi-Logical Device (MLD): Supports partitioning a single physical CXL memory device into up to 16 logical devices, enabling different hosts to use distinct partitions simultaneously.   

* Security: CXL IDE (Integrity and Data Encryption) was introduced to ensure data integrity and confidentiality at the link level.   

* Global Persistent Flush: Standardized functionality to safely store data during system power loss.   

### CXL 3.x
* CXL 3.0, released in August 2022, achieved a leap in data transfer speed and scalability. Based on the PCIe 6.0 physical layer, bandwidth doubled to 64 GT/s, and the signaling method changed from NRZ to PAM4 (Pulse Amplitude Modulation 4-level).   

* Fabric Capabilities: Going beyond single-level switching, it supports Multi-level Switching, enabling complex fabric topologies like Spine-Leaf. This allows for large-scale system configurations extending beyond the rack.   

* Peer-to-Peer (P2P): Supports direct data exchange between devices without passing through the host CPU. This maximizes efficiency for GPU-to-GPU data transfer or storage-to-memory communication.   

* Memory Sharing: While CXL 2.0 pooling involved "partitioning," CXL 3.0 supports true "sharing." Hardware-based coherency management allows multiple hosts to access and modify the same memory address space (Cache Line) concurrently.   

* CXL 3.1, released in late 2023, further strengthens fabric scalability and security.

* Port Based Routing (PBR): Improved routing mechanisms for massive fabric expansion.   

* Trusted Security Protocol (TSP): Extends security scope to accelerators and memory devices, supporting virtualization-based Trusted Execution Environments (TEE) and enabling safe processing of Confidential Computing workloads.   

* Extended Metadata: Expanded metadata fields to exchange more sophisticated state information between hosts and devices.   

### CXL 4.0
* [PDF](https://computeexpresslink.org/wp-content/uploads/2025/12/CXL_4.0-Webinar_December-2025_FINAL.pdf)

### PHY & Electrical Specs
* The physical layer of CXL relies heavily on PCIe technology.

* Signal & Speed: CXL 1.x/2.0 uses PCIe 5.0 (32 GT/s), while CXL 3.x uses PCIe 6.0 (64 GT/s). The PAM4 signaling introduced in CXL 3.0 transfers twice the data per clock but requires stronger Forward Error Correction (FEC) to ensure signal integrity.   

* Flit Structure: CXL uses fixed-size Flits (Flow Control Units) for low-latency communication. CXL 1.1/2.0 uses 68-byte Flits, whereas CXL 3.0 introduces 256-byte Flits for high bandwidth and FEC support. However, it selectively supports 68-byte mode for latency-sensitive applications.   

* Latency: By bypassing the heavy block layer of PCIe and optimizing the transaction layer, CXL achieves significantly lower latency than standard PCIe. It typically adds only about 20-40ns of latency compared to local DDR memory, exhibiting performance characteristics similar to NUMA remote socket access.   


## CXL Device Protocols
* Although the CXL interface physically shares a single link, it logically multiplexes three protocols.   

* CXL.io: Functionally nearly identical to PCIe. Handles device discovery, enumeration, configuration, interrupts, and non-coherent Load/Store. All CXL devices must support CXL.io.   

* CXL.cache: Allows the Device to access the Host's (CPU) memory coherently. This enables devices to snoop the CPU cache or read/write data directly, preventing data inconsistency.   

* CXL.mem: Allows the Host (CPU) to access the Device's attached memory coherently. The CPU can use the device memory like system memory via standard Load/Store instructions without separate drivers or DMA.   



### CXL.io / CXL.cache / CXL.mem
## Device Types
### Type 1: Caching Devices / Accelerators
* Type 1: Caching Devices / Accelerators
* Protocols: CXL.io + CXL.cache

* Characteristics: Accelerators that lack local memory or do not expose it to the host. They use CXL.cache to utilize the host CPU's memory as their cache.

* Key Devices: SmartNICs, Atomic accelerators. For example, a SmartNIC can process network packets and write the results directly to the receive queue (Ring Buffer) in host memory via CXL.cache.
### Type 2: Accelerators with Memory
* Protocols: CXL.io + CXL.cache + CXL.mem

* Characteristics: Accelerators with their own high-performance memory (HBM, GDDR, etc.). The CPU can push data to the accelerator memory via CXL.mem, and the accelerator can access system memory via CXL.cache.

* Bias Mode: Supports two modes for memory coherency optimization.   

* Host Bias: CPU manages coherency. Device memory is treated like standard system memory.

* Device Bias: Device manages coherency. Reduces overhead when the accelerator intensively accesses its local memory.

Key Devices: GPUs, FPGAs, AI ASICs.   
### Type 3: Memory Expanders
* Protocols: CXL.io + CXL.mem

* Characteristics: The most actively adopted type currently, used to expand system memory capacity and bandwidth. Since it does not support CXL.cache, the device does not cache host memory, but the host uses the device's memory (Host-Managed Device Memory, HDM) as main memory.

* OS View: The OS sees Type 3 devices as "CPU-less NUMA nodes." Existing NUMA management policies can be applied directly.

* Key Devices: CXL Memory Buffers, Memory Expander Cards (Add-in Cards), EDSFF (E1.S/E3.S) memory modules.

## Simulation & Emulation Tools
### QEMU CXL Support
* QEMU CXL Support
* QEMU offers the most accessible emulation environment for CXL system software development and driver testing. Key CXL 2.0 features like Type 3 devices, switches, and interleaving are included in the mainline.[link](https://github.com/linuslau/CXL-Emulator-QEMU)

### Gem5 CXL Models
* Gem5 provides cycle-accurate simulation for hardware architectures, used for precise analysis of CXL performance impacts. While QEMU focuses on functional emulation, Gem5 excels in timing and latency modeling.
* CXLSim & gem5-CXL: Researchers have released repositories like 'CXLSim' or 'gem5-CXL' extending Gem5 to model CXL transaction and link layer latencies. These models simulate FLIT packing/unpacking overheads, switch delays (e.g., tens of ns per hop), and PCIe bus contention.
* Usage: In Full System mode, booting a real Linux kernel and running benchmarks allows analysis of how CXL memory latency affects overall system IPC (Instructions Per Cycle) and application performance.   
- gem5-CXL [link](https://github.com/SlugLab/gem5-CXL)

### CXL Simulation
- Yang, Yiwei, et al. "CXLMemSim: A pure software simulated CXL. mem for performance characterization." arXiv preprint arXiv:2303.06153 (2023). [PDF](https://arxiv.org/pdf/2303.06153) [github](https://github.com/SlugLab/CXLMemSim)
- Kim, Seongbeom, et al. "CXLSim: A Simulator for CXL Memory Expander." 2025 IEEE International Conference on Big Data and Smart Computing (BigComp). IEEE, 2025. [PDF](https://ieeexplore.ieee.org/stamp/stamp.jsp?arnumber=10936878&casa_token=rhmkm_VCCTAAAAAA:Xp7PqW9c-N9hGD-Oxkwq8i4oOXIDt-oE72BJdH0sa6Wq3fvtJWdRsuBqi2BVqimXx-XkLw&tag=1)

### CXL Emulation
- OCEAN – Open-source CXL Emulation at Hyperscale Architecture and Networking [github](https://github.com/cxl-emu/OCEAN)
- Gioiosa, Roberto, et al. "Hardware-Software Co-Development for Emerging CXL Architectures." [PDF](https://www.memsys.io/wp-content/uploads/ninja-forms/5/MemSys_25.pdf)

### software
- MemMachine, an open-source memory layer for advanced AI agents. [github](https://github.com/MemMachine/MemMachine)
- Scalable Memory Development Kit [github](https://github.com/OpenMPDK/SMDK)
- Famfs Shared Memory Filesystem Framework [github](https://github.com/cxl-micron-reskit/famfs)

### Intel CoFluent / DRAMSys
* Intel CoFluent: A modeling tool for large-scale data center or cluster-level system design. It is useful for visualizing traffic flows, bottlenecks, and resource utilization in CXL memory pooling scenarios before actual hardware deployment. [link1](https://www.intel.com/content/dam/www/public/us/en/documents/white-papers/cofluent-system-planning-and-optimization-paper.pdf), [link2](https://www.intel.com/content/dam/www/public/us/en/documents/white-papers/cofluent-methodology-for-sysml-white-paper.pdf)
* DRAMSys: A SystemC TLM-2.0 based DRAM subsystem simulator, which has recently added CXL.mem protocol support. It models memory media (DDR5, HBM) behavior behind the CXL controller precisely, allowing analysis of the impact of refresh cycles or bank conflicts on overall CXL performance. [link1](https://www.iese.fraunhofer.de/en/solution/dramsys.html), [link2](https://wiki.f-si.org/index.php?title=DRAM_simulation_with_the_simulator_DRAMSys)


## Profiling & Benchmarks
### Micro-benchmarks
- Weisgut, Marcel, et al. "CXL-Bench: Benchmarking Shared CXL Memory Access." Proceedings of the VLDB Endowment. ISSN 2150: 8097. [PDF](https://hpi.de/oldsite/fileadmin/user_upload/fachgebiete/rabl/publications/2025/p10_weisgut_cxlbench.pdf)
### profiling
- Liu, Jinshu, et al. "Systematic cxl memory characterization and performance analysis at scale." Proceedings of the 30th ACM International Conference on Architectural Support for Programming Languages and Operating Systems, Volume 2. 2025. [PDF](https://dl.acm.org/doi/pdf/10.1145/3676641.3715987)
- Liu, Jinshu, et al. "Dissecting cxl memory performance at scale: Analysis, modeling, and optimization." arXiv preprint arXiv:2409.14317 (2024). [PDF](https://arxiv.org/pdf/2409.14317)
- Ji, Houxiang, et al. "Demystifying a CXL Type-2 Device: A Heterogeneous Cooperative Computing Perspective." 2024 57th IEEE/ACM International Symposium on Microarchitecture (MICRO). IEEE, 2024. [PDF](https://ieeexplore.ieee.org/document/10764537?denied=)
### Application Workloads
- Wang, Xi, et al. "Exploring and evaluating real-world cxl: use cases and system adoption." arXiv preprint arXiv:2405.14209 (2024). [PDF](https://arxiv.org/pdf/2405.14209)
  

## Research Papers
### Survey & Tutorials
- Das Sharma, Debendra, Robert Blankenship, and Daniel Berger. "An introduction to the compute express link (cxl) interconnect." ACM Computing Surveys 56.11 (2024): 1-37. [PDF](https://dl.acm.org/doi/pdf/10.1145/3669900)
- Sharma, Debendra Das. "Compute Express Link®: An open industry-standard interconnect enabling heterogeneous data-centric computing." 2022 IEEE Symposium on High-Performance Interconnects (HOTI). IEEE, 2022. [PDF](https://ieeexplore.ieee.org/document/9912551?denied=)



### Memory Pooling & Disaggregation
- Zhong, Yuhong, et al. "Oasis: Pooling PCIe Devices Over CXL to Boost Utilization." Proceedings of the ACM SIGOPS 31st Symposium on Operating Systems Principles. 2025. [PDF](https://dl.acm.org/doi/pdf/10.1145/3731569.3764812)
- Hermes, Jon, et al. "Udon: A case for offloading to general purpose compute on cxl memory." arXiv preprint arXiv:2404.02868 (2024). [PDF](https://arxiv.org/pdf/2404.02868)
- Gouk, Donghyun, et al. "Memory pooling with cxl." IEEE Micro 43.2 (2023): 48-57. [PDF](https://ieeexplore.ieee.org/stamp/stamp.jsp?arnumber=10018233&casa_token=leUkWhBNNfcAAAAA:pE2GPTE_nrGizvkiilSGRa6gXqL-cGPvbcmwYdMHZrfr0BB_13FN7TysjDE1k3DA2Zf_zw)
- Wu, Jianbo, et al. "Performance Study of CXL Memory Topology." Proceedings of the International Symposium on Memory Systems. 2024. [PDF](https://dl.acm.org/doi/pdf/10.1145/3695794.3695809)
- Li, Huaicheng, et al. "Pond: Cxl-based memory pooling systems for cloud platforms." Proceedings of the 28th ACM International Conference on Architectural Support for Programming Languages and Operating Systems, Volume 2. 2023. [PDF](https://dl.acm.org/doi/pdf/10.1145/3575693.3578835)
- Boles, David, Daniel Waddington, and David A. Roberts. "Cxl-enabled enhanced memory functions." IEEE Micro 43.2 (2023): 58-65. [PDF](https://ieeexplore.ieee.org/stamp/stamp.jsp?tp=&arnumber=10015193)
- Ahn, Minseon, et al. "Enabling CXL memory expansion for in-memory database management systems." Proceedings of the 18th International Workshop on Data Management on New Hardware. 2022. [PDF](https://dl.acm.org/doi/epdf/10.1145/3533737.3535090)
- Yang, Qirui, et al. "Performance evaluation on cxl-enabled hybrid memory pool." 2022 IEEE International Conference on Networking, Architecture and Storage (NAS). IEEE, 2022. [PDF](https://ieeexplore.ieee.org/stamp/stamp.jsp?arnumber=9925356&casa_token=OgB61hOM3t4AAAAA:tiF4wIV4M3X8koPVlzlfANIban_KD3fWmdyeTWPNnyDWHkPVD0AbNrrhcc3ab_uSlnswIA)



### Cache Coherence & Consistency
- Wang, Zixuan, et al. "The Hitchhiker's Guide to Programming and Optimizing CXL-Based Heterogeneous Systems." arXiv preprint arXiv:2411.02814 (2024).[PDF](https://arxiv.org/pdf/2411.02814)
- Sun, Yan, et al. "Demystifying cxl memory with genuine cxl-ready systems and devices." Proceedings of the 56th Annual IEEE/ACM International Symposium on Microarchitecture. 2023.[PDF](https://dl.acm.org/doi/pdf/10.1145/3613424.3614256)
- Cabrera, Anthony M., Aaron R. Young, and Jeffrey S. Vetter. "Design and analysis of CXL performance models for tightly-coupled heterogeneous computing." Proceedings of the 1st International Workshop on Extreme Heterogeneity Solutions. 2022. [PDF](https://dl.acm.org/doi/pdf/10.1145/3529336.3530817)

  

### System Software & OS Support
- Maruf, Hasan Al, et al. "Tpp: Transparent page placement for cxl-enabled tiered-memory." Proceedings of the 28th ACM International Conference on Architectural Support for Programming Languages and Operating Systems, Volume 3. 2023. [PDF](https://scontent-ssn1-1.xx.fbcdn.net/v/t39.8562-6/330632436_575201664331658_1420778763610758182_n.pdf?_nc_cat=105&ccb=1-7&_nc_sid=e280be&_nc_ohc=7DeV0Rqi8cMQ7kNvwE6ceoi&_nc_oc=AdnwlvG5zxkVLGddNhmI6VK2R9L4oGj53hdUo8R_WNjqrcXW-4sS5vfEW-KwK5-RVQA&_nc_zt=14&_nc_ht=scontent-ssn1-1.xx&_nc_gid=5YH5knyy5h6cUO8tJchJEw&oh=00_AfrOMxrkdW5KK8cFedRhfmOoklTUrR0_AWiIYekIGZ9B0Q&oe=695EBA6F)

### Use Cases
- Ji, Houxiang, et al. "Para-ksm: Parallelized Memory Deduplication with Data Streaming Accelerator." 2025 USENIX Annual Technical Conference (USENIX ATC 25). 2025. [PDF](https://www.usenix.org/system/files/atc25-ji.pdf)
- Zhou, Zhe, et al. "NeoMem: Hardware/Software Co-Design for CXL-Native Memory Tiering." 2024 57th IEEE/ACM International Symposium on Microarchitecture (MICRO). IEEE, 2024.[PDF](https://arxiv.org/html/2403.18702v2)
- Wang, Jaylen, et al. "Designing cloud servers for lower carbon." 2024 ACM/IEEE 51st Annual International Symposium on Computer Architecture (ISCA). IEEE, 2024. [PDF](https://ieeexplore.ieee.org/iel8/10609566/10609567/10609689.pdf)


## Learning Resources
### Talks & Videos
- [CXL Consortium Youtube](https://www.youtube.com/@CXLConsortium)
- [Open Compute Project Youtube](https://www.youtube.com/@OpencomputeOrg)
  
### Articles & Blogs
## Community & Ecosystem
- [CXL consortium](https://computeexpresslink.org/)
- [The future of Memory and Storage](https://futurememorystorage.com/)
- [Synopsys Compute Express Link (CXL) IP Solutions](https://www.synopsys.com/designware-ip/interface-ip/cxl.html#products)
- [Controller IP for CXL](https://www.cadence.com/ko_KR/home/tools/silicon-solutions/protocol-ip/pcie-and-compute-express-link/controller-for-pcie-and-cxl/controller-for-cxl.html)
- [Panmnesia CXL switch](https://panmnesia.com/)
- [Xcena CXL SoC](https://xcena.com/)
- [AMD Versal CXL FPGA](https://www.amd.com/en/products/adaptive-socs-and-fpgas/versal/gen2/premium-series.html)
- [Intel CXL FPGA](https://www.altera.com/products/devkit/a1jui0000049utmmam/agilex-7-fpga-i-series-development-kit-2x-r-tile-and-1x-f-tile)
- [OCP Global summit 2025](https://www.opencompute.org/events/past-events/2025-ocp-global-summit)
- [Memverge](https://memverge.ai/cxl-forum-at-fms-2022/)
- [The future of memory and storage](https://futurememorystorage.com/) 

## Contributing
- JSL  M.S Inha university
- If you want to contribute on a "CXL is All You Need", please contact wlstjr4425@gmail.com.

## License
- MIT
