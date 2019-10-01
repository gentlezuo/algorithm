# SQL练习-0(刷leetcode笔记)  

标注：简单题

- [SQL练习-0(刷leetcode笔记)](#SQL%E7%BB%83%E4%B9%A0-0%E5%88%B7leetcode%E7%AC%94%E8%AE%B0)
  - [1 组合两个表](#1-%E7%BB%84%E5%90%88%E4%B8%A4%E4%B8%AA%E8%A1%A8)
  - [2 第二高的薪水](#2-%E7%AC%AC%E4%BA%8C%E9%AB%98%E7%9A%84%E8%96%AA%E6%B0%B4)
  - [3 收入超过经理的员工](#3-%E6%94%B6%E5%85%A5%E8%B6%85%E8%BF%87%E7%BB%8F%E7%90%86%E7%9A%84%E5%91%98%E5%B7%A5)
  - [4 查找重复的email](#4-%E6%9F%A5%E6%89%BE%E9%87%8D%E5%A4%8D%E7%9A%84email)
  - [5 从不订购的客户](#5-%E4%BB%8E%E4%B8%8D%E8%AE%A2%E8%B4%AD%E7%9A%84%E5%AE%A2%E6%88%B7)
  - [6 删除重复的email](#6-%E5%88%A0%E9%99%A4%E9%87%8D%E5%A4%8D%E7%9A%84email)
  - [7 上升的温度](#7-%E4%B8%8A%E5%8D%87%E7%9A%84%E6%B8%A9%E5%BA%A6)
  - [8 大的国家](#8-%E5%A4%A7%E7%9A%84%E5%9B%BD%E5%AE%B6)
  - [9 超过5名同学的课](#9-%E8%B6%85%E8%BF%875%E5%90%8D%E5%90%8C%E5%AD%A6%E7%9A%84%E8%AF%BE)
  - [总结](#%E6%80%BB%E7%BB%93)


## 1 组合两个表

题目：
```
表1: Person

+-------------+---------+
| 列名         | 类型     |
+-------------+---------+
| PersonId    | int     |
| FirstName   | varchar |
| LastName    | varchar |
+-------------+---------+
PersonId 是上表主键
表2: Address

+-------------+---------+
| 列名         | 类型    |
+-------------+---------+
| AddressId   | int     |
| PersonId    | int     |
| City        | varchar |
| State       | varchar |
+-------------+---------+
AddressId 是上表主键
 

编写一个 SQL 查询，满足条件：无论 person 是否有地址信息，都需要基于上述两表提供 person 的以下信息：

```

分析：两个表left join即可

解答：
```sql
select 
    p.FirstName, p.LastName, a.City ,a.State
from 
    Person as p
left join 
    Address as a
on 
    p.personId=a.PersonId
```

## 2 第二高的薪水

题目：
```
编写一个 SQL 查询，获取 Employee 表中第二高的薪水（Salary） 。

+----+--------+
| Id | Salary |
+----+--------+
| 1  | 100    |
| 2  | 200    |
| 3  | 300    |
+----+--------+
例如上述 Employee 表，SQL查询应该返回 200 作为第二高的薪水。如果不存在第二高的薪水，那么查询应返回 null。

+---------------------+
| SecondHighestSalary |
+---------------------+
| 200                 |
+---------------------+

```

分析：使用ifull函数判断为null的情况，使用distinct去掉重复的值，limit 1,1从第二个开始取值，只获取一个


解答：
```sql
select ifnull(
    (select distinct Salary
    from Employee
    order by Salary desc
    limit 1,1),null) 
    as SecondHighestSalary
```

## 3 收入超过经理的员工

题目：
```
Employee 表包含所有员工，他们的经理也属于员工。每个员工都有一个 Id，此外还有一列对应员工的经理的 Id。

+----+-------+--------+-----------+
| Id | Name  | Salary | ManagerId |
+----+-------+--------+-----------+
| 1  | Joe   | 70000  | 3         |
| 2  | Henry | 80000  | 4         |
| 3  | Sam   | 60000  | NULL      |
| 4  | Max   | 90000  | NULL      |
+----+-------+--------+-----------+
给定 Employee 表，编写一个 SQL 查询，该查询可以获取收入超过他们经理的员工的姓名。在上面的表格中，Joe 是唯一一个收入超过他的经理的员工。

+----------+
| Employee |
+----------+
| Joe      |
+----------+
```

分析：两次查询，比较即可

解答：
```sql
select 
    Name as Employee
from 
    Employee as a
where 
    Salary > (
        select 
            Salary 
        from 
            Employee 
        where 
            Id=a.ManagerId)
```

## 4 查找重复的email
题目：
```
编写一个 SQL 查询，查找 Person 表中所有重复的电子邮箱。

示例：

+----+---------+
| Id | Email   |
+----+---------+
| 1  | a@b.com |
| 2  | c@d.com |
| 3  | a@b.com |
+----+---------+
根据以上输入，你的查询应返回以下结果：

+---------+
| Email   |
+---------+
| a@b.com |
+---------+
说明：所有电子邮箱都是小写字母。
```

分析：
- 直接join
- 使用group，计数判断

解答：
```sql
--方法一：
select distinct
    p1.Email
from 
    Person p1,Person p2
where
    p1.Email=p2.Email
    and
    p1.Id!=p2.Id


-- 方法二
select 
    Email
from 
    Person
group by 
    Email
having 
    count(*)>1
```

## 5 从不订购的客户

题目：
```
某网站包含两个表，Customers 表和 Orders 表。编写一个 SQL 查询，找出所有从不订购任何东西的客户。

Customers 表：

+----+-------+
| Id | Name  |
+----+-------+
| 1  | Joe   |
| 2  | Henry |
| 3  | Sam   |
| 4  | Max   |
+----+-------+
Orders 表：

+----+------------+
| Id | CustomerId |
+----+------------+
| 1  | 3          |
| 2  | 1          |
+----+------------+
例如给定上述表格，你的查询应返回：

+-----------+
| Customers |
+-----------+
| Henry     |
| Max       |
+-----------+
```

分析：查找id不在第二张表中的数据

解答：
```sql
select
    c.Name as Customers
from
    Customers as c
where
    c.Id not in (
    select distinct
        o.CustomerId
    from 
        Orders as o
    )
```

## 6 删除重复的email
题目：
```
编写一个 SQL 查询，来删除 Person 表中所有重复的电子邮箱，重复的邮箱里只保留 Id 最小 的那个。

+----+------------------+
| Id | Email            |
+----+------------------+
| 1  | john@example.com |
| 2  | bob@example.com  |
| 3  | john@example.com |
+----+------------------+
Id 是这个表的主键。
例如，在运行你的查询语句之后，上面的 Person 表应返回以下几行:

+----+------------------+
| Id | Email            |
+----+------------------+
| 1  | john@example.com |
| 2  | bob@example.com  |
+----+------------------+
```

分析：可以采用join，然后id1>id2

题解：
```sql
--方法一：
DELETE p1 FROM Person p1,
    Person p2
WHERE
    p1.Email = p2.Email AND p1.Id > p2.Id

--方法二：必须要新建一个临时表，因为不能同时堆一个表查询且删除
delete from Person
where
    Id not in (
    select Id from (
        select min(Id) Id from Person group by Email) p )
    
```

## 7 上升的温度
题目：
```
给定一个 Weather 表，编写一个 SQL 查询，来查找与之前（昨天的）日期相比温度更高的所有日期的 Id。

+---------+------------------+------------------+
| Id(INT) | RecordDate(DATE) | Temperature(INT) |
+---------+------------------+------------------+
|       1 |       2015-01-01 |               10 |
|       2 |       2015-01-02 |               25 |
|       3 |       2015-01-03 |               20 |
|       4 |       2015-01-04 |               30 |
+---------+------------------+------------------+
例如，根据上述给定的 Weather 表格，返回如下 Id:

+----+
| Id |
+----+
|  2 |
|  4 |
+----+
```

分析：使用inner join比较即可

解答：
```sql
select w.Id as Id
from Weather w
join Weather w2
on datediff(w.RecordDate,w2.RecordDate)=1 and w.Temperature>w2.Temperature;
```

## 8 大的国家

题目：
```
这里有张 World 表

+-----------------+------------+------------+--------------+---------------+
| name            | continent  | area       | population   | gdp           |
+-----------------+------------+------------+--------------+---------------+
| Afghanistan     | Asia       | 652230     | 25500100     | 20343000      |
| Albania         | Europe     | 28748      | 2831741      | 12960000      |
| Algeria         | Africa     | 2381741    | 37100000     | 188681000     |
| Andorra         | Europe     | 468        | 78115        | 3712000       |
| Angola          | Africa     | 1246700    | 20609294     | 100990000     |
+-----------------+------------+------------+--------------+---------------+
如果一个国家的面积超过300万平方公里，或者人口超过2500万，那么这个国家就是大国家。

编写一个SQL查询，输出表中所有大国家的名称、人口和面积。

例如，根据上表，我们应该输出:

+--------------+-------------+--------------+
| name         | population  | area         |
+--------------+-------------+--------------+
| Afghanistan  | 25500100    | 652230       |
| Algeria      | 37100000    | 2381741      |
+--------------+-------------+--------------+
```

分析：or即可

解答：
```sql
select 
    name,population,area
from
    World
where
    area>3000000 or population>25000000
```

## 9 超过5名同学的课
题目：
```
有一个courses 表 ，有: student (学生) 和 class (课程)。

请列出所有超过或等于5名学生的课。

例如,表:

+---------+------------+
| student | class      |
+---------+------------+
| A       | Math       |
| B       | English    |
| C       | Math       |
| D       | Biology    |
| E       | Math       |
| F       | Computer   |
| G       | Math       |
| H       | Math       |
| I       | Math       |
+---------+------------+
应该输出:

+---------+
| class   |
+---------+
| Math    |
+---------+
Note:
学生在每个课中不应被重复计算。
```

分析：group by class，然后去重，计数

解答：
```sql
select 
    class
from
    courses
group by
    class
having
    count(distinct student)>=5
```



## 总结
- 对同一个表进行join可以解决一些问题
- datediff(date1,date2)可以计算date1-date2的值，单位为day