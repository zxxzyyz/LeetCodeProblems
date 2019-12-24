# Medium Problems

### Nth Highest Salary
Write a SQL query to get the nth highest salary from the Employee table.
```
+----+--------+
| Id | Salary |
+----+--------+
| 1  | 100    |
| 2  | 200    |
| 3  | 300    |
+----+--------+
For example, given the above Employee table, the nth highest salary where n = 2 is 200.
If there is no nth highest salary, then the query should return null.
+------------------------+
| getNthHighestSalary(2) |
+------------------------+
| 200                    |
+------------------------+
```
##### Solution
```
CREATE FUNCTION getNthHighestSalary(@N INT) RETURNS INT AS
BEGIN
    RETURN (
        /* Write your T-SQL query statement below. */
        SELECT DISTINCT Salary
        FROM (
            SELECT DENSE_RANK() OVER(ORDER BY Salary DESC) AS RowId, Salary
            FROM Employee) AS a
        WHERE @N = RowId
    );
END
```

### Exchange Seats
Mary is a teacher in a middle school and she has a table seat storing students' names and
their corresponding seat ids.
The column id is continuous increment.
Mary wants to change seats for the adjacent students.
Can you write a SQL query to output the result for Mary?
```
+---------+---------+
|    id   | student |
+---------+---------+
|    1    | Abbot   |
|    2    | Doris   |
|    3    | Emerson |
|    4    | Green   |
|    5    | Jeames  |
+---------+---------+
For the sample input, the output is:
+---------+---------+
|    id   | student |
+---------+---------+
|    1    | Doris   |
|    2    | Abbot   |
|    3    | Green   |
|    4    | Emerson |
|    5    | Jeames  |
+---------+---------+
```
##### Solution
```
SELECT
  IIF(id % 2 <> 0, IIF(id = (SELECT COUNT(*) FROM seat), id, id + 1), id - 1) AS id,
  student
FROM seat
ORDER BY id
```

### Department Highest Salary
The Employee table holds all employees. Every employee has an Id, a salary, and there is also a column for the department Id.
```
+----+-------+--------+--------------+
| Id | Name  | Salary | DepartmentId |
+----+-------+--------+--------------+
| 1  | Joe   | 70000  | 1            |
| 2  | Jim   | 90000  | 1            |
| 3  | Henry | 80000  | 2            |
| 4  | Sam   | 60000  | 2            |
| 5  | Max   | 90000  | 1            |
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
Write a SQL query to find employees who have the highest salary in each of the departments. For the above tables, your SQL query should return the following rows (order of rows does not matter).
```
+------------+----------+--------+
| Department | Employee | Salary |
+------------+----------+--------+
| IT         | Max      | 90000  |
| IT         | Jim      | 90000  |
| Sales      | Henry    | 80000  |
+------------+----------+--------+
```
##### Solution
```
SELECT d.Name AS 'Department', e.Name AS'Employee', e.Salary AS 'Salary'
FROM Employee e
JOIN Department d ON d.Id = e.DepartmentId
JOIN (SELECT DepartmentId, MAX(Salary) AS 'Highest' FROM Employee GROUP BY DepartmentId) tmp ON tmp.DepartmentId = e.DepartmentId
WHERE e.Salary = tmp.Highest
```

### Rank Scores
Write a SQL query to rank scores. If there is a tie between two scores,
both should have the same ranking.
Note that after a tie, the next ranking number should be the next consecutive integer value.
In other words, there should be no "holes" between ranks.
```
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
```
For example, given the above Scores table, your query should generate the following report (order by highest score):
```
+-------+------+
| Score | Rank |
+-------+------+
| 4.00  | 1    |
| 4.00  | 1    |
| 3.85  | 2    |
| 3.65  | 3    |
| 3.65  | 3    |
| 3.50  | 4    |
+-------+------+
```
##### Solution
```
SELECT Score, Dense_Rank() OVER (ORDER BY Score DESC) AS Rank FROM Scores
```
