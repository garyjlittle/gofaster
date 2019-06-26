# Notes on using postgres and pgbench

# Installing pgbench
To run pgbench, we will first need a database to store the tables and queries in.  This does not require an entirely new postgres install, simply creating a new database 'schema'.  

Whenever I do postgres/pgbench testing I am usually trying to compare performance of the DB with different sizes which put different stresses on the underlying infrastructure. So I often create databases with different sizes (scale factor) so we can easily compare results. 

## Create and initialize databases
* Create three DBs of different sizes (scale-factors).  I'll call them pgbench-sf1, pgbench-sf10, pgbench-sf100.
```
/usr/pgsql-11/bin/createdb pgbench-sf1
/usr/pgsql-11/bin/createdb pgbench-sf10
/usr/pgsql-11/bin/createdb pgbench-sf100
```
* "pgbench -i. <name> -s <num>" means initialize a database schema with <name> and scale factor <num>
```
$ sudo -u postgres pgbench -i pgbench-sf1 -s 1
$ sudo -u postgres pgbench -i pgbench-sf10 -s 10 
$ sudo -u postgres pgbench -i pgbench-sf100 -s 100
```
## Run the benchmark
Normally I want run the benchmark for a specific period of time.
### Simple use cases.  In each case the final parameter is the schema to use
* Use the -T <time-seconds> switch
```
/usr/pgsql-11/bin/pgbench -T 60 pgbench-sf1
```
* Use the -P to show progress
```
sudo -u postgres pgbench -T 60 -P 5 pgbench-sf100
Password: 
starting vacuum...end.
progress: 5.0 s, 732.4 tps, lat 1.365 ms stddev 0.214
progress: 10.0 s, 757.4 tps, lat 1.320 ms stddev 0.152
progress: 15.0 s, 759.8 tps, lat 1.316 ms stddev 0.172
```
* Use the -j <jobs> to spin up multiple threads
```
 $ sudo -u postgres pgbench -T 60 -P 5 -j 16 pgbench-sf100
```
* Use the -c <clients> to use multiple connections. - This seems to drive concurrency. 
```
sudo -u postgres pgbench -T 60 -P 5 -j 16 -c 4 pgbench-sf100
```
  

### 

  


