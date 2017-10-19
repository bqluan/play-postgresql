### 01 - Install PostgreSQL ###
分别在主库和从库上安装PostgreSQL，安装完后还要安装postgresql96-contrib，以便可以安装citext扩展。
```
yum install postgresql96-contrib
```

### 02 - Configure stream replication on master ###
参考[这里](https://wiki.postgresql.org/wiki/Streaming_Replication)在主库上配置。
配置示例在***02 - postgresql-master.conf***里。

```
CREATE ROLE replica WITH REPLICATION PASSWORD 'abcdefg' LOGIN;

vi pg_hba.conf
host    replication     replica        172.18.12.14/24           md5

iptables -L --line-numbers
iptables -I INPUT 4 -p tcp --dport 5432 -s 172.18.12.14 -j ACCEPT
service iptables save
```

### 03 - Configure stream replication on slave ###
将主库上的数据复制到从库。
```
service postgresql-9.6 stop
su postgres
cd /var/lib/pgsql/9.6
mv data data-bak
mkdir data
chmod 700 data
pg_basebackup -h 172.18.12.13 -U replica -D /var/lib/pgsql/9.6/data -P --xlog
```

参考[这里](https://wiki.postgresql.org/wiki/Streaming_Replication)在从库上配置。
配置示例在***03 - postgresql-slave.conf***和***03 - recovery-slave.conf***里。

在主库和从库的数据库服务启动后使用下面的命令查看同步状态：
```
ps -ef | grep postgres
```

### 04 - Create database on master ###
```
su postgres
psql
create user appuser login createdb password 'abcdefg';
create database appdb with owner=appuser encoding=UTF8 lc_collate='zh_CN.utf-8' lc_ctype='zh_CN.utf-8' template=template0;
create extension citext;
\q
```
