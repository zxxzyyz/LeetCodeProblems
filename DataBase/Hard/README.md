# Hard Problems

### Trips and Users

<details>
  <summary>Schema</summary>
	
  ```
  Create table Trips (Id int, Client_Id int, Driver_Id int, City_Id int, Status varchar(50) NOT NULL CHECK(Status IN ('completed', 'cancelled_by_driver', 'cancelled_by_client')), Request_at varchar(50))
  Create table Users (Users_Id int, Banned varchar(50), Role varchar(50) NOT NULL CHECK(Role IN ('client', 'driver', 'partner')))
  insert into Trips (Id, Client_Id, Driver_Id, City_Id, Status, Request_at) values ('1', '1', '10', '1', 'completed', '2013-10-01')
  insert into Trips (Id, Client_Id, Driver_Id, City_Id, Status, Request_at) values ('2', '2', '11', '1', 'cancelled_by_driver', '2013-10-01')
  insert into Trips (Id, Client_Id, Driver_Id, City_Id, Status, Request_at) values ('3', '3', '12', '6', 'completed', '2013-10-01')
  insert into Trips (Id, Client_Id, Driver_Id, City_Id, Status, Request_at) values ('4', '4', '13', '6', 'cancelled_by_client', '2013-10-01')
  insert into Trips (Id, Client_Id, Driver_Id, City_Id, Status, Request_at) values ('5', '1', '10', '1', 'completed', '2013-10-02')
  insert into Trips (Id, Client_Id, Driver_Id, City_Id, Status, Request_at) values ('6', '2', '11', '6', 'completed', '2013-10-02')
  insert into Trips (Id, Client_Id, Driver_Id, City_Id, Status, Request_at) values ('7', '3', '12', '6', 'completed', '2013-10-02')
  insert into Trips (Id, Client_Id, Driver_Id, City_Id, Status, Request_at) values ('8', '2', '12', '12', 'completed', '2013-10-03')
  insert into Trips (Id, Client_Id, Driver_Id, City_Id, Status, Request_at) values ('9', '3', '10', '12', 'completed', '2013-10-03')
  insert into Trips (Id, Client_Id, Driver_Id, City_Id, Status, Request_at) values ('10', '4', '13', '12', 'cancelled_by_driver', '2013-10-03')
  insert into Users (Users_Id, Banned, Role) values ('1', 'No', 'client')
  insert into Users (Users_Id, Banned, Role) values ('2', 'Yes', 'client')
  insert into Users (Users_Id, Banned, Role) values ('3', 'No', 'client')
  insert into Users (Users_Id, Banned, Role) values ('4', 'No', 'client')
  insert into Users (Users_Id, Banned, Role) values ('10', 'No', 'driver')
  insert into Users (Users_Id, Banned, Role) values ('11', 'No', 'driver')
  insert into Users (Users_Id, Banned, Role) values ('12', 'No', 'driver')
  insert into Users (Users_Id, Banned, Role) values ('13', 'No', 'driver')
  ```
</details>

The Trips table holds all taxi trips.
Each trip has a unique Id,
while Client_Id and Driver_Id are both foreign keys to the Users_Id at the Users table.
Status is an ENUM type of (‘completed’, ‘cancelled_by_driver’, ‘cancelled_by_client’).
```
+----+-----------+-----------+---------+--------------------+----------+
| Id | Client_Id | Driver_Id | City_Id |        Status      |Request_at|
+----+-----------+-----------+---------+--------------------+----------+
| 1  |     1     |    10     |    1    |     completed      |2013-10-01|
| 2  |     2     |    11     |    1    | cancelled_by_driver|2013-10-01|
| 3  |     3     |    12     |    6    |     completed      |2013-10-01|
| 4  |     4     |    13     |    6    | cancelled_by_client|2013-10-01|
| 5  |     1     |    10     |    1    |     completed      |2013-10-02|
| 6  |     2     |    11     |    6    |     completed      |2013-10-02|
| 7  |     3     |    12     |    6    |     completed      |2013-10-02|
| 8  |     2     |    12     |    12   |     completed      |2013-10-03|
| 9  |     3     |    10     |    12   |     completed      |2013-10-03| 
| 10 |     4     |    13     |    12   | cancelled_by_driver|2013-10-03|
+----+-----------+-----------+---------+--------------------+----------+
```
The Users table holds all users.
Each user has an unique Users_Id, and Role is an ENUM type of (‘client’, ‘driver’, ‘partner’).
```
+----------+--------+--------+
| Users_Id | Banned |  Role  |
+----------+--------+--------+
|    1     |   No   | client |
|    2     |   Yes  | client |
|    3     |   No   | client |
|    4     |   No   | client |
|    10    |   No   | driver |
|    11    |   No   | driver |
|    12    |   No   | driver |
|    13    |   No   | driver |
+----------+--------+--------+
```
Write a SQL query to find the cancellation rate of requests made by unbanned users
(both client and driver must be unbanned) between Oct 1, 2013 and Oct 3, 2013.
The cancellation rate is computed by dividing the number of canceled (by client or driver)
requests made by unbanned users by the total number of requests made by unbanned users.
For the above tables,
your SQL query should return the following rows with the cancellation rate being rounded to two decimal places.
```
+------------+-------------------+
|     Day    | Cancellation Rate |
+------------+-------------------+
| 2013-10-01 |       0.33        |
| 2013-10-02 |       0.00        |
| 2013-10-03 |       0.50        |
+------------+-------------------+
```
##### Solution
```
SELECT
  Request_At AS 'Day',
  CONVERT(DECIMAL(3, 2),
    CONVERT(DECIMAL(3, 2) , COUNT(CASE WHEN Status LIKE 'cancelled_by_%' THEN 1 ELSE NULL END))
	/ CONVERT(DECIMAL(3, 2), COUNT(*))
  ) AS 'Cancellation Rate'
FROM Trips
WHERE Request_at BETWEEN '2013-10-01' AND '2013-10-03'
  AND Client_Id IN (SELECT Users_Id FROM Users WHERE Banned = 'No')
  AND Driver_Id IN (SELECT Users_Id FROM Users WHERE Banned = 'No')
GROUP BY Request_At
```

### Department Top Three Salaries

<details>
  <summary>Schema</summary>
	
  ```	
  Create table Employee (Id int, Name varchar(255), Salary int, DepartmentId int)
  Create table Department (Id int, Name varchar(255))
  insert into Employee (Id, Name, Salary, DepartmentId) values ('1', 'Joe', '85000', '1')
  insert into Employee (Id, Name, Salary, DepartmentId) values ('2', 'Henry', '80000', '2')
  insert into Employee (Id, Name, Salary, DepartmentId) values ('3', 'Sam', '60000', '2')
  insert into Employee (Id, Name, Salary, DepartmentId) values ('4', 'Max', '90000', '1')
  insert into Employee (Id, Name, Salary, DepartmentId) values ('5', 'Janet', '69000', '1')
  insert into Employee (Id, Name, Salary, DepartmentId) values ('6', 'Randy', '85000', '1')
  insert into Employee (Id, Name, Salary, DepartmentId) values ('7', 'Will', '70000', '1')
  insert into Department (Id, Name) values ('1', 'IT')
  insert into Department (Id, Name) values ('2', 'Sales')
  ```
</details>

The Employee table holds all employees. Every employee has an Id, and there is also a column for the department Id.
```
+----+-------+--------+--------------+
| Id | Name  | Salary | DepartmentId |
+----+-------+--------+--------------+
| 1  | Joe   | 85000  | 1            |
| 2  | Henry | 80000  | 2            |
| 3  | Sam   | 60000  | 2            |
| 4  | Max   | 90000  | 1            |
| 5  | Janet | 69000  | 1            |
| 6  | Randy | 85000  | 1            |
| 7  | Will  | 70000  | 1            |
+----+-------+--------+--------------+
```
The Department table holds all departments of the company.
```
+----+----------+
| Id | Name     |
+----+----------+
| 1  | IT       |
| 2  | Sales    |
+----+----------+
```
Write a SQL query to find employees who earn the top three salaries in each of the department. For the above tables, your SQL query should return the following rows (order of rows does not matter).
```
+------------+----------+--------+
| Department | Employee | Salary |
+------------+----------+--------+
| IT         | Max      | 90000  |
| IT         | Randy    | 85000  |
| IT         | Joe      | 85000  |
| IT         | Will     | 70000  |
| Sales      | Henry    | 80000  |
| Sales      | Sam      | 60000  |
+------------+----------+--------+
```
Explanation:
In IT department, Max earns the highest salary, both Randy and Joe earn the second highest salary, and Will earns the third highest salary. There are only two employees in the Sales department, Henry earns the highest salary while Sam earns the second highest salary.
##### Solution
```
SELECT d.Name AS 'Department', e.Name 'Employee', e.Salary
FROM Employee AS e
JOIN (
select Dense_Rank() OVER (PARTITION BY e.DepartmentId ORDER BY e.Salary DESC) AS Rank, e.Salary, e.DepartmentId
from Employee e JOIN Department d On e.DepartmentId = d.Id
GROUP BY e.DepartmentId, e.Salary) AS r
ON e.Salary = r.Salary
AND e.DepartmentId = r.DepartmentId
JOIN Department d ON e.DepartmentId = d.Id
WHERE r.Rank <= 3
ORDER BY d.Name, e.Salary desc, e.Name desc
```
```
SELECT d.Name AS 'Department', e1.Name AS 'Employee', e1.Salary
FROM Employee e1
JOIN Department d ON e1.DepartmentId = d.Id
WHERE 3 > (SELECT COUNT(DISTINCT e2.Salary)
           FROM Employee e2
           WHERE e2.Salary > e1.Salary AND e1.DepartmentId = e2.DepartmentId)
```

### Human Traffic of Stadium
<details>
  <summary>Schema</summary>
	
  ```
  Create table stadium (id int, visit_date DATE NULL, people int)
  insert into stadium (id, visit_date, people) values ('1', '2017-01-01', '10')
  insert into stadium (id, visit_date, people) values ('2', '2017-01-02', '109')
  insert into stadium (id, visit_date, people) values ('3', '2017-01-03', '150')
  insert into stadium (id, visit_date, people) values ('4', '2017-01-04', '99')
  insert into stadium (id, visit_date, people) values ('5', '2017-01-05', '145')
  insert into stadium (id, visit_date, people) values ('6', '2017-01-06', '1455')
  insert into stadium (id, visit_date, people) values ('7', '2017-01-07', '199')
  insert into stadium (id, visit_date, people) values ('8', '2017-01-08', '188')
  ```
</details>
X city built a new stadium, each day many people visit it and the stats are saved as these columns: id, visit_date, people
Please write a query to display the records which have 3 or more consecutive rows and the amount of people more than 100(inclusive).
For example, the table stadium:
```
+------+------------+-----------+
| id   | visit_date | people    |
+------+------------+-----------+
| 1    | 2017-01-01 | 10        |
| 2    | 2017-01-02 | 109       |
| 3    | 2017-01-03 | 150       |
| 4    | 2017-01-04 | 99        |
| 5    | 2017-01-05 | 145       |
| 6    | 2017-01-06 | 1455      |
| 7    | 2017-01-07 | 199       |
| 8    | 2017-01-08 | 188       |
+------+------------+-----------+
```
For the sample data above, the output is:
```
+------+------------+-----------+
| id   | visit_date | people    |
+------+------------+-----------+
| 5    | 2017-01-05 | 145       |
| 6    | 2017-01-06 | 1455      |
| 7    | 2017-01-07 | 199       |
| 8    | 2017-01-08 | 188       |
+------+------------+-----------+
```
Note:
Each day only have one row record, and the dates are increasing with id increasing.
##### Solution
```
SELECT s1.* FROM stadium AS s1, stadium AS s2, stadium as s3
  WHERE s1.people>=100 
  AND s2.people>=100
  AND s3.people>=100
  AND ((s1.id + 1 = s2.id AND s1.id + 2 = s3.id)
    OR (s1.id - 1 = s2.id AND s1.id + 1 = s3.id)
    OR (s1.id - 2 = s2.id AND s1.id - 1 = s3.id))
  GROUP BY s1.id, s1.visit_date, s1.people
```
