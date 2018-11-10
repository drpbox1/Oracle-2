### 分配分区权限

 ![image](https://github.com/1771190842/Oracle/blob/master/test3/3-1.PNG)
 
### 切换用户创建orders表：
```sql
SQL> CREATE TABLE orders 
(
 order_id NUMBER(10, 0) NOT NULL 
 , customer_name VARCHAR2(40 BYTE) NOT NULL 
 , customer_tel VARCHAR2(40 BYTE) NOT NULL 
 , order_date DATE NOT NULL 
 , employee_id NUMBER(6, 0) NOT NULL 
 , discount NUMBER(8, 2) DEFAULT 0 
 , trade_receivable NUMBER(8, 2) DEFAULT 0 
) 
TABLESPACE USERS 
PCTFREE 10 INITRANS 1 
STORAGE (   BUFFER_POOL DEFAULT ) 
NOCOMPRESS NOPARALLEL 
PARTITION BY RANGE (order_date) 
(
 PARTITION PARTITION_BEFORE_2016 VALUES LESS THAN (
 TO_DATE(' 2016-01-01 00:00:00', 'SYYYY-MM-DD HH24:MI:SS', 
 'NLS_CALENDAR=GREGORIAN')) 
 NOLOGGING 
 TABLESPACE USERS 
 PCTFREE 10 
 INITRANS 1 
 STORAGE 
( 
 INITIAL 8388608 
 NEXT 1048576 
 MINEXTENTS 1 
 MAXEXTENTS UNLIMITED 
 BUFFER_POOL DEFAULT 
) 
NOCOMPRESS NO INMEMORY  
, PARTITION PARTITION_BEFORE_2017 VALUES LESS THAN (
TO_DATE(' 2017-01-01 00:00:00', 'SYYYY-MM-DD HH24:MI:SS', 
'NLS_CALENDAR=GREGORIAN')) 
NOLOGGING 
TABLESPACE USERS02 
...
);
``` 
```
Table ORDERS 已创建
```
### 创建order_details表：
```sql
SQL> CREATE TABLE order_details 
(
id NUMBER(10, 0) NOT NULL 
, order_id NUMBER(10, 0) NOT NULL
, product_id VARCHAR2(40 BYTE) NOT NULL 
, product_num NUMBER(8, 2) NOT NULL 
, product_price NUMBER(8, 2) NOT NULL 
, CONSTRAINT order_details_fk1 FOREIGN KEY  (order_id)
REFERENCES orders  (  order_id   )
ENABLE 
) 
TABLESPACE USERS 
PCTFREE 10 INITRANS 1 
STORAGE (   BUFFER_POOL DEFAULT ) 
NOCOMPRESS NOPARALLEL
PARTITION BY REFERENCE (order_details_fk1)
(
PARTITION PARTITION_BEFORE_2016 
NOLOGGING 
TABLESPACE USERS --必须指定表空间,否则会将分区存储在用户的默认表空间中
...
) 
NOCOMPRESS NO INMEMORY, 
PARTITION PARTITION_BEFORE_2017 
NOLOGGING 
TABLESPACE USERS02
...
) 
NOCOMPRESS NO INMEMORY  
);
```
```
Table ORDER-DETAILS 已创建
```
### 切换管理元分配用户权限
```sql
grant select on v_$sesstat to new_user16;
Grant succeeded.
grant select on v_$statname to con_res_view16;
Grant succeeded.
grant select on v_$mystat to con_res_view16;
Grant succeeded.
grant con_res_view16 to dba with system option;
Grant succeeded.
grant con_res_view16 to new_user16;
Grant succeeded.
```
### 用户身份登陆插入1万行数据，并查询执行计划。

### 分析
```
表进行分区后，逻辑上表仍然是一张完整的表，只是将表中的数据在物理上存放到多个表空间，这样查询数据时，不至于每次都扫描整张表。提高了查询的速度。
对于需要存储大量数据的表来说，分区是很有必要的。分区是一种负载均衡的手段，提高了磁盘利用率，让零碎空间可以利用，让系统管理员更加方便表的管理。
很适合磁盘组的服务器数据存储方式，可以提高系统吞吐量。
```
