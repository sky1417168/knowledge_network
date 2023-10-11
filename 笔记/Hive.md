## Hive安装配置

#### 安装环境
- [[Linux]] 
- [[Java]] 8
- [[Hadoop|Hadoop]] 3.1.3
- [[Mysql]]
- Hive 3.1.2

#### 安装步骤
- 安装JDK
- 安装Hadoop
- 安装Mysql
- 安装Hive

#### 命令行
```bash
# -e 执行语句
$ hive -e "select * from mytable" 
# -S 开启静默模式
$ hive -S -e "select * from mytable limit 3" > /tmp/myquery
# -f 执行文件中的语句
$ hive -f /path/to/file/withqueries.hql

# hive shell 中执行
hive> source /path/to/file/withqueries.hql
```

## 数据类型和文件格式
#### 常用数据类型
| 类型      | 大小/说明                     |
| --------- | ----------------------------- |
| INT       | 4个字节                       |
| BIGINT    | 8个字节                       |
| BOOLEAN   | TRUE/FALSE                    |
| FLOAT     | 4个字节                       |
| DOUBLE    | 8个字节                       |
| DECIMAL   | decimal(10,2)                 |
| TIMESTAMP | YYYY-MM-DD HH:MM:SS.fffffffff |
| DATE      | YYYY-MM-DD                    |
| STRING    |                               |
| ARRAY     | 数组                          |
| MAP       | 字典                          |
| STRUCTS   | 结构                          |
#### 文件格式
##### 文本格式
| 分隔符 | 作用                                  | 编码   |
| ------ | ------------------------------------- | ------ |
| \\n    | 分割行                                |        |
| \^A    | 分割列                                | \\u001 |
| \^B    | 分割ARRAY或者STRUCT,或者MAP键值对分割 | \\u002 |
| \^C    | 分割MAP键和值                         | \\u003 |
##### Parquet格式

## 数据定义

#### 数据库
```sql
create database if not exists mydb;
show databases like 'dmm.*';
drop database if exists mydb;
alter database mydb set dbproperties ('edited-by'='sky')
```

#### 数据表
```sql

```

## 数据操作

## 数据查询

## 视图

## 索引

## 模式设计

## 调优

## 函数

