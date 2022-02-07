# SQL Server 执行计划
SQL Server Management Studio 提供了查看图形化执行计划的简单方法，这里我们介绍一种通过命令查看的方法：

```Plain Text
SET STATISTICS PROFILE ON
```
以上命令可以打开 SQL Server 语句的分析功能，打开之后执行的语句会额外返回相应的执行计划：

```Plain Text
SELECT e.first_name,e.last_name,e.salary,d.department_name
  FROM employees e
  JOIN departments d ON (e.department_id = d.department_id)
 WHERE e.salary > 15000;

first_name|last_name|salary  |department_name|
----------|---------|--------|---------------|
Steven    |King     |24000.00|Executive      |
Neena     |Kochhar  |17000.00|Executive      |
Lex       |De Haan  |17000.00|Executive      |

Rows|Executes|StmtText                                                                                                                                                                                           |StmtId|NodeId|Parent|PhysicalOp          |LogicalOp           |Argument                                                                                                                                                           |DefinedValues                                                       |EstimateRows|EstimateIO  |EstimateCPU|AvgRowSize|TotalSubtreeCost|OutputList                                                            |Warnings|Type    |Parallel|EstimateExecutions|
----|--------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------|------|------|--------------------|--------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------|------------|------------|-----------|----------|----------------|----------------------------------------------------------------------|--------|--------|--------|------------------|
   3|       1|SELECT e.first_name,e.last_name,e.salary,d.department_name¶  FROM employees e¶  JOIN departments d ON (e.department_id = d.department_id)¶ WHERE e.salary > 15000                                  |     1|     1|     0|                    |                    |                                                                                                                                                                   |                                                                    |   2.9719627|            |           |          |     0.007803641|                                                                      |        |SELECT  |       0|                  |
   3|       1|  |--Nested Loops(Inner Join, OUTER REFERENCES:([e].[department_id]))                                                                                                                              |     1|     2|     1|Nested Loops        |Inner Join          |OUTER REFERENCES:([e].[department_id])                                                                                                                             |                                                                    |   2.9719627|           0|          0|        57|     0.007803641|[e].[first_name], [e].[last_name], [e].[salary], [d].[department_name]|        |PLAN_ROW|       0|                 1|
   3|       1|       |--Clustered Index Scan(OBJECT:([hrdb].[dbo].[employees].[emp_emp_id_pk] AS [e]), WHERE:([hrdb].[dbo].[employees].[salary] as [e].[salary]>(15000.00)))                                     |     1|     3|     2|Clustered Index Scan|Clustered Index Scan|OBJECT:([hrdb].[dbo].[employees].[emp_emp_id_pk] AS [e]), WHERE:([hrdb].[dbo].[employees].[salary] as [e].[salary]>(15000.00))                                     |[e].[first_name], [e].[last_name], [e].[salary], [e].[department_id]|           3|0.0038657407|   2.747E-4|        44|     0.004140441|[e].[first_name], [e].[last_name], [e].[salary], [e].[department_id]  |        |PLAN_ROW|       0|                 1|
   3|       3|       |--Clustered Index Seek(OBJECT:([hrdb].[dbo].[departments].[dept_id_pk] AS [d]), SEEK:([d].[department_id]=[hrdb].[dbo].[employees].[department_id] as [e].[department_id]) ORDERED FORWARD)|     1|     4|     2|Clustered Index Seek|Clustered Index Seek|OBJECT:([hrdb].[dbo].[departments].[dept_id_pk] AS [d]), SEEK:([d].[department_id]=[hrdb].[dbo].[employees].[department_id] as [e].[department_id]) ORDERED FORWARD|[d].[department_name]                                               |           1|    0.003125|   1.581E-4|        26|       0.0035993|[d].[department_name]                                                 |        |PLAN_ROW|       0|                 3|
```
> SQL Server 中的执行计划支持 SELECT、INSERT、UPDATE、DELETE 以及 EXECUTE 语句。  

SQL Server 执行计划各个步骤的执行顺序按照缩进来判断，缩进越多的越先执行，同样缩进的从上至下执行。接下来，我们需要理解执行计划中各种信息的含义：

* **Rows** 表示该步骤实际产生的记录数；
* **Executes** 表示该步骤实际被执行的次数；
* **StmtText** 包含了每个步骤的具体描述，也就是如何访问和过滤表的数据、如何实现表的连接、如何进行排序操作等；
* **StmtId**，该语句的编号；
* **NodeId**，当前操作步骤的节点号，不代表执行顺序；
* **Parent**，当前操作步骤的父节点，先执行子节点，再执行父节点；
* **PhysicalOp**，物理操作，例如连接操作的嵌套循环实现；
* **LogicalOp**，逻辑操作，例如内连接操作；
* **Argument**，操作使用的参数；
* **DefinedValues**，定义的变量值；
* **EstimateRows**，估计返回的行数；
* **EstimateIO**，估计的 IO 成本；
* **EstimateCPU**，估计的 CPU 成本；
* **AvgRowSize**，平均返回的行大小；
* **TotalSubtreeCost**，当前节点累计的成本；
* **OutputList**，当前节点输出的字段列表；
* **Warnings**，预估得到的警告信息；
* **Type**，当前操作步骤的类型；
* **Parallel**，是否并行执行；
* **EstimateExecutions**，该步骤预计被执行的次数；

对于上面的语句，节点执行的顺序为 3 -> 4 -> 2 -> 1。首先执行第 3 行，通过聚集索引（主键）扫描 employees 表加过滤的方式返回了 3 行数据，估计的行数（3.0841121673583984）与此非常接近；然后执行第 4 行，循环使用聚集索引的方式查找 departments 表，循环 3 次每次返回 1 行数据；第 2 行是它们的父节点，表示使用 Nested Loops 方式实现 Inner Join，Argument 列（OUTER REFERENCES:(\[e\].\[department\_id\])）说明驱动表为 employees ；第 1 行代表了整个查询，不执行实际操作。

最后，可以使用以下命令关闭语句的分析功能：

```Plain Text
SET STATISTICS PROFILE OFF
```
关于 SQL Server 执行计划和 SQL 调优，可以参考 SQL Server 官方文档[执行计划](https://link.zhihu.com/?target=https%3A//docs.microsoft.com/en-us/sql/relational-databases/performance/execution-plans%3Fview%3Dsql-server-ver15)。