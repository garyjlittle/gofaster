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
### Simplest use case
* Use the -T <time> switch and select the benchmark schema to run against  
```
/usr/pgsql-11/bin/pgbench -T 60 pgbench-sf1
```
### 

  


