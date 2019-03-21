# Comparing HCIbench and X-Ray
### Simple IOPS tests

For simple IOPS based tests, where all VMs run the same workloads, it should be possible to compare the results of the two tools.  

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
|Ensure ASYNC_IO|  | ioengine=libaio |
|Size of Disk | size | size|

#### HCIbench result.
This chart generated from the HCIbench Excel sheet.  There are three individual runs, with the "Fill=Random" used to ensure data is actually written to the SSDs.  There is one outlier datapoint (blue line) but generally there is very little variation.

<img src="https://github.com/garyjlittle/images/blob/master/HCIbench-random-read.png">

#### X-Ray Result
This chart is generated automatically in X-Ray.

<img src="https://github.com/garyjlittle/images/blob/master/X-Ray-HCIbench-simulation.png">
