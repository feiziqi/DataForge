# SQL

structured query language：DDL(定义)、DML(操作)、DQL(查询)、DCL(控制)

**DDL**

DDL：create增、drop删、alter改、desc和show查

**DML和DQL**

DML：insert增、delete删、updata改，DQL：select查



# 第1章 Hive基本概念

# 第2章 Hive安装

# 第3章 Hive数据类型

## 3.1 基本数据类型

| Hive      | MySQL     | JAVA    | 长度                                                 | 例子                                |
| --------- | --------- | ------- | ---------------------------------------------------- | ----------------------------------- |
| TINYINT   | TINYINT   | byte    | 1byte有符号整数                                      | 2                                   |
| SMALINT   | SMALINT   | short   | 2byte有符号整数                                      | 20                                  |
| INT       | INT       | int     | 4byte有符号整数                                      | 20                                  |
| BIGINT    | BIGINT    | long    | 8byte有符号整数                                      | 20                                  |
| BOOLEAN   | 无        | boolean | 布尔类型，true或者false                              | TRUE  FALSE                         |
| FLOAT     | FLOAT     | float   | 单精度浮点数                                         | 3.14159                             |
| DOUBLE    | DOUBLE    | double  | 双精度浮点数                                         | 3.14159                             |
| STRING    | VARCHAR   | string  | 字符系列。可以指定字符集。可以使用单引号或者双引号。 | ‘now is the time’“for all good men” |
| TIMESTAMP | TIMESTAMP | ——      | 时间类型                                             | ——                                  |
| BINARY    | BINARY    | ——      | 字节数组                                             | ——                                  |

对于Hive的String类型相当于数据库的varchar类型，该类型是一个可变的字符串，不过它不能声明其中最多能存储多少个字符，理论上它可以存储2GB的字符数。

## 3.2 集合数据类型

| 数据类型 | 描述                                                         | 语法示例                                       |
| -------- | :----------------------------------------------------------- | ---------------------------------------------- |
| STRUCT   | 和c语言中的struct类似，都可以通过“点”符号访问元素内容。例如，如果某个列的数据类型是STRUCT{first STRING, last STRING}，那么第1个元素可以通过字段.first来引用。 | struct()例如struct<street:string, city:string> |
| MAP      | MAP是一组键-值对元组集合，使用数组表示法可以访问数据。例如，如果某个列的数据类型是MAP，其中键->值对是’first’->’John’和’last’->’Doe’，那么可以通过字段名[‘last’]获取最后一个元素 | map()例如map<string, int>                      |
| ARRAY    | 数组是一组具有相同类型和名称的变量的集合。这些变量称为数组的元素，每个数组元素都有一个编号，编号从零开始。例如，数组值为[‘John’, ‘Doe’]，那么第2个元素可以通过数组名[1]进行引用。 | Array()例如array\<string>                      |

Hive有三种复杂数据类型ARRAY、MAP和STRUCT。ARRAY和MAP与Java中的Array和Map类似，而STRUCT与C语言中的Struct类似，它封装了一个命名字段集合，复杂数据类型允许任意层次的嵌套。

**1.struct(对象)**

和c语言中的struct类似，都可以通过“点”符号访问元素内容。例如，如果某个列的数据类型是STRUCT{first STRING, last STRING}，那么第1个元素可以通过字段.first来引用。

```hive
--语法
STRUCT<field1_name field1_type, field2_name field2_type, ..., fieldn_name fieldn_type>
CREATE TABLE example_table (
  id INT,
  employee_info STRUCT<name:STRING, age:INT, department:STRING>
);
--常用操作
--插入数据
INSERT INTO example_table VALUES (1, named_struct('name', 'Alice', 'age', 30, 'department', 'HR'));
--访问struct里面的字段
SELECT employee_info.name FROM example_table;


```

**2.map**

MAP是一组键-值对元组集合，使用数组表示法可以访问数据。例如，如果某个列的数据类型是MAP，其中键->值对是’first’->’John’和’last’->’Doe’，那么可以通过字段名[‘last’]获取最后一个元素

```hive
--语法
MAP<key_type, value_type>
CREATE TABLE example_table (
  id INT,
  properties MAP<STRING, STRING>
);
--常用操作
--插入数据
INSERT INTO example_table VALUES (1, map('color', 'blue', 'size', 'M', 'brand', 'Nike'));
--访问MAP中的值
SELECT properties['color'] FROM example_table;
--MAP函数
SELECT size(properties) FROM example_table;--返回Map中每一行键值对的数量
SELECT map_keys(properties) FROM example_table;--返回MAP中所有键的列表
SELECT map_values(properties) FROM example_table;--返回map中所有值的列表


```

**3.array**

数组是一组具有相同类型和名称的变量的集合。这些变量称为数组的元素，每个数组元素都有一个编号，编号从零开始。例如，数组值为[‘John’, ‘Doe’]，那么第2个元素可以通过数组名[1]进行引用。

```hive
--语法
ARRAY<data_type>
CREATE TABLE example_table (
  id INT,
  names ARRAY<STRING>
);
--常用操作
--插入数据
INSERT INTO example_table VALUES (1, array('Alice', 'Bob', 'Charlie'));
--访问数组元素
SELECT names[0] FROM example_table;
--数组函数
SELECT size(names) FROM example_table;--返回数组大小
SELECT array_contains(names, 'Bob') FROM example_table;--检查数组中是否包含指定的元素
SELECT concat(names, array('Dave', 'Eve')) FROM example_table;--合并两个数组
--迭代数组
SELECT id, name
FROM example_table
LATERAL VIEW explode(names) t AS name;


```

## 3.3 类型转换

# 第4章 DDL数据定义

**增删查改**

## 4.1 创建数据库

```hive
CREATE DATABASE [IF NOT EXISTS] database_name
[COMMENT database_comment]
[LOCATION hdfs_path]
[WITH DBPROPERTIES (property_name=property_value, ...)];
```

## 4.2 查询数据库

```hive
1 SHOW DATABASES;--查询所有数据库
2 DESCRIBE DATABASE database_name;--查询具体数据库的详细
```

## 4.3 修改数据库

```hive
ALTER DATABASE database_name SET DBPROPERTIES ('key'='value');
```

## 4.4 删除数据库

```hive
DROP DATABASE [IF EXISTS] database_name [RESTRICT | CASCADE];
```





## 4.5 创建表

```hive
CREATE [EXTERNAL] TABLE [IF NOT EXISTS] table_name 
[(col_name data_type [COMMENT col_comment], ...)] 
[COMMENT table_comment] 
[PARTITIONED BY (col_name data_type [COMMENT col_comment], ...)] 
[CLUSTERED BY (col_name, col_name, ...) 
[SORTED BY (col_name [ASC|DESC], ...)] INTO num_buckets BUCKETS] 
[ROW FORMAT row_format] 
[STORED AS file_format] 
[LOCATION hdfs_path]
[TBLPROPERTIES (property_name=property_value, ...)]
[AS select_statement]
[LIKE table_name]
```

## 4.6 修改表

```hive
--1.重写命名
ALTER TABLE table_name RENAME TO new_table_name
--2.更新列
ALTER TABLE table_name CHANGE [COLUMN] col_old_name col_new_name column_type [COMMENT col_comment]
--3.增加和替换列
ALTER TABLE table_name ADD|REPLACE COLUMNS (col_name data_type [COMMENT col_comment], ...) 
```

## 4.7 删除表

```hive
DROP TABLE [IF EXISTS] table_name;
--注意：Truncate只能删除管理表，不能删除外部表中数据
TRUNCATE TABLE table_name;
```

## 4.8 查询表

```hive
--查询所有表
SHOW TABLES [LIKE 'pattern'];
--查询表结构
DESCRIBE [FORMATTED][PARTITIONS] table_name;
```



# 第5章 DML数据操作

insert增、delete删、updata改

## 5.1 数据导入

LOAD DATA 是 Hive 中最常用的加载数据的方法之一，它将本地文件系统或 HDFS 上的数据加载到 Hive 表中。

```hive
LOAD DATA [LOCAL] INPATH 'data_source_path' [OVERWRITE] INTO TABLE table_name;
--LOCAL：如果数据在本地文件系统中，使用 LOCAL 关键字；如果数据在 HDFS 上，则不需要 LOCAL。
--INPATH 'data_source_path'：指定数据的路径，可以是本地路径或 HDFS 路径。
--OVERWRITE：如果指定了 OVERWRITE，会清空目标表中的现有数据并将新数据加载进去。如果不指定，数据会追加到目标表中。
--INTO TABLE table_name：指定要加载数据的目标表。
```

## 5.2 数据导出

```hive
--导出到本地系统
INSERT OVERWRITE LOCAL DIRECTORY 'local_directory_path' 
ROW FORMAT DELIMITED
FIELDS TERMINATED BY ',' 
SELECT * FROM table_name;
--导出到HDFS系统
INSERT OVERWRITE DIRECTORY 'hdfs_directory_path' 
ROW FORMAT DELIMITED
FIELDS TERMINATED BY ',' 
SELECT * FROM table_name;
```



## 5.3 insert增

```hive
--插入单行数据
INSERT INTO TABLE table_name VALUES (value1, value2, ...);
--从查询结果插入结果
INSERT OVERWRITE TABLE table_name SELECT ... FROM source_table WHERE condition;
--插入分区表
INSERT INTO TABLE partitioned_table PARTITION (partition_column='value') SELECT ... FROM source_table;
```

## 5.4 delete删

```hive
DELETE FROM table_name WHERE condition;
```

## 5.5 update改

```hive
UPDATE table_name SET column1 = value1, column2 = value2 WHERE condition;
```

# 第6章 DQL数据查询

## 6.1 基本查询(select  ... from)

```hive
SELECT column1, column2, ... 
FROM table_name [WHERE condition] [LIMIT n];
```

## 6.2 分组查询(group by)

```hive
SELECT column1, AGGREGATE_FUNCTION(column2) 
FROM table_name 
WHERE condition 
GROUP BY column1;
```

## 6.3 连接查询(join,union)

**JOIN 横向连接**

JOIN 用于连接两个或多个表的数据。通过指定连接条件（通常是通过共享的列），JOIN 可以将相关的数据行合并在一起。Hive 支持多种类型的连接，常见的包括 INNER JOIN、LEFT JOIN、RIGHT JOIN 和 FULL JOIN。

```hive
SELECT column1, column2, ...
FROM table1 
JOIN table2 ON table1.column = table2.column;
--inner join, left join, right join, full join
```

**UNION 纵向连接**

UNION 操作符用于将两个或多个查询的结果集合并成一个结果集。UNION 会去除重复的记录，仅返回唯一的结果。如果你希望保留所有的记录，包括重复的，可以使用 UNION ALL

```hive
SELECT column1, column2, ...
FROM table1
UNION [ALL]
SELECT column1, column2, ...
FROM table2;
--UNION 会将两个查询结果中的所有行合并，并去除重复的行（即，只保留唯一的行）。
--UNION ALL 则会保留所有行，包括重复的行。
```



## 6.4 排序查询

1.全局排序(order by)

```hive
SELECT column1, column2, ... 
FROM table_name 
ORDER BY column1 [ASC|DESC]; --默认asc
```

2.局部排序(sort by)

对于大规模的数据集order by的效率非常低。在很多情况下，并不需要全局排序，此时可以使用sort by局部内部排序。sort by为每个reducer产生一个排序文件。每个reducer内部进行排序，对全局结果集来说不是排序。

```hive
--hive (default)> set mapreduce.job.reduces=3;设置reduce个数
SELECT column1, column2, ...
FROM table_name
SORT BY column1 [ASC|DESC];
--distribute by用于决定数据如何分发到不同的 reducer
--hive要求distribute by语句要写在sort by语句之前
SELECT column1, column2, ...
FROM table_name
DISTRIBUTE BY column1
SORT BY column2 [ASC|DESC];
--当distribute by和sort by字段相同时，可以使用cluster by方式
SELECT column1, column2, ...
FROM table_name
CLUSTER BY column1 [ASC|DESC];
--CLUSTER BY column1等价于DISTRIBUTE BY column1 SORT BY column1
```



# 第7章 分区表和分桶表

## 7.1 分区表

```hive
CREATE TABLE table_name (
    column1 datatype,
    column2 datatype,
    ...
) PARTITIONED BY (partition_column datatype);
```



**动态分区调整**

动态分区是指在插入数据时，分区的值不需要预先指定，而是根据数据动态生成分区。动态分区的优势在于可以根据数据自动创建分区，减少手动维护分区的复杂度，尤其在分区数量较多的场景中非常有用。

```hive
--静态插入
INSERT INTO TABLE partition_table PARTITION (partition_column1=value1, partition_column2=value2)
SELECT column1, column2, ...
FROM source_table;

--启动动态分区和非严格模式
SET hive.exec.dynamic.partition = true;
SET hive.exec.dynamic.partition.mode = nonstrict;
SET hive.exec.max.dynamic.partitions = 1000;
SET hive.exec.max.dynamic.partitions.pernode = 100;
--动态插入
INSERT INTO TABLE partition_table PARTITION (partition_column1, partition_column2, ...)
SELECT column1, column2, ..., partition_column1, partition_column2
FROM source_table;
```



## 7.2 分桶表

**分桶表**

分桶表是对数据进一步细化的一种方式，它在分区的基础上，将数据按某个列的哈希值分成固定数量的桶（文件）。每个桶存储的数据是独立的。

```hive
CREATE TABLE table_name (
    column1 datatype,
    column2 datatype,
    ...
) CLUSTERED BY (bucket_column) INTO num_buckets BUCKETS;
```



# 第8章 函数

## 8.1 系统内置函数

**空字段赋值nvl**

给值为NULL的数据赋值，它的格式是NVL（value，default_value）。它的功能是如果value为NULL，则NVL函数返回default_value的值，否则返回value的值，如果两个参数都为NULL，则返回NULL。

**case when then when then else end**

```hive
CASE
  WHEN condition1 THEN result1
  WHEN condition2 THEN result2
  ELSE default_result
END
```



**行转列**



**列转行**



**窗口函数**

窗口函数（Window Functions）允许你在结果集的每一行上执行计算，并且可以基于某些列进行排序、分组等操作。窗口函数与传统的聚合函数不同，后者会将数据压缩为一行，而窗口函数不会丢失任何行数据。通过使用窗口函数，我们可以轻松实现排序、排名、累计计算、滚动统计等分析功能。

```hive
--基本语法
function_name (expression) 
OVER ([PARTITION BY partition_columns]
      [ORDER BY order_columns]
      [ROWS/RANGE BETWEEN ...])
--function_name: 窗口函数的名称，如 ROW_NUMBER(), RANK(), SUM(), AVG() 等。
--expression: 计算的目标字段或表达式。
--PARTITION BY: 用于将数据划分为多个分区，相同的分区会进行相同的计算。
--ORDER BY: 用于定义窗口中的排序顺序。
--ROWS 或 RANGE: 定义窗口的大小和范围，控制计算的上下文。
```





**rank**





## 8.2 自定义函数

**自定义函数分类**

**UDF（User-Defined-Function）**：一进一出
**UDAF（User-Defined Aggregation Function）**：用户自定义聚合函数，多进一出。类似于：count/max/min
**UDTF（User-Defined Table-Generating Functions）**：用户自定义表生成函数，一进多出。如lateral view explode()



# 第9章 压缩和存储

## 9.1 Hadoop压缩配置



## 9.2 开启Map输出阶段压缩

## 9.3 开启Reduce输出阶段压缩

## 9.4 文件存储格式

hive支持的存储数据的格式主要有：textfile,sequencefile,orc,parquet

**列式存储和行式存储**

行式存储将整行数据存储在一起，适合事务处理（OLTP）；列式存储将每列数据存储在一起，适合分析处理（OLAP）。

josn,textfile和sequencefile是基于行式存储

orc和parquet是基于列式存储

```hive
--行式存储
Row 1: 1, Laptop, 1000, 2025-01-01
Row 2: 2, Phone, 500, 2025-01-02
--列式存储
Column 1: 1, 2
Column 2: Laptop, Phone
Column 3: 1000, 500
Column 4: 2025-01-01, 2025-01-02
```

HDFS files→InputFileFormat→<key,value>→Deserializer→Row object

Row object→Serializer→<key,value>→OutputFileFormat→HDFS files



## 9.5 存储和压缩结合

# 第10章 企业级调优

# 第11章 Hive实战











