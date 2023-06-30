# mysql
everything about mysql


# setup
```bash
# pull
$ docker pull mysql:5.7


# run
# $ docker run --platform linux/x86_64 \
# --name mysql_container \
# -e MYSQL_ROOT_PASSWORD=admin \
# -d -p 3307:3306 \
# -v /Users/runzhou/space/data/mysqlconf:/etc/mysql/conf.d \
# -v /Users/runzhou/space/data/mysqldata:/var/lib/mysql mysql:latest --explicit-defaults-for-timestamp=1


$ docker run \
--name mysql_container \
-e MYSQL_ROOT_PASSWORD=admin \
-d -p 3307:3306 \
-v /Users/runzhou/space/data/mysqlconf:/etc/mysql/conf.d \
-v /Users/runzhou/space/data/mysqldata:/var/lib/mysql mysql:latest

# connect
$ mysql -h127.0.0.1 -uroot -padmin --port=3307


# docker.cnf
[mysqld]
skip-host-cache
skip-name-resolve

# mysql.cnf
[mysql]

[mysqld]
max_connections=512
explicit_defaults_for_timestamp=0
lower_case_table_names=1

# mysqldump.cnf
[mysqldump]
quick
quote-names
max_allowed_packet = 16M

############################################ download mysql backup start ############################################
# (1) backup from devcos
$ docker exec 183bb56cdc56 /usr/bin/mysqldump -u root --password=admin test > backup.sql

# (2) download
$ scp -r runzhou@10.176.8.119:/x/home/runzhou/mysql_bkup/backup.sql /Users/runzhou/git/argus/gcp_migration/dev52-test-apps-argus/10.176.8.119_web

# (3) upload
$ scp -r /Users/runzhou/git/argus/gcp_migration/dev52-test-apps-argus/10.176.8.119_web/backup.sql runzhou@10.183.162.86:/home/runzhou/mysql_bkup
$ chwon root:root backup.sql

# (4) import data to dev52
# (4.1) create database;
$ mysql -h127.0.0.1 -uroot -padmin
# with differnt port
# https://dev.mysql.com/doc/refman/8.0/en/connecting.html

$ create database test;


# (4.2) import
# (4.2.1) import via docker cmd
$ docker exec -i a22012cd1da3 mysql -uroot -padmin test < /home/runzhou/mysql_bkup/backup.sql

# (4.2.2) import via mysql client
$ mysql -h127.0.0.1 -uroot -padmin test < /home/runzhou/mysql_bkup/backup.sql





# drop database
$ drop database test;

# into container
$ docker exec -it a22012cd1da3 bash


############################################ download mysql backup end ############################################

```

```sql
-- only mysql8+ supported
-- Recursive create https://dev.mysql.com/doc/refman/8.0/en/with.html#common-table-expressions-recursive
-- need execute below in non-strict mode
WITH RECURSIVE cte (n) AS
(
  SELECT 1
  UNION ALL
  SELECT n + 1 FROM cte WHERE n < 5
);

SELECT * FROM cte;

$ mysql -h127.0.0.1 --port=3307 -uroot -padmin -e "SELECT @@GLOBAL.sql_mode;"

-- default mode
-- ONLY_FULL_GROUP_BY,STRICT_TRANS_TABLES,NO_ZERO_IN_DATE,NO_ZERO_DATE,ERROR_FOR_DIVISION_BY_ZERO,NO_ENGINE_SUBSTITUTION

-- set as non-strict mode
$ mysql -h127.0.0.1 --port=3307 -uroot -padmin -e "SET GLOBAL sql_mode = ’NO_ENGINE_SUBSTITUTION’;"

-- reset
$ mysql -h127.0.0.1 --port=3307 -uroot -padmin -e "SET GLOBAL sql_mode = 'ONLY_FULL_GROUP_BY,STRICT_TRANS_TABLES,NO_ZERO_IN_DATE,NO_ZERO_DATE,ERROR_FOR_DIVISION_BY_ZERO,NO_ENGINE_SUBSTITUTION’;"

$ mysql -h127.0.0.1 -uroot -padmin --port=3307

```