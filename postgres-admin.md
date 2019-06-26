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
* e.g. 


## Services
### Bitnami Debian
#### restart
```
# service bitnami restart
```
