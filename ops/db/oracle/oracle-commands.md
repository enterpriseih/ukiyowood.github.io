# oracle日常操作手册
## 启停数据库实例
1. 登录数据库
```bash
su - oracle
sqlplus / as sysdba
```
2. 启动实例
```sql
startup
```
3. 启动和关闭监听
```bash
lsnrctl start
lsnrctl stop
```
4. 关闭实例
```sql
shutdown immediate;
```
> `shutdown`有4个参数`NORMAL`、`TRANSACTIONAL`、`IMMEDIATE`、`ABORT`。缺省不带任何参数时表示是`NORMAL`。

## 数据库用户
```sql
# 创建用户并赋权
create user HW1 identified by 1 default tablespace nnc_data01 temporary tablespace temp;
grant dba,connect to HW1;

# 修改用户的密码
alter user HW1 identified by 1;

# 删除用户
drop user HW1 cascade;

SELECT s.status, 'alter system kill session '''||sid||','||serial#||'''  IMMEDIATE;'  FROM v$session  s where s.username='FENKU_CORE_0126';

# 统计数据库用户所有表的大小
SELECT OWNER as "用户名", sum(BYTES) / 1024 / 1024 / 1024 as "所有表的大小(GB)"
  FROM DBA_SEGMENTS
 WHERE SEGMENT_NAME in (select t2.OBJECT_NAME
                          from dba_objects t2
                         where t2.OBJECT_TYPE = 'TABLE')
 group by OWNER order by 2 desc;
```

## 表空间
```sql
# 创建表空间
create tablespace NNC_DATA01 datafile '+DB/ORCL/DATAFILE/nnc_data01_01.dbf' size 30G autoextend off;
create tablespace NNC_INDEX01 datafile '+DB/ORCL/DATAFILE/nnc_index01_01.dbf' size 10G autoextend off;
# 扩增表空间
alter tablespace nnc_index01 add datafile 'E:\oradata\nnc_index01_100.dbf' size 32767M;
```
## 数据库备份
### 数据泵模式
```sql
create directory dump_dir as '/oracle/datadump/dumps';
create directory log_dir as '/oracle/datadump/logs';
select * from dba_directories;    # 查看当前所有的directory对象
grant read,write on directory dump_dir to HW1;
grant read,write on directory log_dir to HW1;

# 导出数据
expdp scott/passwd directory=backup dumpfile=alldb.dmp logfile=alldb.log schemas=scott PARALLEL=4

# 导入数据
impdp sswu/1 directory=sswu dumpfile=NCC.DMP remap_schema=NCC:SSWU PARALLEL=4
impdp sswu/1 directory=sswu dumpfile=NCC.DMP table_exists_action=truncate tables=scott.dept,scott.emp remap_schema=NCC:SSWU PARALLEL=4    # 导入指定表，并设置表存在的策略
```
> `table_exists_action`参数为在数据导入过程中表存在的策略设置
> | 值    | 解释    |
> | :-: | :-: |
> |  skip   |   如果已存在表，则跳过并处理下一个对象  |
> |   append  |   为表增加数据;  |
> |   truncate  |   截断表，然后为其增加新数据；  |
> |   replace  |  删除已存在表，重新建表并追加数据;   |

### 普通模式
```bash

```
## 数据库优化配置
### 连接数
```sql
select count(*) from v$session;    #查看当前连接数
show parameter processes;    # 查看oracle连接数设置
alter system set processes=5000 scope=spfile;    # 修改最大连接数

# 查看连接数和会话
show parameter processes;
show parameter sessions;

# 重启生效
shutdown immediate;
startup;
```

## rac集群操作
```bash
olsnodes -n    # 查看节点
oifcfg getif    # 查看网络信息
ocrcheck    # 查看OCR(oracle cluster registry)信息

crsctl stat res -t    # 使用grid用户登录，查看集群运行状态
crsctl check crs    # 检查OHAS状态和集群状态
crsctl start cluster -all    #启动所有节点集群服务
crsctl stop cluster -all     #关闭所有节点集群服务

# 查看数据库状态
srvctl  status database -d racncc
# 启动或关闭数据库实例
srvctl stop instance -d orcl -n rac208

srvctl status nodeapps -n rac208    # 查看节点应用状态
srvctl status scan    # 查看scanip状态
srvctl status vip -n rac208    # 查看节点vip的状态
srvctl status listener    # 查看监听器的状态
```