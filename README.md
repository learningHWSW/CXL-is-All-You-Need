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

### CXL 1.x 

### CXL 2.x

### CXL 3.x

### CXL 4.0

### PHY & Electrical Specs


## CXL Device Protocols
### CXL.io / CXL.cache / CXL.mem
## Device Types
### Type 1: Caching Devices / Accelerators
### Type 2: Accelerators with Memory
### Type 3: Memory Expanders

## Simulation & Emulation Tools
### QEMU CXL Support
### Gem5 CXL Models
### Intel CoFluent / DRAMSys
### FPGA Based Emulation

## Profiling & Benchmarks
### Micro-benchmarks
### System Profiling Tools
### Application Workloads

## Research Papers
### Survey & Tutorials
### Memory Pooling & Disaggregation
### Cache Coherence & Consistency
### System Software & OS Support
### Use Cases

## Learning Resources
### Talks & Videos
### Articles & Blogs

## Community & Ecosystem

## Contributing
## License
