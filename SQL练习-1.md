# SQL练习-1(刷leetcode笔记) 

标注：简单+中等

- [SQL练习-1(刷leetcode笔记)](#SQL%E7%BB%83%E4%B9%A0-1%E5%88%B7leetcode%E7%AC%94%E8%AE%B0)
  - [11 有趣的电影](#11-%E6%9C%89%E8%B6%A3%E7%9A%84%E7%94%B5%E5%BD%B1)
  - [12 交换工资](#12-%E4%BA%A4%E6%8D%A2%E5%B7%A5%E8%B5%84)
  - [13 分数排名](#13-%E5%88%86%E6%95%B0%E6%8E%92%E5%90%8D)
  - [14 连续出现的数字](#14-%E8%BF%9E%E7%BB%AD%E5%87%BA%E7%8E%B0%E7%9A%84%E6%95%B0%E5%AD%97)
  - [15 部门工资最高的员工](#15-%E9%83%A8%E9%97%A8%E5%B7%A5%E8%B5%84%E6%9C%80%E9%AB%98%E7%9A%84%E5%91%98%E5%B7%A5)
  - [16 交换座位](#16-%E4%BA%A4%E6%8D%A2%E5%BA%A7%E4%BD%8D)
  - [总结](#%E6%80%BB%E7%BB%93)


## 11 有趣的电影
题目：
```
某城市开了一家新的电影院，吸引了很多人过来看电影。该电影院特别注意用户体验，专门有个 LED显示板做电影推荐，上面公布着影评和相关电影描述。

作为该电影院的信息部主管，您需要编写一个 SQL查询，找出所有影片描述为非 boring (不无聊) 的并且 id 为奇数 的影片，结果请按等级 rating 排列。

 

例如，下表 cinema:

+---------+-----------+--------------+-----------+
|   id    | movie     |  description |  rating   |
+---------+-----------+--------------+-----------+
|   1     | War       |   great 3D   |   8.9     |
|   2     | Science   |   fiction    |   8.5     |
|   3     | irish     |   boring     |   6.2     |
|   4     | Ice song  |   Fantacy    |   8.6     |
|   5     | House card|   Interesting|   9.1     |
+---------+-----------+--------------+-----------+
对于上面的例子，则正确的输出是为：

+---------+-----------+--------------+-----------+
|   id    | movie     |  description |  rating   |
+---------+-----------+--------------+-----------+
|   5     | House card|   Interesting|   9.1     |
|   1     | War       |   great 3D   |   8.9     |
+---------+-----------+--------------+-----------+
```

分析：无

解答：
```sql
select 
    id,movie,description,rating
from
    cinema
where
    description!='boring' and id%2=1 -- mod(id,2)=1
order by
    rating desc
```

## 12 交换工资
```
给定一个 salary 表，如下所示，有 m = 男性 和 f = 女性 的值。交换所有的 f 和 m 值（例如，将所有 f 值更改为 m，反之亦然）。要求只使用一个更新（Update）语句，并且没有中间的临时表。

注意，您必只能写一个 Update 语句，请不要编写任何 Select 语句。

例如：

| id | name | sex | salary |
|----|------|-----|--------|
| 1  | A    | m   | 2500   |
| 2  | B    | f   | 1500   |
| 3  | C    | m   | 5500   |
| 4  | D    | f   | 500    |
运行你所编写的更新语句之后，将会得到以下表:

| id | name | sex | salary |
|----|------|-----|--------|
| 1  | A    | f   | 2500   |
| 2  | B    | m   | 1500   |
| 3  | C    | f   | 5500   |
| 4  | D    | m   | 500    |
```

分析：
- 只有两个值，并且交换，采用不使用第三个变量的方法进行交换值
- 采用if()函数即可


解答：
```sql
update salary
set sex=char(ascii('f')+ascii('m')-ascii(sex))

--方法二
update salary
set sex=if(sex='f','m','f')
```

## 13 分数排名
题目：
```
编写一个 SQL 查询来实现分数排名。如果两个分数相同，则两个分数排名（Rank）相同。请注意，平分后的下一个名次应该是下一个连续的整数值。换句话说，名次之间不应该有“间隔”。

+----+-------+
| Id | Score |
+----+-------+
| 1  | 3.50  |
| 2  | 3.65  |
| 3  | 4.00  |
| 4  | 3.85  |
| 5  | 4.00  |
| 6  | 3.65  |
+----+-------+
例如，根据上述给定的 Scores 表，你的查询应该返回（按分数从高到低排列）：

+-------+------+
| Score | Rank |
+-------+------+
| 4.00  | 1    |
| 4.00  | 1    |
| 3.85  | 2    |
| 3.65  | 3    |
| 3.65  | 3    |
| 3.50  | 4    |
+-------+------+
```

分析：
- 方法一：使用变量计数，当此值与上一个值相等时，curr不加1，反之加1
- 方法二：join，计算大于等于目前值的有多少个

解答：
```sql

select Score, @curr:=@curr+(@pre<>(@pre:=Score)) as Rank
from Scores,(select @curr:=0,@pre:=-1) t
order by Score desc;
   

select 
    a.Score as score,
    (select count(distinct b.Score) from Scores b where b.Score>=a.Score) as rank
from Scores a order by Score desc;
```



## 14 连续出现的数字
题目：
```
编写一个 SQL 查询，查找所有至少连续出现三次的数字。

+----+-----+
| Id | Num |
+----+-----+
| 1  |  1  |
| 2  |  1  |
| 3  |  1  |
| 4  |  2  |
| 5  |  1  |
| 6  |  2  |
| 7  |  2  |
+----+-----+
例如，给定上面的 Logs 表， 1 是唯一连续出现至少三次的数字。

+-----------------+
| ConsecutiveNums |
+-----------------+
| 1               |
+-----------------+
```

分析：
- 使用变量进行计数，保存上一个num
- 使用3个表join

注意：**在if中赋值似乎不行**

解答：
```sql
-- 方法一
select distinct tmp.last_var as ConsecutiveNums
from
    (select
        @counts:=if(@last_var<>Logs.num,1,@counts+1) cnt,
        @last_var:=Logs.num last_var
    from Logs,(select @counts:=0,@last_var:=null) t) tmp
where tmp.cnt>=3

-- 或者
select distinct tmp.num as ConsecutiveNums
from
    (select
        case (@counts:=if(@last_var!=Logs.num,1,@counts+1)) when 3 then num  end as num,
        @last_var:=Logs.num
    from Logs,(select @counts:=0,@last_var:=null) t) tmp
where tmp.num is not null

-- 方法二
select distinct a.num as ConsecutiveNums
from 
    Logs a1,
    Logs a2,
    Logs a3
where
    a1.id=a2.id-1
    and a2.id=a3.id-1
    and a1.num=a2.num
    and a2.num=a3.num

```

## 15 部门工资最高的员工
题目：
```
Employee 表包含所有员工信息，每个员工有其对应的 Id, salary 和 department Id。

+----+-------+--------+--------------+
| Id | Name  | Salary | DepartmentId |
+----+-------+--------+--------------+
| 1  | Joe   | 70000  | 1            |
| 2  | Henry | 80000  | 2            |
| 3  | Sam   | 60000  | 2            |
| 4  | Max   | 90000  | 1            |
+----+-------+--------+--------------+
Department 表包含公司所有部门的信息。

+----+----------+
| Id | Name     |
+----+----------+
| 1  | IT       |
| 2  | Sales    |
+----+----------+
编写一个 SQL 查询，找出每个部门工资最高的员工。例如，根据上述给定的表格，Max 在 IT 部门有最高工资，Henry 在 Sales 部门有最高工资。

+------------+----------+--------+
| Department | Employee | Salary |
+------------+----------+--------+
| IT         | Max      | 90000  |
| Sales      | Henry    | 80000  |
+------------+----------+--------+
```

分析：首先需要查得每个部门最高的salary，因此需要group by DepartmentId，然后再进行join

解答：
```sql
select 
    d.Name as Department,e.Name as Employee,e.Salary
from
    Employee e join Department d
    on e.DepartmentId=d.Id
where
    (DepartmentId,Salary) in
    (select DepartmentId,max(Salary) 
    from Employee
    group by DepartmentId
    )
```


## 16 交换座位
题目：
```
小美是一所中学的信息科技老师，她有一张 seat 座位表，平时用来储存学生名字和与他们相对应的座位 id。

其中纵列的 id 是连续递增的

小美想改变相邻俩学生的座位。

你能不能帮她写一个 SQL query 来输出小美想要的结果呢？

 

示例：

+---------+---------+
|    id   | student |
+---------+---------+
|    1    | Abbot   |
|    2    | Doris   |
|    3    | Emerson |
|    4    | Green   |
|    5    | Jeames  |
+---------+---------+
假如数据输入的是上表，则输出结果如下：

+---------+---------+
|    id   | student |
+---------+---------+
|    1    | Doris   |
|    2    | Abbot   |
|    3    | Green   |
|    4    | Emerson |
|    5    | Jeames  |
+---------+---------+
注意：

如果学生人数是奇数，则不需要改变最后一个同学的座位。
```

分析：使用case when else end和mod即可，交换name不如交换id

解答：
```sql
select (case  
    when mod(id,2)=1 and id=(select count(*) from seat) then id
    when mod(id,2)=1 then id+1
    else id-1
    end) as id,student
from 
    seat
order by id
```



## 总结
- ascill('x')将字符x转换为对应的ascill int值
- char(x)与上述相反
- if()函数像是三目运算符：IF(expr1,expr2,expr3)，如果expr1的值为true，则返回expr2的值，如果expr1的值为false，则返回expr3的值。
- 变量 `set @var=1`,`select @var:=1`，`declare var1 int default 0`,后者只能在存储过程中使用