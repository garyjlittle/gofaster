- [Benchmarks](#benchmarks)
  * [Benchmarks and workloads](#benchmarks-and-workloads)
    + [IO / Storage Benchmarks - Simulate a particular application or workload](#io---storage-benchmarks---simulate-a-particular-application-or-workload)
      - [Application Benchmarks](#application-benchmarks)
      - [Workload Benchmarks](#workload-benchmarks)
    + [IO / Storage Workload Generators - Create your own workload pattern](#io---storage-workload-generators---create-your-own-workload-pattern)
  * [Orchestrators](#orchestrators)
    + [Application Benchmarks](#application-benchmarks-1)
    + [Virtualization Benchmarks](#virtualization-benchmarks)

# Benchmarks for hyper-converged infrastructure

## Benchmarks and workloads
There is no shortage of  benchmarks and workload generators that can conveivably be used to measure hyper-converged infrastructure (HCI).  Broadly HCI benchmarks measure either the storage component (how fast), or the hypervisor component (how dense). 

Storage benchmarks have been traditionally of interest because HCI is most different from traditional data-center architecture in the way that storage is provisioned.  HCI vendors typically provide their own distributed storage abstraction, which provides the bulk of the propritory IP.  For example Nutanix provides NDSF (Nutanix Distributed Storage Fabric), VMware has vSAN (Virtual SAN) and Cisco Hyperflex provide HX Data-platform.

### IO / Storage Benchmarks - Simulate a particular application or workload
#### Application Benchmarks
* JetStress - simulates IO of a Microsoft exchange database server
#### Workload Benchmarks
* SFS 97/2008 - simulates the IO workload pattern of an email server
* SFS 2016? - varios IO profiles
* SPC-1/SPC-2 - simulates database workloads. SPC-1 simulates an OLTP IO workload, SPC-2 sequential IO workload.

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


## Choosing an IO benchmark
### Pitfalls and Anti-patterns
Choosing an IO benchmark for HCI evaluation will usually differ from benchmarks that have been used to test traditional storage.  The main reason is that in HCI storage is distributed among many nodes.  Taking a well known and respected benchmark such as SPC-1 and running it on an HCI cluster will not yield the expected results.  SPC-1 excpects to have the workload executed on a host machine, conntected to a single large storage device.  In HCI every host is connected to a relatively small distributed storage device.  

The power of HCI comes from the aggregation of many nodes in a cluster.  A benchmark such as SPC-1 would easily exhaust the local resources of the node where the benchmark is running, but leave many of the nodes idle.  Some HCI offerings such as Nutanix can be configured to operate as a single-large storage cluster, but this is not the normal deployment mode.

The same can be said for running any benchmark or workload which only executes on a single host. Results again are likely to vastly underestimate the total cluster capacity.  I conducted an experiment which shows just how much the capacity of an  HCI cluster can be reported : https://www.n0derunner.com/2018/11/you-are-here-the-art-of-hci-performance-testing/

In this test, we see that a single host issuing a low-concurrency workload might yield only 2500 IOPS (at 400uSec), but the cluster as a whole is capable of >600,000 IOPS at 800uSec.

### Testing HCI the right way.
To test the performance capacity of an HCI cluster, the workload should be generated on every node in the cluster.  This is how work will be generated in a production environment.  Unfortunately this requires coordinating the creation of multiple VMs across multiple nodes and aggregating the results.

Workload generators such as IOmeter, fio and vdbench support client-server configurations but much of the burden of setup and coordination is placed on the user.

The leading HCI vendors all provide tools for carrying out distributed load/performance testing.  We will cover them here

### HCI specific performance test tools
The major vendors provide the following tools, all of which support the ability to provision multiple worker VMs across the cluster and execute workload patterns.

* Nutanix : X-Ray
* vmWare : HCIbench
* Cisco : HXbench

#### HCIbench
HCIbench is a tool from VMware that can be run against any VMware base virtualization platform.  It cannot run against other hypervisors, as it relys on vCenter to provision the VMs.  

#### Workload generator
HCIbench uses the vdbench workload generator.  Users can create the workload pattern via a web-form. e.g. 
<img src="https://github.com/garyjlittle/images/blob/master/HCIbench-vdbench-parameter-page.png" width="300" height="600">
Currently all worker VMs must run the same workload.  This makes it impossible to run noisy neighbor tests where VMs on the same cluster have diferent characteristics.  e.g. VDI users (bootstorm) on an OLTP database, or a DSS workload running a large querey on the same cluster as a latency sensitive EMR workload.
#### Failure modes
HCIbench does not support failing hardware during test.

### X-Ray
X-Ray is a tool from Nutanix that can run on multiple hypervisors and virtualization platforms
* Nutanix (AHV or ESXi) with vCenter or PRISM
* vmware VSAN on ESXi with vCenter
* Cisco Hyperflex with vCenter
* Microsoft Hyper-V on Storage spaces direct with SCVMM

#### Workload generator
X-Ray uses the fio workload generator.  Users can specify the workload pattern using standard fio configuration files, and yaml to describe how to place VMs within the cluster.  It is possible to have different workloads running concurrently.  e.g. it's possible to run VDI users on 3 nodes, and an OLTP workload on another, or an OLTP on two nodes and DSS workloads on four nodes.  Currently the fio and YAML files must be edited outside the web browser.
#### Failure modes
X-Ray can be configured to connect to the out-of-band host management infrastructure so that power-failure test can be executed while the cluster is under load.


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
