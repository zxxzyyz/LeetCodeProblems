# DataBase
My solution for open problems(free sources) from LeetCode.
### Second Highest Salary
Write a SQL query to get the second highest salary from the Employee table.
```
+----+--------+
| Id | Salary |
+----+--------+
| 1  | 100    |
| 2  | 200    |
| 3  | 300    |
+----+--------+
```
For example, given the above Employee table, the query should return 200 as the second highest salary. If there is no second highest salary, then the query should return null.
```
+---------------------+
| SecondHighestSalary |
+---------------------+
| 200                 |
+---------------------+
```
##### Solution
```
SELECT max(Salary) as "SecondHighestSalary"
FROM Employee WHERE Salary < (SELECT max(Salary) FROM Employee)
```
```
SELECT max(Salary) as "SecondHighestSalary"
FROM Employee WHERE Salary NOT IN (SELECT max(Salary) FROM Employee)
```

### Combine Two Tables
```
Table: Person
+-------------+---------+
| Column Name | Type    |
+-------------+---------+
| PersonId    | int     |
| FirstName   | varchar |
| LastName    | varchar |
+-------------+---------+
```
PersonId is the primary key column for this table.
```
Table: Address
+-------------+---------+
| Column Name | Type    |
+-------------+---------+
| AddressId   | int     |
| PersonId    | int     |
| City        | varchar |
| State       | varchar |
+-------------+---------+
```
AddressId is the primary key column for this table.
 

Write a SQL query for a report that provides the following information for each person in the Person table, regardless if there is an address for each of those people:

FirstName, LastName, City, State
##### Solution
```
SELECT p.FirstName, p.LastName, a.City, a.State
FROM Person as p
LEFT JOIN Address as a
ON p.PersonId = a.PersonId
```

### Employees Earning More Than Their Managers
The Employee table holds all employees including their managers. Every employee has an Id, and there is also a column for the manager Id.
```
+----+-------+--------+-----------+
| Id | Name  | Salary | ManagerId |
+----+-------+--------+-----------+
| 1  | Joe   | 70000  | 3         |
| 2  | Henry | 80000  | 4         |
| 3  | Sam   | 60000  | NULL      |
| 4  | Max   | 90000  | NULL      |
+----+-------+--------+-----------+
```
Given the Employee table, write a SQL query that finds out employees who earn more than their managers. For the above table, Joe is the only employee who earns more than his manager.
```
+----------+
| Employee |
+----------+
| Joe      |
+----------+
```
##### Solution
```
SELECT e.name as Employee FROM Employee e
WHERE e.Salary > (SELECT m.Salary FROM Employee m WHERE m.Id = e.ManagerId)
```

### Duplicate Emails
Write a SQL query to find all duplicate emails in a table named Person.
```
+----+---------+
| Id | Email   |
+----+---------+
| 1  | a@b.com |
| 2  | c@d.com |
| 3  | a@b.com |
+----+---------+
```
For example, your query should return the following for the above table:
```
+---------+
| Email   |
+---------+
| a@b.com |
+---------+
```
##### Solution
```
SELECT Email FROM Person GROUP BY Email HAVING COUNT(Email) > 1
```

### Customers Who Never Order
Suppose that a website contains two tables, the Customers table and the Orders table. Write a SQL query to find all customers who never order anything.
```
Table: Customers
+----+-------+
| Id | Name  |
+----+-------+
| 1  | Joe   |
| 2  | Henry |
| 3  | Sam   |
| 4  | Max   |
+----+-------+
Table: Orders
+----+------------+
| Id | CustomerId |
+----+------------+
| 1  | 3          |
| 2  | 1          |
+----+------------+
Using the above tables as example, return the following:
+-----------+
| Customers |
+-----------+
| Henry     |
| Max       |
+-----------+
```
##### Solution
```
SELECT c.Name as "Customers" FROM Customers c
WHERE c.Id NOT IN (SELECT CustomerId FROM Orders)
```

### Rising Temperature
Given a Weather table, write a SQL query to find all dates' Ids with higher temperature compared to its previous (yesterday's) dates.
```
+---------+------------------+------------------+
| Id(INT) | RecordDate(DATE) | Temperature(INT) |
+---------+------------------+------------------+
|       1 |       2015-01-01 |               10 |
|       2 |       2015-01-02 |               25 |
|       3 |       2015-01-03 |               20 |
|       4 |       2015-01-04 |               30 |
+---------+------------------+------------------+
For example, return the following Ids for the above Weather table:
+----+
| Id |
+----+
|  2 |
|  4 |
+----+
```
##### Solution
SQL-Server solution
```
SELECT w.Id FROM Weather w
WHERE w.Temperature > (SELECT yda.Temperature
                       FROM Weather yda
                       WHERE yda.RecordDate = DATEADD(day, -1, w.RecordDate))
```
Someone else's solution
```
select a.id as Id
from Weather a , Weather b
where datediff(day, b.RecordDate, a.RecordDate) = 1
and a.Temperature > b.Temperature
```
Oracle solution
```
SELECT w.Id FROM Weather w JOIN Weather yda ON yda.RecordDate = w.RecordDate - 1
WHERE w.Temperature > yda.Temperature
```
