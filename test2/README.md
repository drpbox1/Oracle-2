创建角色con_res_view16和用户new_user16，并授权和分配空间
 ```sql
 SQL>CREATE ROLE con_res_view16;
角色已创建。
 SQL> GRANT connect,resource,CREATE VIEW TO con_res_view16;
授权成功。
 CREATE USER new_user16 IDENTIFIED BY 123 DEFAULT TABLESPACE users TEMPORARY TABLESPACE temp;
 User NEW_USER16 已创建。
  ALTER USER new_user16 QUOTA 50M ON users;
 User NEW_USER16已变更。
GRANT con_res_view TO new_user16;
Grant 成功。
```

 新用户new_user16连接到pdborcl，创建表mytable和视图myview，插入数据，最后将myview的SELECT对象权限授予hr用户。
```sql
SQL> show user;
USER 为 "NEW_USER16"
SQL> CREATE TABLE mytable (id number,name varchar(50));
表已创建。
SQL> INSERT INTO mytable(id,name)VALUES(1,'zhang');
已创建 1 行。
SQL> INSERT INTO mytable(id,name)VALUES (2,'wang');
已创建 1 行。
SQL> CREATE VIEW myview AS SELECT name FROM mytable;
视图已创建。
SQL> SELECT * FROM myview;

NAME
--------------------------------------------------------------------------------
zhang
wang

```

hr查询new_user16授予它的视图myview
```sql
SQl>SELECT * FROM new_user16.myview;

NAME
--------------------------------------------------
zhang
wang
```

 查看数据库的使用情况
-----------------------------------------------------------------------
TABLESPACE_NAME | FILE_NAME | MB   |  MAX_MB | AUTOEXTEN
----------------|-----------|------|---------|-------------------------------------
USERS  |  /home/oracle/app/oracle/oradata/orcl/pdborcl/SAMPLE_SCHEMA_users01.dbf | 25 | 32767.9844 | YES



------------------------------------------------------------------
表空间名   |    大小MB   |   剩余MB   | 使用MB   | 使用率%
----------| -----------| ---------- |----------| ----------
SYSAUX	  |    640     |  40.4375   | 599.5625 |    93.68
USERS	  |   25       |  1.9375    | 23.0625  |   92.25
SYSTEM	  |	 270   |   1.3125   |268.6875  |   99.51
EXAMPLE   |  1281.875  |     62.25  | 1219.625 |    95.14
USERS03   |	1000   |      998   |       2  | .2
USERS02   |	1000   |    998	    |       2  | .2
