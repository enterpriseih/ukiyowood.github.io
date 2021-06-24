# mysql配置

```
[mysqld]
datadir=/var/lib/mysql
basedir=/data/adam-saas-5.0.1/modules/mysql-onclick-installer/mysql
port=3306
user=mysql
character-set-server=utf8mb4
# Disabling symbolic-links is recommended to prevent assorted security risks
symbolic-links=0
innodb_log_file_size=4m
lc-messages-dir=/data/adam-saas-5.0.1/modules/mysql-onclick-installer/mysql/share
lc-messages=en_US

log-error=/var/log/mysqld.log
pid-file=/var/run/mysqld/mysqld.pid
# adam config
ssl=0
sql_mode='STRICT_TRANS_TABLES,ERROR_FOR_DIVISION_BY_ZERO,NO_AUTO_CREATE_USER,NO_ENGINE_SUBSTITUTION'
innodb_large_prefix=on
group_concat_max_len=18446744073
innodb_default_row_format= DYNAMIC
max_allowed_packet=1G
max_binlog_cache_size=1G
optimizer_switch = 'derived_merge=off'

max_connections=99999
max_error_count=88888
character-set-server=utf8mb4
lower_case_table_names=1
max_connections=9999
max_user_connections=8888
wait_timeout=31536000
interactive_timeout=31536000
innodb_buffer_pool_size=8192M
explicit_defaults_for_timestamp=0
max_allowed_packet=100M

```