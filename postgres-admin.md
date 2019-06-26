# Postgres admin tasks
## Data locations
### Locations for specific databases
```
postgres=# select oid,datname from pg_database; 
  oid  |    datname     
-------+----------------
 12368 | postgres
 16384 | pgbench-sf1
     1 | template1
 12367 | template0
 16385 | pgbench-sf10
 16386 | pgbench-sf100
 16437 | pgbench-sf1000
 16454 | pgbench-sf500
 ```
 
## Passwords
### Change postgres password
* e.g. set password to 'pg'
```
$ psql -U postgres
alter user postgres with password 'pg';  
ALTER ROLE
postgres=# \q
```
* e.g. set password to NULL
Edit $PGBASE/data/pg_hba.conf to make local and 127.0.0.1 set to trust.
```
# "local" is for Unix domain socket connections only
local   all             all                                     trust
# IPv4 local connections:
host    all             all             127.0.0.1/32            trust
```
* restart postgres
```
$ psql -U postgres
alter user postgres with password '';  
ALTER ROLE
postgres=# \q
```

## Services
### Bitnami Debian
#### restart
```
# service bitnami restart
```
