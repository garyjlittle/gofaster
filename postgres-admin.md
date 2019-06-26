# Postgres admin tasks
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
