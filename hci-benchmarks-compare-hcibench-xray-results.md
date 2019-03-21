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
|Ensure O_DIRECT| openflags=o_direct  | direct=1|
|Ensure ASYNC_IO|  | ioengine=libaio |
