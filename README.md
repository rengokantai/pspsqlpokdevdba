#### pspsqlpokdevdba
#####opti
######install postgre
```
apt-get install postgresql postgresql-client postgresql-contrib
su postgres
```

#####locking down
######setting account
```
adduser yd
adduser boss
su yd
cd
mkdir .ssh
touch .ssh/authorized_keys
```
open local machine
```
cat ~/.ssh/id_rsa.pub
```
######Remote access
```
vim /etc/postgresql/9.3/main/pg_hba.conf
```
edit
```
```

######setting up superuser
```
su postgres
psql
```
```
create role yd with SUPERUSER CREATEDB CREATEROLE LOGIN password 'pass';
\q
```
```
su yd -
createdb yd
psql
create role test with password 'pass';
alter databasae yd owner to test;
\q
```

#####back up and restore
######moving data
<This command will not be recorded>
```
pg_dump -d yd -f yd.sql
```
import
```
psql yd < yd.sql
```
######backup
```
pg_dump -d yd | gzip > yd.gz //Or
pg_dump -Fc yd >yd.bk
pg_restore -d yd yd.bk
```
######simple cron
```
cd
mkdir backup
mkdir original
mv yd.sql original
rm yd.bk
crontab -e 
```
edit
```
0 0 * * * yd pg_dump -Fc yd > backup/yd.bk
```
#####Understanding 
######using pgbench
```
createdb bench
pgbench -i bench
pgbench bench
```
scale test
```
pgbench -i -s 100 tbname(bench)
pgbench -T 120 -j 2 -c 2 -S bench
psql yd -c "show config_file"
psql yd -c "show max_connections"
psql yd -c "show shared_buffers"
```

#####Tuning server
######config tweaks
```
vim /etc/postgresql/9.3/main/postgresql.conf
```
edit  
add
```
include postgresql.local.conf
```
add file:
```
vim /etc/postgresql/9.3/main/postgresql.local.conf
```
using [pgtune](http://pgtune.leopard.in.ua/) to test.
For 9.5,copy
```
max_connections = 200
shared_buffers = 128MB
effective_cache_size = 384MB
work_mem = 655kB
maintenance_work_mem = 32MB
checkpoint_segments = 32
checkpoint_completion_target = 0.7
wal_buffers = 3932kB
default_statistics_target = 100
```
open 
```
vim /etc/postgresql/9.3/main/postgresql.local.conf
```
use
```
:set paste
```
Note only first 5 criteria are critical..

######Lets Psql tell us
######pg_stat_statements
```
psql
create extension pg_stat_statements;
select * from pg_stat_statements;  //this will arise a error
show config_file;
```
To fix
```
vim /etc/postgresql/9.3/main/postgresql.local.conf
```
edit
```
shared_preload_libraries ='pg_stat_statements'
```
restart
```
service postgresql restart
```
Then
```
psql
select pg_stat_reset();
```
######Cache usage
```
select sum(heap_blks_read) as hbr, sum(heap_blks_hit)as hh, sum(heap_blks_hit)/(sum(heap_blks_hit)+sum(heap_blks_read))as ratio from pg_statio_user_tables;
```
