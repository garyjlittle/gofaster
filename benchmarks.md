- [Benchmarks](#benchmarks)
  * [Benchmarks and workloads](#benchmarks-and-workloads)
    + [IO / Storage Benchmarks - Simulate a particular application or workload](#io---storage-benchmarks---simulate-a-particular-application-or-workload)
      - [Application Benchmarks](#application-benchmarks)
      - [Workload Benchmarks](#workload-benchmarks)
    + [IO / Storage Workload Generators - Create your own workload pattern](#io---storage-workload-generators---create-your-own-workload-pattern)
  * [Orchestrators](#orchestrators)
    + [Application Benchmarks](#application-benchmarks-1)
    + [Virtualization Benchmarks](#virtualization-benchmarks)

# Benchmarks

## Benchmarks and workloads
There is no shortage of  benchmarks and workload generators, I group them into the following categories

### IO / Storage Benchmarks - Simulate a particular application or workload
#### Application Benchmarks
* JetStress - simulates IO of a Microsoft exchange database server
#### Workload Benchmarks
* SFS 97/2008 - simulates the IO workload pattern of an email server
* SFS 2016? - varios IO profiles
* SPC-1 - simulates database workloads.

### IO / Storage Workload Generators - Create your own workload pattern
* IOmeter - one of the most well-known, popular with Windows users.  Allows client-server deployment
* vdbench - Java based IO generator, popular due to its configurability. Allows client-server deployment
* fio - C based IO generator, versatile and very popular on Linux, written by the maintainer of the Linux SCSI stack
* bonnie++ - Has some built-in workloads

## Orchestrators
* HCIbench - orchestrates vdbench.  Can all workloads are uniform (e.g. all VM's run same workload)
* X-Ray - orchestrates fio.  Each VM can run a separate workload, incorporates temporal and failure dimensions


### Application Benchmarks
* HammerDB (Free) - Simulates various database workload patterns.  HammerDB only generates the queries and DB schema, it requires a database engine to connect to.
* Benchmark Factory (Commercial) - Like HammerDB it simulated a variety 

### Virtualization Benchmarks
* VMmark - VMWare hypervisor only.  Mostly used to test host hrdware/CPU/Memory performance/density. (Java, Mail, Web, DB)
* SPECVirt - Bundles SPECweb, SPECmail, AppServer, CPU bechmarks and creates a tile-base measure of performance/density
* TPCx-V - Measures density of VMs running OLTP workloads. Hypervisor agnostic
* TPCx-HCI  - Measures density of VMs running OLTP workloads on HCI infrastructure.  Hypervisor agnostic.





```
Proof of Concept
·         Intro to different benchmarking tools (, , , , , , etc.)
o    Gary Little
o    Conversational/supporting materials
o    Does it have a place in evaluating HCI or not?
·         Installing/configuring X-Ray (not on target cluster, network/DHCP requirements, IPMI requirements, cloning/editing scenarios)
o    Reference product documentation
o    Matt will look for existing install video
·         X-Ray test scenario overview - what each test aims to demonstrate, why are the testing parameters reasonable, what we use to generate workloads, open source/customizable
·         Analyzing X-Ray test results - Speaking to the example X-Ray results for each test, why it looks the way it does, how/why we would (generically) expect competitors to have issues
```
