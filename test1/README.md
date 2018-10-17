
## 第一个sql
```sql
SELECT d.department_name,count(e.job_id)as "部门总人数",
avg(e.salary)as "平均工资"
from hr.departments d,hr.employees e
where d.department_id = e.department_id
and d.department_name in ('IT','Sales')
GROUP BY department_name;
```
- 第一个sql的执行计划
------------------------------------------------------------------------------------------------
| Id  | Operation                     | Name              | Rows  | Bytes | Cost (%CPU)| Time     |
|-----|-------------------------------|-------------------|-------|-------|------------|----------|
|   0 | SELECT STATEMENT              |                   |     2 |    46 |     2   (0)| 00:00:01 |
|   1 |  SORT GROUP BY NOSORT         |                   |     2 |    46 |     2   (0)| 00:00:01 |
|   2 |   NESTED LOOPS                |                   |    19 |   437 |     2   (0)| 00:00:01 |
|   3 |    NESTED LOOPS               |                   |    20 |   437 |     2   (0)| 00:00:01 |
|   4 |     INLIST ITERATOR           |                   |       |       |            |          |
|*  5 |      INDEX RANGE SCAN         | IDX$$_00320001    |     2 |    32 |     1   (0)| 00:00:01 |
|*  6 |     INDEX RANGE SCAN          | EMP_DEPARTMENT_IX |    10 |       |     0   (0)| 00:00:01 |
|   7 |    TABLE ACCESS BY INDEX ROWID| EMPLOYEES         |    10 |    70 |     1   (0)| 00:00:01 |
-------------------------------------------------------------------------------------------------------------



## 第二个sql
```sql
SELECT d.department_name,count(e.job_id)as "部门总人数",
avg(e.salary)as "平均工资"
FROM hr.departments d,hr.employees e
WHERE d.department_id = e.department_id
GROUP BY department_name
HAVING d.department_name in ('IT','Sales');
```
- 第二个sql的执行计划
---------------------------------------------------------------------------------------
| Id  | Operation            | Name           | Rows  | Bytes | Cost (%CPU)| Time     |
|-----|----------------------|----------------|-------|-------|------------|----------|
|   0 | SELECT STATEMENT     |                |     1 |    23 |     5  (20)| 00:00:01 |
|*  1 |  FILTER              |                |       |       |            |          |
|   2 |   HASH GROUP BY      |                |     1 |    23 |     5  (20)| 00:00:01 |
|*  3 |    HASH JOIN         |                |   106 |  2438 |     4   (0)| 00:00:01 |
|   4 |     INDEX FULL SCAN  | IDX$$_00320001 |    27 |   432 |     1   (0)| 00:00:01 |
|   5 |     TABLE ACCESS FULL| EMPLOYEES      |   107 |   749 |     3   (0)| 00:00:01 |
---------------------------------------------------------------------------------------

#### 对于两个sql，优化指导显示显示 ```There are no recommendations to improve the statement.```表示没有推荐的优化方法.
##  分析：
#### 对于第一个sql,计划显示操作数更多，但是总体读取行和字节更少。对于第二个sql，计划显示操作数更少，但是查询的行数和字符数目更多，所以第一个sql语句更加优秀。
