# Hard Problems

### Trips and Users
## Schema
<details>
  <summary>Schema</summary>
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
</details>

## Problem
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
