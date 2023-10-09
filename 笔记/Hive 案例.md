[Hive 开窗函数_hive开窗函数 王佩-CSDN博客](https://blog.csdn.net/wangpei1949/article/details/81437574)

### 开窗函数



#### 测试数据
```sql
-- 建表
create table student_scores(
id int,
studentId int,
language int,
math int,
english int,
classId string,
departmentId string
);
-- 写入数据
insert into table student_scores values 
  (1,111,68,69,90,'class1','department1'),
  (2,112,73,80,96,'class1','department1'),
  (3,113,90,74,75,'class1','department1'),
  (4,114,89,94,93,'class1','department1'),
  (5,115,99,93,89,'class1','department1'),
  (6,121,96,74,79,'class2','department1'),
  (7,122,89,86,85,'class2','department1'),
  (8,123,70,78,61,'class2','department1'),
  (9,124,76,70,76,'class2','department1'),
  (10,211,89,93,60,'class1','department2'),
  (11,212,76,83,75,'class1','department2'),
  (12,213,71,94,90,'class1','department2'),
  (13,214,94,94,66,'class1','department2'),
  (14,215,84,82,73,'class1','department2'),
  (15,216,85,74,93,'class1','department2'),
  (16,221,77,99,61,'class2','department2'),
  (17,222,80,78,96,'class2','department2'),
  (18,223,79,74,96,'class2','department2'),
  (19,224,75,80,78,'class2','department2'),
  (20,225,82,85,63,'class2','department2');
```

### 聚合开窗函数

#### count 开窗函数
```sql
select studentId,math,departmentId,classId,
-- 以符合条件的所有行作为窗口
count(math) over() as count1,
 -- 以按classId分组的所有行作为窗口
count(math) over(partition by classId) as count2,
 -- 以按classId分组、按math排序的所有行作为窗口
count(math) over(partition by classId order by math) as count3,
 -- 以按classId分组、按math排序、按 当前行+往前1行+往后2行的行作为窗口
count(math) over(partition by classId order by math rows between 1 preceding and 2 following) as count4
from student_scores where departmentId='department1';
```

#### sum 开窗函数

#### min 开窗函数

#### max 开窗函数

#### first_value 开窗函数

#### last_value 开窗函数

#### lag 开窗函数

#### lead 开窗函数

#### cume_dist 开窗函数

#### 排序开窗函数

#### dense_rank 开窗函数

#### ntile 开窗函数

#### row_number 开窗函数

#### percent_rank 开窗函数

