- [Benchmarks for hyper-converged infrastructure](#benchmarks-for-hyper-converged-infrastructure)
  * [Benchmarks and workloads](#benchmarks-and-workloads)
    + [IO / Storage Benchmarks - Simulate a particular application or workload](#io---storage-benchmarks---simulate-a-particular-application-or-workload)
      - [Application Benchmarks](#application-benchmarks)
      - [Workload Benchmarks](#workload-benchmarks)
    + [IO / Storage Workload Generators - Create your own workload pattern](#io---storage-workload-generators---create-your-own-workload-pattern)
  * [Orchestrators](#orchestrators)
    + [Application Benchmarks](#application-benchmarks-1)
    + [Virtualization Benchmarks](#virtualization-benchmarks)
  * [Choosing a HCI benchmark](#choosing-a-hci-benchmark)
    + [Pitfalls and Anti-patterns](#pitfalls-and-anti-patterns)
    + [Testing HCI the right way.](#testing-hci-the-right-way)
  * [HCI specific performance test tools](#hci-specific-performance-test-tools)
    + [HCIbench](#hcibench)
    + [X-Ray](#x-ray)
   
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


## Choosing a HCI benchmark
### Pitfalls and Anti-patterns
Choosing an IO benchmark for HCI evaluation will usually differ from benchmarks that have been used to test traditional storage.  The main reason is that in HCI storage is distributed among many nodes.  Taking a well known and respected benchmark such as SPC-1 and running it on an HCI cluster will not yield the expected results.  SPC-1 excpects to have the workload executed on a host machine, conntected to a single large storage device.  In HCI every host is connected to a relatively small distributed storage device.  

The power of HCI comes from the aggregation of many nodes in a cluster.  A benchmark such as SPC-1 would easily exhaust the local resources of the node where the benchmark is running, but leave many of the nodes idle.  Some HCI offerings such as Nutanix can be configured to operate as a single-large storage cluster, but this is not the normal deployment mode.

The same can be said for running any benchmark or workload which only executes on a single host. Results again are likely to vastly underestimate the total cluster capacity.  I conducted an experiment which shows just how much the capacity of an  HCI cluster can be reported : https://www.n0derunner.com/2018/11/you-are-here-the-art-of-hci-performance-testing/

In this test, we see that a single host issuing a low-concurrency workload might yield only 2500 IOPS (at 400uSec), but the cluster as a whole is capable of >600,000 IOPS at 800uSec.

### Testing HCI the right way.
To test the performance capacity of an HCI cluster, the workload should be generated on every node in the cluster.  This is how work will be generated in a production environment.  Unfortunately this requires coordinating the creation of multiple VMs across multiple nodes and aggregating the results.

Workload generators such as IOmeter, fio and vdbench support client-server configurations but much of the burden of setup and coordination is placed on the user.

The leading HCI vendors all provide tools for carrying out distributed load/performance testing.  We will cover them here.

From the HCIbench user guide.

_"In a hyper-converged architecture, each server is intended to support both many application VMs, as well as contribute to the pool of storage available to applications. This is best modeled by invoking many dozens of test VMs, each accessing multiple stored VMDKs. The goal is to simulate a very busy cluster.
Unfortunately, popular storage performance testing tools do not directly support this kind of model. To achieve a simulation of a busy production cluster, much effort is required to automate load generation, monitoring and data collection after the fact. These steps waste so much valuable time available to do actual testing, even worse may introduce errors into the process.
To address this situation, VMware released a storage performance testing automation tool— HCIBench—that automates the use of the popular Vdbench testing tool in larger clusters. Users simply specify the testing parameters they would like to run, and HCIBench instructs Vdbench what to do on each and every node in the cluster.
HCIBench aims to simplify and accelerate customer Proof of Concept (POC) performance testing in a consistent and controlled way. This tool fully automates the end-to-end process of deploying test VMs, coordinating workload runs, aggregating test results, and collecting necessary data for troubleshooting purposes. Evaluators choose the profiles they are interested in; HCIBench does the rest quickly and easily."_

### HCI specific performance test tools
The major HCI vendors provide benchmark tools, all of which support the ability to provision multiple worker VMs across the cluster and execute workload patterns.  Running a simple IOPS test should return uniform results across all the tools - since the tool itself should not determine the benchmark result.

* Nutanix : X-Ray
* vmWare : HCIbench
* Cisco : HXbench

In a simple test, performing 4K random reads on a Nutanix HCI cluster, we put this theory to the test.  The results for the exact same configuration between HCIbench and X-Ray yields the following results.

[Compare HICbench and X-Ray results](hci-benchmarks-compare-hcibench-xray-results.md)

### HCIbench
HCIbench is a tool from VMware that can be run against any VMware base virtualization platform.  It cannot run against other hypervisors, as it relys on vCenter to provision the VMs.  

#### Workload generator
HCIbench uses the vdbench workload generator.  Users can create the workload pattern via a web-form. e.g. 
<img src="https://github.com/garyjlittle/images/blob/master/HCIbench-vdbench-parameter-page.png" width="300" height="600">
#### Provisioning multiple worker VMs
HCIbench does a good job or provisioning multiple VMs to generate work across the cluster.  All VMs must be uniform.
<img src="https://github.com/garyjlittle/images/blob/master/HCIbench-deploying-VMs.png">
#### Multiple, distinct workloads
Currently all worker VMs must run the same workload.  This makes it impossible to run noisy neighbor tests where VMs on the same cluster have diferent characteristics.  e.g. VDI users (bootstorm) on an OLTP database, or a DSS workload running a large querey on the same cluster as a latency sensitive EMR workload.
<img src="https://github.com/garyjlittle/images/blob/master/HCIbench-select-vdbench-parameter-file.png">
#### Failure modes
HCIbench does not support failing hardware during test.
#### Scenarios
HCIbench does not support scenarios

### X-Ray
X-Ray is a tool from Nutanix that can run on multiple hypervisors and virtualization platforms
* Nutanix (AHV or ESXi) with vCenter or PRISM
* vmware VSAN on ESXi with vCenter
* Cisco Hyperflex with vCenter
* Microsoft Hyper-V on Storage spaces direct with SCVMM

#### Workload generator
X-Ray uses the fio workload generator.  Users can specify the workload pattern using standard fio configuration files, and yaml to describe how to place VMs within the cluster.  
#### Provisioning multiple worker VMs
X-Ray does a good job of provisioning worker VMs.  Each VM can have a different layout, e.g, number and size of disks, CPU, memory allocation.

<img src="https://github.com/garyjlittle/images/blob/master/X-Ray%20Distinct%20vm%20configuration%20.png" width="200" height="300">

#### Multiple distinct workloads
It is possible to have different workloads running concurrently.  e.g. it's possible to run VDI users on 3 nodes, and an OLTP workload on another, or an OLTP on two nodes and DSS workloads on four nodes.  Currently the fio and YAML files must be edited outside the web browser.

<img src="https://github.com/garyjlittle/images/blob/master/X-Ray-Distinct-workload-configuration-yaml.png">

#### Failure modes
X-Ray can be configured to connect to the out-of-band host management infrastructure so that power-failure test can be executed while the cluster is under load.
#### Scenarios
One way that X-Ray is different than most workload generators, even with orchestration such as HCIBench is that X-Ray fundamentally uses the idea of a test scenario, which comprises fo _workloads_ and _workflows_ .  The _workload_ is typlically one or more VMs running various IO mixes to simulate application work.  The _workflow_ is something like VDI Bootstorm - which means X-Ray will simultaneously boot all the VDI VMs (which could be serveral hundred).  Or it could be simulating a failure, or an rolling-upgrade of the cluster.

#### Installing and running X-ray 
See [here](hci-benchmarks-xray.md)

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
