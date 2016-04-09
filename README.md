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
```
