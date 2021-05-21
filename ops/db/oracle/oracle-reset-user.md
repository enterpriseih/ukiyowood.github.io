# oracle如何重置一个数据库用户
```sql
alter user demo account lock;    # 锁定账号，避免数据库用户接受新的连接

SELECT 'alter system kill session '''||sid||','||serial#||''' IMMEDIATE;' FROM v$session s where s.username='demo';    # 拼接sql语句，杀掉已有的连接

# 执行拼接的语句
alter system kill session '5272,39559'  IMMEDIATE;
alter system kill session '5322,21503'  IMMEDIATE;

drop user demo cascade;    # 删除用户

create user demo identified by 1 default tablespace nnc_data01 temporary tablespace temp;
grant dba,connect to demo;
```