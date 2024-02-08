---
title: "LeetCode 高频SQL50题基础版"
date: 2024-02-07T14:00:00+08:00
categories: ["SQL"]
---

一般`JOIN`、`GROUP BY`、子查询是面试的常考内容, 下面是**LeetCode 高频SQL50题基础版**中**高级查询和连接、子查询**的解析.

<!--more-->

# [每位经理的下属员工数量](https://leetcode.cn/problems/the-number-of-employees-which-report-to-each-employee/)

## 子查询

经过一段时间后我写下了如下的`SQL`:

```sql
SELECT Employees.employee_id,
       name,
       reports_count,
       average_age
FROM (select reports_to      AS employee_id,
             COUNT(*)        AS reports_count,
             ROUND(AVG(age)) AS average_age
      from Employees
      where reports_to IS NOT NULL
      group by reports_to) AS Managers
         JOIN Employees
              ON Employees.employee_id = Managers.employee_id
order by employee_id;
```

然而得到的结果却是**超时**.

然后分析整体的思路:

1. 通过按照报告人分组, 计算报告人数、平均年龄.
2. 通过JOIN补齐经理的姓名.

然后分析`SQL`的执行计划:

| id  | select_type | table        | type   | key     | ref                  | Extra                                              |
| --- | ----------- | ------------ | ------ | ------- | -------------------- | -------------------------------------------------- |
| 1   | PRIMARY     | `<derived2>` | ALL    |         |                      | Using where;<br>Using temporary;<br>Using filesort |
| 1   | PRIMARY     | Employees    | eq_ref | PRIMARY | Managers.employee_id |                                                    |
| 2   | DERIVED     | Employees    | ALL    |         |                      | Using where;<br>Using temporary                    |

首先执行`id=2`的子查询, 进行`where reports_to IS NOT NULL`然后将结果放入临时表`<derived2>`中.

然后执行`id=1`的查询, 先进行`group by reports_to`, 然后再走`Employees`表的主键索引进行`JOIN`.

## JOIN

我查看之前的通过记录发现了这样一段`SQL`:

```sql
SELECT Managers.employee_id,
       Managers.name,
       COUNT(*)                  reports_count,
       ROUND(AVG(Employees.age)) average_age
FROM Employees AS Managers
         JOIN Employees
              ON Managers.employee_id = Employees.reports_to
GROUP BY Managers.employee_id
ORDER BY Managers.employee_id;
```

> 当`sql_mode`包含`ONLY_FULL_GROUP_BY`时, 需要`employee_id`为主键, 这样才能推断出`name`的唯一性.
> [参考文档](https://dev.mysql.com/doc/refman/8.0/en/group-by-handling.html).

然后分析整体思路:

1. 先进行JOIN, 一张表看作**经理**, 一张表看作**直接汇报的员工**.
2. 然后再按照经理进行分组, 计算报告人数、平均年龄.

| id  | select_type | table     | type   | key     | ref                  | Extra                                              |
| --- | ----------- | --------- | ------ | ------- | -------------------- | -------------------------------------------------- |
| 1   | SIMPLE      | Employees | ALL    |         |                      | Using where;<br>Using temporary;<br>Using filesort |
| 1   | SIMPLE      | Managers  | eq_ref | PRIMARY | Employees.reports_to |

首先进行`ORDER BY Managers.employee_id`, 然后走`Employees`表的主键索引进行`JOIN`.

很明显少了一步, 所以这个`SQL`的执行速度要更快.

> 当`optimizer_switch`打开`derived_merge`时, MySQL会进行一定程度的子查询展开, 但在使用`GROUP BY`时会失效.

## 总结

使用子查询时一定要检查执行计划, 因为MySQL的Optimizer优化子查询的能力有限, 出现性能问题应尽量改写.

# [员工的直属部门](https://leetcode.cn/problems/primary-department-for-each-employee/)

## 子查询

首先这个问题的本质是根据条件进行过滤:

1. 多部门, 有标记: `primary_flag = 'Y'`
2. 单部门.

针对单部门的情况, 我们使用`IN`配合子查询进行过滤.

于是就有了如下的`SQL`:

```sql
select employee_id,
       department_id
from Employee
where primary_flag = 'Y'
   OR employee_id IN
      (select employee_id
       from Employee
       group by employee_id
       HAVING COUNT(*) = 1);
```

## JOIN

翻看之前的提交记录, 还有个`JOIN`的写法:

```sql
SELECT Employee.employee_id,
       Employee.department_id
FROM Employee
         JOIN (SELECT employee_id,
                      COUNT(*) department_count
               FROM Employee
               GROUP BY employee_id) AS DepartmentCount
              ON Employee.employee_id = DepartmentCount.employee_id
WHERE primary_flag = 'Y'
   OR DepartmentCount.department_count = 1;
```

# [判断三角形](https://leetcode.cn/problems/triangle-judgement/)

太简单, 没啥价值.

# [连续出现的数字](https://leetcode.cn/problems/consecutive-numbers/)

因为`id`是自增的, 如果不考虑删除, 那就可以当作行号用.

于是通过三次`JOIN`就可以找出连续出现的数字.

```sql
select DISTINCT Logs.num AS ConsecutiveNums
from Logs
         JOIN Logs AS Logs2 ON Logs.id = Logs2.id - 1
         JOIN Logs AS Logs3 on Logs.id = Logs3.id - 2
where Logs.num = Logs2.num
  AND Logs2.num = Logs3.num;
```

# [指定日期的产品价格](https://leetcode.cn/problems/product-price-at-a-given-date/)

首先我们分析一下思路:

1. 有些商品再`2019-08-16`之后才有价格, 需要给它们一个默认价格: `LEFT JOIN`.
2. 找出每个商品最后的价格: 通过`rank`对商品分组对时间排序.

```sql
select ProductIDs.product_id,
       IFNULL(ProductRanks.new_price, 10) AS price
from (select distinct product_id
      from Products) AS ProductIDs
         LEFT JOIN
     (select product_id,
             new_price,
             rank() over (partition by product_id order by change_date DESC) AS date_rank
      from Products
      WHERE change_date <= '2019-08-16') AS ProductRanks
     ON ProductIDs.product_id = ProductRanks.product_id
where date_rank = 1
   OR ProductRanks.date_rank IS NULL;
```

# [最后一个能进入巴士的人](https://leetcode.cn/problems/last-person-to-fit-in-the-bus/)

我解决这个问题的思路:

1. 将每个人前面所有人的体重进行累加(包括自己).
2. 再`HAVING`中过滤直接过滤掉已经超重的情况.

```sql

select Queue.person_name
from Queue
         LEFT JOIN Queue AS FrontQueue
                   ON Queue.turn >= FrontQueue.turn
group by Queue.person_id
HAVING SUM(FrontQueue.weight) <= 1000
order by Queue.turn desc
LIMIT 1;
```

# [按分类统计薪水](https://leetcode.cn/problems/count-salary-categories/)

太简单, 没啥价值.

# [上级经理已离职的公司员工](https://leetcode.cn/problems/employees-whose-manager-left-the-company/)

分析一下思路:

1. 一张表座位员工, 一张表座位经理, 但经理可能已经不存在: `LEFT JOIN`.
2. 有`manager_id`, 但`Manager`找不到: `WHERE`.

```sql
SELECT Employees.employee_id
FROM Employees
         LEFT JOIN Employees AS Managers
                   ON Employees.manager_id = Managers.employee_id
WHERE Employees.salary < 30000
  AND Employees.manager_id IS NOT NULL
  AND Managers.employee_id IS NULL
ORDER BY Employees.employee_id;
```

# [换座位](https://leetcode.cn/problems/exchange-seats/)

因为`id`是自增的, 如果不考虑删除, 那就可以当作行号用.

然后进行`JOIN`, 但要根据`id`的奇偶选择交换的座位的行号.

```sql
SELECT Seat.id,
       IFNULL(SwitchSeat.student, Seat.student) AS student
FROM Seat
         LEFT JOIN Seat AS SwitchSeat
                   ON Seat.id = SwitchSeat.id + IF(Seat.id % 2, -1, 1)
ORDER BY Seat.id;
```

# [电影评分](https://leetcode.cn/problems/movie-rating/)

这应该是两个相似的问题, 用两个`JOIN`+`GROUP BY`就可以解决了.

```sql
SELECT name AS results
FROM (SELECT Users.user_id, Users.name, COUNT(*) AS rating_count
      FROM Users
               JOIN MovieRating
                    ON Users.user_id = MovieRating.user_id
      GROUP BY Users.user_id
      ORDER BY rating_count DESC, Users.name
      LIMIT 1) AS UserRatingCount
UNION ALL
SELECT title AS results
FROM (SELECT Movies.movie_id, Movies.title, AVG(rating) AS avg_rating
      FROM Movies
               JOIN MovieRating
                    ON Movies.movie_id = MovieRating.movie_id
      WHERE YEAR(MovieRating.created_at) = 2020
        AND MONTH(created_at) = 2
      GROUP BY Movies.movie_id
      ORDER BY avg_rating DESC, Movies.title
      LIMIT 1) AS MovieAvgRating;
```

# [餐厅7天的营业额](https://leetcode.cn/problems/restaurant-growth/)

思路:

1. 用子查询查出`DISTINCT visited_on`.
2. `JOIN`过去7天的数据.
3. 分组求和.
4. 保证分组内存在7天的数据(不满7天不统计).

```sql
SELECT VisitedDates.visited_on,
       SUM(Customer.amount)               AS amount,
       ROUND(SUM(Customer.amount) / 7, 2) AS average_amount
FROM (SELECT DISTINCT visited_on
      FROM Customer) AS VisitedDates
         JOIN Customer
              ON TO_DAYS(Customer.visited_on) + 7 > TO_DAYS(VisitedDates.visited_on)
                  AND TO_DAYS(Customer.visited_on) <= TO_DAYS(VisitedDates.visited_on)
GROUP BY VisitedDates.visited_on
HAVING COUNT(DISTINCT Customer.visited_on) = 7
ORDER BY VisitedDates.visited_on;
```

# [谁的好友最多](https://leetcode.cn/problems/friend-requests-ii-who-has-the-most-friends/)

思路:

1. 该表并没有记录反向的好友关系, 可以通过`UNION ALL`得到.
2. 分组统计.

```sql
SELECT requester_id AS id, COUNT(*) AS num
FROM (SELECT requester_id,
             accepter_id
      FROM RequestAccepted
      UNION ALL
      SELECT accepter_id  AS requester_id,
             requester_id AS accepter_id
      FROM RequestAccepted) AS FullRequestAccepted
GROUP BY requester_id
ORDER BY num DESC
LIMIT 1;
```

# [2016年的投保总额](https://leetcode.cn/problems/investments-in-2016/)

思路:

1. 至少跟一个其他投保人的投保额相同: `JOIN`找满足条件的数据.
2. 经纬度与其他投保人都不同: `LEFT JOIN`找没有满足条件的.
3. 然后做交集: 这里通过JOIN实现.

```sql
SELECT ROUND(SUM(InsuranceMatchCondition1.tiv_2016), 2) AS tiv_2016
FROM (SELECT Insurance.pid, Insurance.tiv_2016
      FROM Insurance
               JOIN Insurance AS OtherInsurance
                    ON Insurance.pid <> OtherInsurance.pid
                        AND Insurance.tiv_2015 = OtherInsurance.tiv_2015
      GROUP BY Insurance.pid) AS InsuranceMatchCondition1
         JOIN
     (SELECT Insurance.pid, Insurance.tiv_2016
      FROM Insurance
               LEFT JOIN Insurance AS OtherInsurance
                         ON Insurance.pid <> OtherInsurance.pid
                             AND Insurance.lat = OtherInsurance.lat
                             AND Insurance.lon = OtherInsurance.lon
      WHERE OtherInsurance.pid IS NULL
      GROUP BY Insurance.pid) AS InsuranceMatchCondition2
     ON InsuranceMatchCondition1.pid = InsuranceMatchCondition2.pid;
```

# [部门工资前三高的员工](https://leetcode.cn/problems/department-top-three-salaries/)

思路:

1. 通过`JOIN`补齐`Department.name`.
2. 通过`dense_rank`计算工资排名.
3. 保留部门排名前三的员工.

```sql
SELECT Department, Employee, Salary
FROM (SELECT Department.name AS Department,
             Employee.name   AS Employee,
             salary          AS Salary,
             dense_rank() over (
                 partition by Employee.departmentId
                 order by salary DESC
                 )           AS salary_rank
      FROM Employee
               JOIN Department
                    ON Employee.departmentId = Department.id) AS SalaryRank
WHERE salary_rank <= 3;
```
