# Comparing HCIbench and X-Ray
### Simple IOPS tests

For simple IOPS based tests, where all VMs run the same workloads, it should be possible to compare the results of the two tools. Given an identical offered workload, e.g. same queue-depth, IO size and working-set-size  the results should be very similar.  The real result is that X-Ray/fio is slightly faster (10%) which I put down to the fact that the response times are quite low, and so the performance of the workload generator comes in to play.  The data suggests that for very low-latency workload generation fio is a bit quicker than vdbench.

The underlying IO generators (vdbench for HCIbench and fio for X-Ray) use different syntax, and terminology.  Use this handy lookup to decode what's what.

vdbench manual May 2018 (gated by Oracle login) : http://download.oracle.com/otn/utilities_drivers/vdbench/vdbench-50407.pdf
fui manual (many, e.g.) : https://media.readthedocs.org/pdf/fio/latest/fio.pdf


|Thing | vdbench | fio |
|------|---------|-----|
|IO size | xfersize|bs |
|Concurrency| threads | iodepth|
|Read/Write Mix | rdpct| rwmixread|
|Sequential/Random Mix | seekpct | percent_random|
|Ensure O_DIRECT| openflags=o_direct  | direct=1|
|Ensure ASYNC_IO| N/A | ioengine=libaio |
|Size of Disk | size | size|

#### HCIbench result.
This chart generated from the HCIbench Excel sheet.  There are three individual runs, with the "Fill=Random" used to ensure data is actually written to the SSDs.  There is one outlier datapoint (blue line) but generally there is very little variation.  The median figure is about 500,000 IOPS on a 4 node cluster with a queue depth of 64 per node.

<img src="https://github.com/garyjlittle/images/blob/master/HCIbench-random-read.png">

#### HCIbench vdbench configuration


#### X-Ray Result
This chart is generated automatically in X-Ray. The workload configuration is the same (the fio and vdbench files are shown below.  X-Ray generates a slightly higher IOP value (+10%) than HCIbench.  I expect that this is due to the slightly higher CPU usage demanded by vdbench compared to fio which is a bit more efficient.

<img src="https://github.com/garyjlittle/images/blob/master/X-Ray-HCIbench-randread-simulation.png">

#### HCIbench vdbench configuration
```
root@photon-HCIBench [ /opt/automation/vdbench-param-files ]# cat vdb-8vmdk-100ws-4k-100rdpct-100randompct-8threads 
*Auto Generated VDBench Parameter File
*8 raw disk, 100% random, 100% read
*SD:    Storage Definition
*WD:    Workload Definition
*RD:    Run Definition
debug=86
data_errors=10000
sd=sd1,lun=/dev/sda,openflags=o_direct,hitarea=0,range=(0,100),threads=8
sd=sd2,lun=/dev/sdb,openflags=o_direct,hitarea=0,range=(0,100),threads=8
sd=sd3,lun=/dev/sdc,openflags=o_direct,hitarea=0,range=(0,100),threads=8
sd=sd4,lun=/dev/sdd,openflags=o_direct,hitarea=0,range=(0,100),threads=8
sd=sd5,lun=/dev/sde,openflags=o_direct,hitarea=0,range=(0,100),threads=8
sd=sd6,lun=/dev/sdf,openflags=o_direct,hitarea=0,range=(0,100),threads=8
sd=sd7,lun=/dev/sdg,openflags=o_direct,hitarea=0,range=(0,100),threads=8
sd=sd8,lun=/dev/sdh,openflags=o_direct,hitarea=0,range=(0,100),threads=8
wd=wd1,sd=(sd1,sd2,sd3,sd4,sd5,sd6,sd7,sd8),xfersize=4k,rdpct=100,seekpct=100
rd=run1,wd=wd1,iorate=max,elapsed=300,warmup=60,interval=1
```


#### X-Ray fio configuration
GLittleDRM-MBP:HCIbench-clone gary.little$ cat random_read.fio 
```
[global]
ioengine=libaio
direct=1
time_based
norandommap
group_reporting
disk_util=0
continue_on_error=all
rate_process=poisson

[disk0]
bs=4k
size=2G
filename=/dev/sdb
rw=randread
iodepth=8

[disk1]
bs=4k
size=2G
filename=/dev/sdc
rw=randread
iodepth=8

[disk2]
bs=4k
size=2G
filename=/dev/sdd
rw=randread
iodepth=8

[disk3]
bs=4k
size=2G
filename=/dev/sde
rw=randread
iodepth=8

[disk4]
bs=4k
size=2G
filename=/dev/sdf
rw=randread
iodepth=8

[disk5]
bs=4k
size=2G
filename=/dev/sdg
rw=randread
iodepth=8

[disk6]
bs=4k
size=2G
filename=/dev/sdh
rw=randread
iodepth=8

[disk7]
bs=4k
size=2G
filename=/dev/sdi
rw=randread
iodepth=8
```
