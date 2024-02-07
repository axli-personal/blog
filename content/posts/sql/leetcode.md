---
title: "LeetCode 高频SQL50题基础版 高级查询和连接"
date: 2024-02-07T14:00:00+08:00
categories: ["SQL"]
summary: "高级查询和连接"
---

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
