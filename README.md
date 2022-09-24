## SQL Queries

#### Query Optimization Techniques

##### Use Column Names Instead of * in a SELECT Statement.

##### Avoid including a HAVING clause in SELECT statements.

Original query:
```sql
SELECT s.cust_id,count(s.cust_id)
FROM SH.sales s
GROUP BY s.cust_id
HAVING s.cust_id != '1660' AND s.cust_id != '2';
```
Improved query:
```sql
SELECT s.cust_id,count(cust_id)
FROM SH.sales s
WHERE s.cust_id != '1660'
AND s.cust_id !='2'
GROUP BY s.cust_id;
```
##### Un-nest sub queries

Original query:
```sql
SELECT *
FROM SH.products p
WHERE p.prod_id =
 (SELECT s.prod_id
 FROM SH.sales s
 WHERE s.cust_id = 100996
 AND s.quantity_sold = 1 );
```
Improved query:
```sql
SELECT p.*
FROM SH.products p, sales s
WHERE p.prod_id = s.prod_id
AND s.cust_id = 100996
AND s.quantity_sold = 1;
```
##### Remove any redundant mathematics

Original query:
```sql
SELECT *
FROM SH.sales s
WHERE s.cust_id + 10000 < 35000;
```
Improved query:
```sql
SELECT *
FROM SH.sales s
WHERE s.cust_id < 25000;
```
#### Query to skip first five rows and then display remaining all rows from emp table?
```sql
select * from ( select rownum r,ename, sal from emp ) where r>5;
```
#### Query to display junior most employee details from emp table?
```sql
select * from emp where hiredate= ( select max(hiredate) from emp);
```
#### Query to display employee details who are getting min salary in each department from emp table?
```sql
select * from emp where (deptno, sal) in ( select deptno, min(sal) from emp group by deptno);
```
#### Query to display last four rows from emp table?
```sql
select * from emp minus select * from emp where rownum<= (select count(*) - 4 from emp);
```
#### Query to display first row and last row from emp table?
```sql
select * from ( select rownum r, ename, sal from emp) where r=1 or r= ( select count(*) from emp);
```
#### Query to display odd number of records from emp table?
```sql
select * from ( select rownum r, ename, sal from emp) where mod(r,2)=1;
```
#### Query to display 5th highest salary employee from emp table?
```sql
select * from ( select rownum r, ename, sal from ( select * from emp order by sal desc)) where r=5;
```
#### Query to display nth highest salary employee from emp table using corelated subquery?
```sql
select * from emp e1 where &n= ( select count(distinct(sal)) from emp e2 where e2.sal>=e1.sal);
```
#### Query to display first ten rows from emp table?
```sql
select * from emp where rownum <=10;
```
#### Query to display first five highest salary employees from from emp table?
```sql
select * from ( select * from emp order by sal desc ) where rownum<=5;
```
#### Query to display 2nd, 3rd, 4th, 5th, 7th, 9th row from emp table?
```sql
select * from ( select rownum r, ename, sal from emp) where r in (2,3,4,5,7,9);
```
#### Query to display second row from emp tablle?
```sql
select * from ( select rownum r,ename,sal from emp) where r=2;
```
#### Write an SQL query to report the first name, last name, city, and state of each person in the Person table. 

If the address of a personId is not present in the Address table, report null instead. Return the result table in any order. The query result format is in the following example.
 
Example 1:
Input: 
```table
Person table:
+----------+----------+-----------+
| personId | lastName | firstName |
+----------+----------+-----------+
| 1        | Wang     | Allen     |
| 2        | Alice    | Bob       |
+----------+----------+-----------+
Address table:
+-----------+----------+---------------+------------+
| addressId | personId | city          | state      |
+-----------+----------+---------------+------------+
| 1         | 2        | New York City | New York   |
| 2         | 3        | Leetcode      | California |
+-----------+----------+---------------+------------+
Output: 
+-----------+----------+---------------+----------+
| firstName | lastName | city          | state    |
+-----------+----------+---------------+----------+
| Allen     | Wang     | Null          | Null     |
| Bob       | Alice    | New York City | New York |
+-----------+----------+---------------+----------+
```
Explanation: 
There is no address in the address table for the personId = 1 so we return null in their city and state.
addressId = 1 contains information about the address of personId = 2.
```sql
SELECT p.firstName, p.lastName, a.city, a.state 
FROM Person p LEFT JOIN Address a 
ON p.personId = a.personId
```
#### Write an SQL query to find the employees who earn more than their managers.
Return the result table in any order. The query result format is in the following example.
 
Example 1:
Input: 
```table
Employee table:
+----+-------+--------+-----------+
| id | name  | salary | managerId |
+----+-------+--------+-----------+
| 1  | Joe   | 70000  | 3         |
| 2  | Henry | 80000  | 4         |
| 3  | Sam   | 60000  | Null      |
| 4  | Max   | 90000  | Null      |
+----+-------+--------+-----------+
Output: 
+----------+
| Employee |
+----------+
| Joe      |
+----------+
```
Explanation: Joe is the only employee who earns more than his manager.
```sql
SELECT e1.name AS Employee FROM Employee e1 
INNER JOIN Employee e2 
ON e1.salary > e2.salary AND e1.managerId = e2.id
```
#### Write an SQL query to report all the duplicate emails.
Return the result table in any order. The query result format is in the following example.
 
Example 1:
Input: 
```table
Person table:
+----+---------+
| id | email   |
+----+---------+
| 1  | a@b.com |
| 2  | c@d.com |
| 3  | a@b.com |
+----+---------+
Output: 
+---------+
| Email   |
+---------+
| a@b.com |
+---------+
```
Explanation: a@b.com is repeated two times.
```sql
WITH email_count AS (
    SELECT email, COUNT(*) FROM Person 
    GROUP BY email
    HAVING COUNT(*) > 1
)
SELECT email FROM email_count
```
#### Write an SQL query to report all customers who never order anything.
Return the result table in any order. The query result format is in the following example.
 
Example 1:
Input: 
```table
Customers table:
+----+-------+
| id | name  |
+----+-------+
| 1  | Joe   |
| 2  | Henry |
| 3  | Sam   |
| 4  | Max   |
+----+-------+
Orders table:
+----+------------+
| id | customerId |
+----+------------+
| 1  | 3          |
| 2  | 1          |
+----+------------+
Output: 
+-----------+
| Customers |
+-----------+
| Henry     |
| Max       |
+-----------+
```
```sql
SELECT name AS Customers FROM Customers 
WHERE id NOT IN (SELECT customerId FROM Orders)
```
#### Write an SQL query to delete all the duplicate emails, keeping only one unique email with the smallest id. 
Note that you are supposed to write a DELETE statement and not a SELECT one. After running your script, the answer shown is the Person table.  The driver will first compile and run your piece of code and then show the Person table.  The final order of the Person table does not matter. The query result format is in the following example.
 
Example 1:
Input: 
```table
Person table:
+----+------------------+
| id | email            |
+----+------------------+
| 1  | john@example.com |
| 2  | bob@example.com  |
| 3  | john@example.com |
+----+------------------+
Output: 
+----+------------------+
| id | email            |
+----+------------------+
| 1  | john@example.com |
| 2  | bob@example.com  |
+----+------------------+
```
Explanation: john@example.com is repeated two times. We keep the row with the smallest Id = 1.
```sql
DELETE p1 
FROM Person p1, Person p2 
WHERE p1.id > p2.id AND p1.email = p2.email
```
#### Write an SQL query to find all dates' Id with higher temperatures compared to its previous dates (yesterday).
Return the result table in any order. The query result format is in the following example.
 
Example 1:
Input: 
```table
Weather table:
+----+------------+-------------+
| id | recordDate | temperature |
+----+------------+-------------+
| 1  | 2015-01-01 | 10          |
| 2  | 2015-01-02 | 25          |
| 3  | 2015-01-03 | 20          |
| 4  | 2015-01-04 | 30          |
+----+------------+-------------+
Output: 
+----+
| id |
+----+
| 2  |
| 4  |
+----+
```
Explanation: 
In 2015-01-02, the temperature was higher than the previous day (10 -> 25).
In 2015-01-04, the temperature was higher than the previous day (20 -> 30).
```sql
SELECT w2.id FROM Weather w1, Weather w2 
WHERE w2.temperature > w1.temperature 
AND DATEDIFF(w2.recordDate, w1.recordDate) = 1
```
#### Write an SQL query to report the first login date for each player.
Return the result table in any order. The query result format is in the following example.

Example 1:
Input: 
```table
Activity table:
+-----------+-----------+------------+--------------+
| player_id | device_id | event_date | games_played |
+-----------+-----------+------------+--------------+
| 1         | 2         | 2016-03-01 | 5            |
| 1         | 2         | 2016-05-02 | 6            |
| 2         | 3         | 2017-06-25 | 1            |
| 3         | 1         | 2016-03-02 | 0            |
| 3         | 4         | 2018-07-03 | 5            |
+-----------+-----------+------------+--------------+
Output: 
+-----------+-------------+
| player_id | first_login |
+-----------+-------------+
| 1         | 2016-03-01  |
| 2         | 2017-06-25  |
| 3         | 2016-03-02  |
+-----------+-------------+
```
```sql
SELECT player_id, MIN(event_date) AS first_login 
FROM Activity 
GROUP BY player_id
```
#### Write an SQL query to report the names of the customer that are not referred by the customer with id = 2.
Return the result table in any order. The query result format is in the following example.
 
Example 1:
Input: 
```table
Customer table:
+----+------+------------+
| id | name | referee_id |
+----+------+------------+
| 1  | Will | null       |
| 2  | Jane | null       |
| 3  | Alex | 2          |
| 4  | Bill | null       |
| 5  | Zack | 1          |
| 6  | Mark | 2          |
+----+------+------------+
Output: 
+------+
| name |
+------+
| Will |
| Jane |
| Bill |
| Zack |
+------+
```
```sql
SELECT name FROM Customer 
WHERE id NOT IN (
    SELECT id FROM Customer 
    WHERE referee_id = 2
)
```
#### Write an SQL query to find the customer_number for the customer who has placed the largest number of orders.
The test cases are generated so that exactly one customer will have placed more orders than any other customer. The query result format is in the following example.
 
Example 1:
Input: 
```table
Orders table:
+--------------+-----------------+
| order_number | customer_number |
+--------------+-----------------+
| 1            | 1               |
| 2            | 2               |
| 3            | 3               |
| 4            | 3               |
+--------------+-----------------+
Output: 
+-----------------+
| customer_number |
+-----------------+
| 3               |
+-----------------+
```
Explanation: 
The customer with number 3 has two orders, which is greater than either customer 1 or 2 because each of them only has one order. 
So the result is customer_number 3.
```sql
SELECT customer_number FROM Orders 
GROUP BY customer_number
ORDER BY COUNT(order_number) DESC LIMIT 1
```
#### Write an SQL query to report the name, population, and area of the big countries.
Return the result table in any order. The query result format is in the following example.

A country is big if: it has an area of at least three million (i.e., 3000000 km2), or it has a population of at least twenty-five million (i.e., 25000000).

Example 1:
Input: 
```table
World table:
+-------------+-----------+---------+------------+--------------+
| name        | continent | area    | population | gdp          |
+-------------+-----------+---------+------------+--------------+
| Afghanistan | Asia      | 652230  | 25500100   | 20343000000  |
| Albania     | Europe    | 28748   | 2831741    | 12960000000  |
| Algeria     | Africa    | 2381741 | 37100000   | 188681000000 |
| Andorra     | Europe    | 468     | 78115      | 3712000000   |
| Angola      | Africa    | 1246700 | 20609294   | 100990000000 |
+-------------+-----------+---------+------------+--------------+
Output: 
+-------------+------------+---------+
| name        | population | area    |
+-------------+------------+---------+
| Afghanistan | 25500100   | 652230  |
| Algeria     | 37100000   | 2381741 |
+-------------+------------+---------+
```
```sql
SELECT name, population, area 
FROM World 
WHERE area >= 3000000 OR population >= 25000000
```
#### Write an SQL query to report all the classes that have at least five students.
Return the result table in any order. The query result format is in the following example.
 
Example 1:
Input: 
```table
Courses table:
+---------+----------+
| student | class    |
+---------+----------+
| A       | Math     |
| B       | English  |
| C       | Math     |
| D       | Biology  |
| E       | Math     |
| F       | Computer |
| G       | Math     |
| H       | Math     |
| I       | Math     |
+---------+----------+
Output: 
+---------+
| class   |
+---------+
| Math    |
+---------+
```
Explanation: 
- Math has 6 students, so we include it.
- English has 1 student, so we do not include it.
- Biology has 1 student, so we do not include it.
- Computer has 1 student, so we do not include it.
```sql
SELECT class FROM Courses 
GROUP BY class 
HAVING COUNT(student) >= 5
```
#### Write an SQL query to report the names of all the salespersons who did not have any orders related to the company with the name "RED".
Return the result table in any order. The query result format is in the following example.
 
Example 1:
Input: 
```table
SalesPerson table:
+----------+------+--------+-----------------+------------+
| sales_id | name | salary | commission_rate | hire_date  |
+----------+------+--------+-----------------+------------+
| 1        | John | 100000 | 6               | 4/1/2006   |
| 2        | Amy  | 12000  | 5               | 5/1/2010   |
| 3        | Mark | 65000  | 12              | 12/25/2008 |
| 4        | Pam  | 25000  | 25              | 1/1/2005   |
| 5        | Alex | 5000   | 10              | 2/3/2007   |
+----------+------+--------+-----------------+------------+
Company table:
+--------+--------+----------+
| com_id | name   | city     |
+--------+--------+----------+
| 1      | RED    | Boston   |
| 2      | ORANGE | New York |
| 3      | YELLOW | Boston   |
| 4      | GREEN  | Austin   |
+--------+--------+----------+
Orders table:
+----------+------------+--------+----------+--------+
| order_id | order_date | com_id | sales_id | amount |
+----------+------------+--------+----------+--------+
| 1        | 1/1/2014   | 3      | 4        | 10000  |
| 2        | 2/1/2014   | 4      | 5        | 5000   |
| 3        | 3/1/2014   | 1      | 1        | 50000  |
| 4        | 4/1/2014   | 1      | 4        | 25000  |
+----------+------------+--------+----------+--------+
Output: 
+------+
| name |
+------+
| Amy  |
| Mark |
| Alex |
+------+
```
Explanation: 
According to orders 3 and 4 in the Orders table, it is easy to tell that only salesperson John and Pam have sales to company RED, 
so we report all the other names in the table salesperson.
```sql
SELECT SalesPerson.name FROM SalesPerson 
WHERE SalesPerson.sales_id NOT IN (
    SELECT SalesPerson.sales_id FROM SalesPerson 
    INNER JOIN Orders 
    ON SalesPerson.sales_id = Orders.sales_id 
    INNER JOIN Company 
    ON Orders.com_id = Company.com_id AND Company.name IN ("RED")
)
```
#### Write an SQL query to report the movies with an odd-numbered ID and a description that is not "boring".
Return the result table ordered by rating in descending order. The query result format is in the following example.
 
Example 1:
Input: 
```table
Cinema table:
+----+------------+-------------+--------+
| id | movie      | description | rating |
+----+------------+-------------+--------+
| 1  | War        | great 3D    | 8.9    |
| 2  | Science    | fiction     | 8.5    |
| 3  | irish      | boring      | 6.2    |
| 4  | Ice song   | Fantacy     | 8.6    |
| 5  | House card | Interesting | 9.1    |
+----+------------+-------------+--------+
Output: 
+----+------------+-------------+--------+
| id | movie      | description | rating |
+----+------------+-------------+--------+
| 5  | House card | Interesting | 9.1    |
| 1  | War        | great 3D    | 8.9    |
+----+------------+-------------+--------+
```
Explanation: 
We have three movies with odd-numbered IDs: 1, 3, and 5. The movie with ID = 3 is boring so we do not include it in the answer.
```sql
SELECT * FROM Cinema 
WHERE id % 2 != 0 AND description NOT IN ("boring") 
ORDER BY rating DESC
```
#### Write an SQL query to swap all 'f' and 'm' values (i.e., change all 'f' values to 'm' and vice versa) with a single update statement and no intermediate temporary tables.
Note that you must write a single update statement, do not write any select statement for this problem. The query result format is in the following example.
 
Example 1:
Input: 
```table
Salary table:
+----+------+-----+--------+
| id | name | sex | salary |
+----+------+-----+--------+
| 1  | A    | m   | 2500   |
| 2  | B    | f   | 1500   |
| 3  | C    | m   | 5500   |
| 4  | D    | f   | 500    |
+----+------+-----+--------+
Output: 
+----+------+-----+--------+
| id | name | sex | salary |
+----+------+-----+--------+
| 1  | A    | f   | 2500   |
| 2  | B    | m   | 1500   |
| 3  | C    | f   | 5500   |
| 4  | D    | m   | 500    |
+----+------+-----+--------+
```
Explanation: 
(1, A) and (3, C) were changed from 'm' to 'f'.
(2, B) and (4, D) were changed from 'f' to 'm'.
```sql
UPDATE Salary 
SET sex = CASE sex WHEN 'f' THEN 'm' ELSE 'f' END
```
#### Write a SQL query for a report that provides the pairs (actor_id, director_id) where the actor has cooperated with the director at least three times.
Return the result table in any order. The query result format is in the following example.
 
Example 1:
Input: 
```table
ActorDirector table:
+-------------+-------------+-------------+
| actor_id    | director_id | timestamp   |
+-------------+-------------+-------------+
| 1           | 1           | 0           |
| 1           | 1           | 1           |
| 1           | 1           | 2           |
| 1           | 2           | 3           |
| 1           | 2           | 4           |
| 2           | 1           | 5           |
| 2           | 1           | 6           |
+-------------+-------------+-------------+
Output: 
+-------------+-------------+
| actor_id    | director_id |
+-------------+-------------+
| 1           | 1           |
+-------------+-------------+
```
Explanation: The only pair is (1, 1) where they cooperated exactly 3 times.
```sql
SELECT actor_id, director_id 
FROM ActorDirector 
GROUP BY actor_id, director_id 
HAVING COUNT(*) >= 3
```
#### Write an SQL query that reports the products that were only sold in the first quarter of 2019. That is, between 2019-01-01 and 2019-03-31 inclusive.
Return the result table in any order. The query result format is in the following example.
 
Example 1:
Input: 
```table
Product table:
+------------+--------------+------------+
| product_id | product_name | unit_price |
+------------+--------------+------------+
| 1          | S8           | 1000       |
| 2          | G4           | 800        |
| 3          | iPhone       | 1400       |
+------------+--------------+------------+
Sales table:
+-----------+------------+----------+------------+----------+-------+
| seller_id | product_id | buyer_id | sale_date  | quantity | price |
+-----------+------------+----------+------------+----------+-------+
| 1         | 1          | 1        | 2019-01-21 | 2        | 2000  |
| 1         | 2          | 2        | 2019-02-17 | 1        | 800   |
| 2         | 2          | 3        | 2019-06-02 | 1        | 800   |
| 3         | 3          | 4        | 2019-05-13 | 2        | 2800  |
+-----------+------------+----------+------------+----------+-------+
Output: 
+-------------+--------------+
| product_id  | product_name |
+-------------+--------------+
| 1           | S8           |
+-------------+--------------+
```
Explanation: 
The product with id 1 was only sold in the spring of 2019.
The product with id 2 was sold in the spring of 2019 but was also sold after the spring of 2019.
The product with id 3 was sold after spring 2019.
We return only product 1 as it is the product that was only sold in the spring of 2019.
```sql
SELECT DISTINCT p.product_id, p.product_name 
FROM Product p 
INNER JOIN Sales s 
ON p.product_id = s.product_id 
AND s.sale_date BETWEEN "2019-01-01" AND "2019-03-31" 
AND p.product_id NOT IN (
    SELECT p2.product_id 
    FROM Product p2 
    INNER JOIN Sales s 
    ON p.product_id = s.product_id
    WHERE s.sale_date < "2019-01-01" OR s.sale_date > "2019-03-31"
)
```
#### Write an SQL query to find the daily active user count for a period of 30 days ending 2019-07-27 inclusively. 
A user was active on someday if they made at least one activity on that day. Return the result table in any order. The query result format is in the following example.
 
Example 1:
Input: 
```table
Activity table:
+---------+------------+---------------+---------------+
| user_id | session_id | activity_date | activity_type |
+---------+------------+---------------+---------------+
| 1       | 1          | 2019-07-20    | open_session  |
| 1       | 1          | 2019-07-20    | scroll_down   |
| 1       | 1          | 2019-07-20    | end_session   |
| 2       | 4          | 2019-07-20    | open_session  |
| 2       | 4          | 2019-07-21    | send_message  |
| 2       | 4          | 2019-07-21    | end_session   |
| 3       | 2          | 2019-07-21    | open_session  |
| 3       | 2          | 2019-07-21    | send_message  |
| 3       | 2          | 2019-07-21    | end_session   |
| 4       | 3          | 2019-06-25    | open_session  |
| 4       | 3          | 2019-06-25    | end_session   |
+---------+------------+---------------+---------------+
Output: 
+------------+--------------+ 
| day        | active_users |
+------------+--------------+ 
| 2019-07-20 | 2            |
| 2019-07-21 | 2            |
+------------+--------------+ 
```
Explanation: Note that we do not care about days with zero active users.
```sql
SELECT activity_date AS day, 
COUNT(DISTINCT user_id) AS active_users 
FROM Activity 
WHERE activity_date BETWEEN "2019-06-28" AND "2019-07-27"
GROUP BY activity_date 
```
#### Write an SQL query to find all the authors that viewed at least one of their own articles.
Return the result table sorted by id in ascending order. The query result format is in the following example.
 
Example 1:
Input: 
```table
Views table:
+------------+-----------+-----------+------------+
| article_id | author_id | viewer_id | view_date  |
+------------+-----------+-----------+------------+
| 1          | 3         | 5         | 2019-08-01 |
| 1          | 3         | 6         | 2019-08-02 |
| 2          | 7         | 7         | 2019-08-01 |
| 2          | 7         | 6         | 2019-08-02 |
| 4          | 7         | 1         | 2019-07-22 |
| 3          | 4         | 4         | 2019-07-21 |
| 3          | 4         | 4         | 2019-07-21 |
+------------+-----------+-----------+------------+
Output: 
+------+
| id   |
+------+
| 4    |
| 7    |
+------+
```
```sql
SELECT DISTINCT author_id AS id 
FROM Views 
WHERE author_id = viewer_id 
ORDER BY id ASC
```
#### Write an SQL query to reformat the table such that there is a department id column and a revenue column for each month.
Return the result table in any order. The query result format is in the following example.
 
Example 1:
Input: 
```table
Department table:
+------+---------+-------+
| id   | revenue | month |
+------+---------+-------+
| 1    | 8000    | Jan   |
| 2    | 9000    | Jan   |
| 3    | 10000   | Feb   |
| 1    | 7000    | Feb   |
| 1    | 6000    | Mar   |
+------+---------+-------+
Output: 
+------+-------------+-------------+-------------+-----+-------------+
| id   | Jan_Revenue | Feb_Revenue | Mar_Revenue | ... | Dec_Revenue |
+------+-------------+-------------+-------------+-----+-------------+
| 1    | 8000        | 7000        | 6000        | ... | null        |
| 2    | 9000        | null        | null        | ... | null        |
| 3    | null        | 10000       | null        | ... | null        |
+------+-------------+-------------+-------------+-----+-------------+
```
Explanation: The revenue from Apr to Dec is null.
Note that the result table has 13 columns (1 for the department id + 12 for the months).
```sql
SELECT id, 
SUM(CASE WHEN month = "Jan" THEN revenue ELSE NULL END) AS Jan_Revenue, 
SUM(CASE WHEN month = "Feb" THEN revenue ELSE NULL END) AS Feb_Revenue, 
SUM(CASE WHEN month = "Mar" THEN revenue ELSE NULL END) AS Mar_Revenue, 
SUM(CASE WHEN month = "Apr" THEN revenue ELSE NULL END) AS Apr_Revenue,
SUM(CASE WHEN month = "May" THEN revenue ELSE NULL END) AS May_Revenue,
SUM(CASE WHEN month = "Jun" THEN revenue ELSE NULL END) AS Jun_Revenue,
SUM(CASE WHEN month = "Jul" THEN revenue ELSE NULL END) AS Jul_Revenue,
SUM(CASE WHEN month = "Aug" THEN revenue ELSE NULL END) AS Aug_Revenue,
SUM(CASE WHEN month = "Sep" THEN revenue ELSE NULL END) AS Sep_Revenue,
SUM(CASE WHEN month = "Oct" THEN revenue ELSE NULL END) AS Oct_Revenue,
SUM(CASE WHEN month = "Nov" THEN revenue ELSE NULL END) AS Nov_Revenue,
SUM(CASE WHEN month = "Dec" THEN revenue ELSE NULL END) AS Dec_Revenue 
FROM Department
GROUP BY id
```
#### Write an SQL query to report the distance traveled by each user.
Return the result table ordered by travelled_distance in descending order, if two or more users traveled the same distance, order them by their name in ascending order. The query result format is in the following example.
 
Example 1:
Input: 
```table
Users table:
+------+-----------+
| id   | name      |
+------+-----------+
| 1    | Alice     |
| 2    | Bob       |
| 3    | Alex      |
| 4    | Donald    |
| 7    | Lee       |
| 13   | Jonathan  |
| 19   | Elvis     |
+------+-----------+
Rides table:
+------+----------+----------+
| id   | user_id  | distance |
+------+----------+----------+
| 1    | 1        | 120      |
| 2    | 2        | 317      |
| 3    | 3        | 222      |
| 4    | 7        | 100      |
| 5    | 13       | 312      |
| 6    | 19       | 50       |
| 7    | 7        | 120      |
| 8    | 19       | 400      |
| 9    | 7        | 230      |
+------+----------+----------+
Output: 
+----------+--------------------+
| name     | travelled_distance |
+----------+--------------------+
| Elvis    | 450                |
| Lee      | 450                |
| Bob      | 317                |
| Jonathan | 312                |
| Alex     | 222                |
| Alice    | 120                |
| Donald   | 0                  |
+----------+--------------------+
```
Explanation: 
Elvis and Lee traveled 450 miles, Elvis is the top traveler as his name is alphabetically smaller than Lee.
Bob, Jonathan, Alex, and Alice have only one ride and we just order them by the total distances of the ride.
Donald did not have any rides, the distance traveled by him is 0.
```sql
SELECT u.name, 
SUM(CASE WHEN r.distance THEN r.distance ELSE 0 END) AS travelled_distance 
FROM Users u LEFT JOIN Rides r 
ON u.id = r.user_id 
GROUP BY r.user_id 
ORDER BY travelled_distance DESC, name ASC
```
#### Write an SQL query to find for each date the number of different products sold and their names.
The sold products names for each date should be sorted lexicographically. Return the result table ordered by sell_date. The query result format is in the following example.
 
Example 1:
Input: 
```table
Activities table:
+------------+------------+
| sell_date  | product     |
+------------+------------+
| 2020-05-30 | Headphone  |
| 2020-06-01 | Pencil     |
| 2020-06-02 | Mask       |
| 2020-05-30 | Basketball |
| 2020-06-01 | Bible      |
| 2020-06-02 | Mask       |
| 2020-05-30 | T-Shirt    |
+------------+------------+
Output: 
+------------+----------+------------------------------+
| sell_date  | num_sold | products                     |
+------------+----------+------------------------------+
| 2020-05-30 | 3        | Basketball,Headphone,T-shirt |
| 2020-06-01 | 2        | Bible,Pencil                 |
| 2020-06-02 | 1        | Mask                         |
+------------+----------+------------------------------+
```
Explanation: 
For 2020-05-30, Sold items were (Headphone, Basketball, T-shirt), we sort them lexicographically and separate them by a comma.
For 2020-06-01, Sold items were (Pencil, Bible), we sort them lexicographically and separate them by a comma.
For 2020-06-02, the Sold item is (Mask), we just return it.
```sql
SELECT sell_date, 
COUNT(DISTINCT product) AS num_sold, 
GROUP_CONCAT(DISTINCT product ORDER BY product ASC SEPARATOR ',') AS products 
FROM Activities 
GROUP BY sell_date
ORDER BY sell_date
```
#### Write an SQL query to report the patient_id, patient_name all conditions of patients who have Type I Diabetes. 
Type I Diabetes always starts with DIAB1 prefix. Return the result table in any order. The query result format is in the following example.
 
Example 1:
Input: 
```table
Patients table:
+------------+--------------+--------------+
| patient_id | patient_name | conditions   |
+------------+--------------+--------------+
| 1          | Daniel       | YFEV COUGH   |
| 2          | Alice        |              |
| 3          | Bob          | DIAB100 MYOP |
| 4          | George       | ACNE DIAB100 |
| 5          | Alain        | DIAB201      |
+------------+--------------+--------------+
Output: 
+------------+--------------+--------------+
| patient_id | patient_name | conditions   |
+------------+--------------+--------------+
| 3          | Bob          | DIAB100 MYOP |
| 4          | George       | ACNE DIAB100 | 
+------------+--------------+--------------+
```
Explanation: Bob and George both have a condition that starts with DIAB1.
```sql
SELECT * FROM Patients 
WHERE conditions LIKE "% DIAB1%" OR conditions LIKE "DIAB1%"
```
#### Write an SQL query to find the IDs of the users who visited without making any transactions and the number of times they made these types of visits.
Return the result table sorted in any order. The query result format is in the following example.
 
Example 1:
Input: 
```table
Visits
+----------+-------------+
| visit_id | customer_id |
+----------+-------------+
| 1        | 23          |
| 2        | 9           |
| 4        | 30          |
| 5        | 54          |
| 6        | 96          |
| 7        | 54          |
| 8        | 54          |
+----------+-------------+
Transactions
+----------------+----------+--------+
| transaction_id | visit_id | amount |
+----------------+----------+--------+
| 2              | 5        | 310    |
| 3              | 5        | 300    |
| 9              | 5        | 200    |
| 12             | 1        | 910    |
| 13             | 2        | 970    |
+----------------+----------+--------+
Output: 
+-------------+----------------+
| customer_id | count_no_trans |
+-------------+----------------+
| 54          | 2              |
| 30          | 1              |
| 96          | 1              |
+-------------+----------------+
```
Explanation: 
Customer with id = 23 visited the mall once and made one transaction during the visit with id = 12.
Customer with id = 9 visited the mall once and made one transaction during the visit with id = 13.
Customer with id = 30 visited the mall once and did not make any transactions.
Customer with id = 54 visited the mall three times. During 2 visits they did not make any transactions, and during one visit they made 3 transactions.
Customer with id = 96 visited the mall once and did not make any transactions.
As we can see, users with IDs 30 and 96 visited the mall one time without making any transactions. 
Also, user 54 visited the mall twice and did not make any transactions.
```sql
SELECT customer_id, 
COUNT(visit_id) AS count_no_trans 
FROM Visits  
WHERE visit_id NOT IN (
    SELECT visit_id FROM Transactions
)
GROUP BY customer_id
```
#### Write an SQL query to report the name and balance of users with a balance higher than 10000. 
The balance of an account is equal to the sum of the amounts of all transactions involving that account. Return the result table in any order. The query result format is in the following example.
 
Example 1:
Input: 
```table
Users table:
+------------+--------------+
| account    | name         |
+------------+--------------+
| 900001     | Alice        |
| 900002     | Bob          |
| 900003     | Charlie      |
+------------+--------------+
Transactions table:
+------------+------------+------------+---------------+
| trans_id   | account    | amount     | transacted_on |
+------------+------------+------------+---------------+
| 1          | 900001     | 7000       |  2020-08-01   |
| 2          | 900001     | 7000       |  2020-09-01   |
| 3          | 900001     | -3000      |  2020-09-02   |
| 4          | 900002     | 1000       |  2020-09-12   |
| 5          | 900003     | 6000       |  2020-08-07   |
| 6          | 900003     | 6000       |  2020-09-07   |
| 7          | 900003     | -4000      |  2020-09-11   |
+------------+------------+------------+---------------+
Output: 
+------------+------------+
| name       | balance    |
+------------+------------+
| Alice      | 11000      |
+------------+------------+
```
Explanation: 
Alice's balance is (7000 + 7000 - 3000) = 11000.
Bob's balance is 1000.
Charlie's balance is (6000 + 6000 - 4000) = 8000.
```sql
SELECT u.name, 
SUM(t.amount) AS balance 
FROM Users u 
INNER JOIN Transactions t 
ON u.account = t.account  
GROUP BY t.account 
HAVING balance > 10000
```
#### Write an SQL query to fix the names so that only the first character is uppercase and the rest are lowercase.
Return the result table ordered by user_id. The query result format is in the following example.
 
Example 1:
Input:
```table
Users table:
+---------+-------+
| user_id | name  |
+---------+-------+
| 1       | aLice |
| 2       | bOB   |
+---------+-------+
Output: 
+---------+-------+
| user_id | name  |
+---------+-------+
| 1       | Alice |
| 2       | Bob   |
+---------+-------+
```
```sql
SELECT user_id, 
CONCAT(UPPER(SUBSTR(name, 1, 1)), LOWER(SUBSTR(name, 2))) AS name 
FROM Users 
ORDER BY user_id ASC
```
#### Write an SQL query that will, for each date_id and make_name, return the number of distinct lead_id's and distinct partner_id's.
Return the result table in any order. The query result format is in the following example.
 
Example 1:
Input: 
```table
DailySales table:
+-----------+-----------+---------+------------+
| date_id   | make_name | lead_id | partner_id |
+-----------+-----------+---------+------------+
| 2020-12-8 | toyota    | 0       | 1          |
| 2020-12-8 | toyota    | 1       | 0          |
| 2020-12-8 | toyota    | 1       | 2          |
| 2020-12-7 | toyota    | 0       | 2          |
| 2020-12-7 | toyota    | 0       | 1          |
| 2020-12-8 | honda     | 1       | 2          |
| 2020-12-8 | honda     | 2       | 1          |
| 2020-12-7 | honda     | 0       | 1          |
| 2020-12-7 | honda     | 1       | 2          |
| 2020-12-7 | honda     | 2       | 1          |
+-----------+-----------+---------+------------+
Output: 
+-----------+-----------+--------------+-----------------+
| date_id   | make_name | unique_leads | unique_partners |
+-----------+-----------+--------------+-----------------+
| 2020-12-8 | toyota    | 2            | 3               |
| 2020-12-7 | toyota    | 1            | 2               |
| 2020-12-8 | honda     | 2            | 2               |
| 2020-12-7 | honda     | 3            | 2               |
+-----------+-----------+--------------+-----------------+
```
Explanation: 
For 2020-12-8, toyota gets leads = [0, 1] and partners = [0, 1, 2] while honda gets leads = [1, 2] and partners = [1, 2].
For 2020-12-7, toyota gets leads = [0] and partners = [1, 2] while honda gets leads = [0, 1, 2] and partners = [1, 2].
```sql
SELECT date_id, make_name, 
COUNT(DISTINCT lead_id) AS unique_leads, 
COUNT(DISTINCT partner_id) AS unique_partners 
FROM DailySales 
GROUP BY date_id, make_name
```
#### Write an SQL query that will, for each user, return the number of followers.
Return the result table ordered by user_id. The query result format is in the following example.
 
Example 1:
Input: 
```table
Followers table:
+---------+-------------+
| user_id | follower_id |
+---------+-------------+
| 0       | 1           |
| 1       | 0           |
| 2       | 0           |
| 2       | 1           |
+---------+-------------+
Output: 
+---------+----------------+
| user_id | followers_count|
+---------+----------------+
| 0       | 1              |
| 1       | 1              |
| 2       | 2              |
+---------+----------------+
```
Explanation: 
The followers of 0 are {1}
The followers of 1 are {0}
The followers of 2 are {0,1}
```sql
SELECT user_id, 
COUNT(follower_id) AS followers_count 
FROM Followers 
GROUP BY user_id 
ORDER BY user_id
```
#### Write an SQL query to calculate the total time in minutes spent by each employee on each day at the office. 
Note that within one day, an employee can enter and leave more than once. The time spent in the office for a single entry is out_time - in_time. Return the result table in any order. The query result format is in the following example.
 
Example 1:
Input: 
```table
Employees table:
+--------+------------+---------+----------+
| emp_id | event_day  | in_time | out_time |
+--------+------------+---------+----------+
| 1      | 2020-11-28 | 4       | 32       |
| 1      | 2020-11-28 | 55      | 200      |
| 1      | 2020-12-03 | 1       | 42       |
| 2      | 2020-11-28 | 3       | 33       |
| 2      | 2020-12-09 | 47      | 74       |
+--------+------------+---------+----------+
Output: 
+------------+--------+------------+
| day        | emp_id | total_time |
+------------+--------+------------+
| 2020-11-28 | 1      | 173        |
| 2020-11-28 | 2      | 30         |
| 2020-12-03 | 1      | 41         |
| 2020-12-09 | 2      | 27         |
+------------+--------+------------+
```
Explanation: 
Employee 1 has three events: two on day 2020-11-28 with a total of (32 - 4) + (200 - 55) = 173, and one on day 2020-12-03 with a total of (42 - 1) = 41.
Employee 2 has two events: one on day 2020-11-28 with a total of (33 - 3) = 30, and one on day 2020-12-09 with a total of (74 - 47) = 27.
```sql
SELECT event_day AS day, 
emp_id, 
SUM((out_time - in_time)) AS total_time 
FROM Employees 
GROUP BY emp_id, event_day
```
#### Write an SQL query to find the ids of products that are both low fat and recyclable.
Return the result table in any order. The query result format is in the following example.
 
Example 1:
Input:
```table
Products table:
+-------------+----------+------------+
| product_id  | low_fats | recyclable |
+-------------+----------+------------+
| 0           | Y        | N          |
| 1           | Y        | Y          |
| 2           | N        | Y          |
| 3           | Y        | Y          |
| 4           | N        | N          |
+-------------+----------+------------+
Output: 
+-------------+
| product_id  |
+-------------+
| 1           |
| 3           |
+-------------+
```
Explanation: Only products 1 and 3 are both low fat and recyclable.
```sql
SELECT product_id 
FROM Products 
WHERE low_fats = 'Y' AND recyclable = 'Y'
```
#### Write an SQL query to rearrange the Products table so that each row has (product_id, store, price). 
If a product is not available in a store, do not include a row with that product_id and store combination in the result table. Return the result table in any order. The query result format is in the following example.
 
Example 1:
Input: 
```table
Products table:
+------------+--------+--------+--------+
| product_id | store1 | store2 | store3 |
+------------+--------+--------+--------+
| 0          | 95     | 100    | 105    |
| 1          | 70     | null   | 80     |
+------------+--------+--------+--------+
Output: 
+------------+--------+-------+
| product_id | store  | price |
+------------+--------+-------+
| 0          | store1 | 95    |
| 0          | store2 | 100   |
| 0          | store3 | 105   |
| 1          | store1 | 70    |
| 1          | store3 | 80    |
+------------+--------+-------+
```
Explanation: 
Product 0 is available in all three stores with prices 95, 100, and 105 respectively.
Product 1 is available in store1 with price 70 and store3 with price 80. The product is not available in store2.
```sql
SELECT product_id, 'store1' AS store, store1 AS price 
FROM Products 
WHERE store1 IS NOT NULL 
UNION 
SELECT product_id, 'store2' AS store, store2 AS price 
FROM Products 
WHERE store2 IS NOT NULL
UNION 
SELECT product_id, 'store3' AS store, store3 AS price 
FROM Products 
WHERE store3 IS NOT NULL
```
#### Write an SQL query to calculate the bonus of each employee. 
The bonus of an employee is 100% of their salary if the ID of the employee is an odd number and the employee name does not start with the character 'M'. The bonus of an employee is 0 otherwise. Return the result table ordered by employee_id. The query result format is in the following example.
 
Example 1:
Input: 
```table
Employees table:
+-------------+---------+--------+
| employee_id | name    | salary |
+-------------+---------+--------+
| 2           | Meir    | 3000   |
| 3           | Michael | 3800   |
| 7           | Addilyn | 7400   |
| 8           | Juan    | 6100   |
| 9           | Kannon  | 7700   |
+-------------+---------+--------+
Output: 
+-------------+-------+
| employee_id | bonus |
+-------------+-------+
| 2           | 0     |
| 3           | 0     |
| 7           | 7400  |
| 8           | 0     |
| 9           | 7700  |
+-------------+-------+
```
Explanation: 
The employees with IDs 2 and 8 get 0 bonus because they have an even employee_id.
The employee with ID 3 gets 0 bonus because their name starts with 'M'.
The rest of the employees get a 100% bonus.
```sql
SELECT employee_id, 
CASE WHEN employee_id % 2 != 0 AND name NOT LIKE 'M%' THEN salary 
     ELSE 0 
END AS bonus 
FROM Employees 
ORDER BY employee_id
```
#### Write an SQL query to report the latest login for all users in the year 2020. Do not include the users who did not login in 2020.
Return the result table in any order. The query result format is in the following example.
 
Example 1:
Input: 
```table
Logins table:
+---------+---------------------+
| user_id | time_stamp          |
+---------+---------------------+
| 6       | 2020-06-30 15:06:07 |
| 6       | 2021-04-21 14:06:06 |
| 6       | 2019-03-07 00:18:15 |
| 8       | 2020-02-01 05:10:53 |
| 8       | 2020-12-30 00:46:50 |
| 2       | 2020-01-16 02:49:50 |
| 2       | 2019-08-25 07:59:08 |
| 14      | 2019-07-14 09:00:00 |
| 14      | 2021-01-06 11:59:59 |
+---------+---------------------+
Output: 
+---------+---------------------+
| user_id | last_stamp          |
+---------+---------------------+
| 6       | 2020-06-30 15:06:07 |
| 8       | 2020-12-30 00:46:50 |
| 2       | 2020-01-16 02:49:50 |
+---------+---------------------+
```
Explanation: 
User 6 logged into their account 3 times but only once in 2020, so we include this login in the result table.
User 8 logged into their account 2 times in 2020, once in February and once in December. We include only the latest one (December) in the result table.
User 2 logged into their account 2 times but only once in 2020, so we include this login in the result table.
User 14 did not login in 2020, so we do not include them in the result table.
```sql
SELECT user_id, MAX(time_stamp) AS last_stamp 
FROM Logins 
WHERE time_stamp LIKE '2020%'
GROUP BY user_id
```
#### Write an SQL query to report the IDs of all the employees with missing information. The information of an employee is missing if:
The employee's name is missing, or The employee's salary is missing. Return the result table ordered by employee_id in ascending order. The query result format is in the following example.
 
Example 1:
Input: 
```table
Employees table:
+-------------+----------+
| employee_id | name     |
+-------------+----------+
| 2           | Crew     |
| 4           | Haven    |
| 5           | Kristian |
+-------------+----------+
Salaries table:
+-------------+--------+
| employee_id | salary |
+-------------+--------+
| 5           | 76071  |
| 1           | 22517  |
| 4           | 63539  |
+-------------+--------+
Output: 
+-------------+
| employee_id |
+-------------+
| 1           |
| 2           |
+-------------+
```
Explanation: 
Employees 1, 2, 4, and 5 are working at this company.
The name of employee 1 is missing.
The salary of employee 2 is missing.
```sql
SELECT employee_id 
FROM Employees 
WHERE employee_id NOT IN (
    SELECT employee_id FROM Salaries
)
UNION 
SELECT employee_id 
FROM Salaries 
WHERE employee_id NOT IN (
    SELECT employee_id FROM Employees
)
ORDER BY employee_id
```
#### Write an SQL query to report the second highest salary from the Employee table.
If there is no second highest salary, the query should report null. The query result format is in the following example.
 
Example 1:
Input: 
```table
Employee table:
+----+--------+
| id | salary |
+----+--------+
| 1  | 100    |
| 2  | 200    |
| 3  | 300    |
+----+--------+
Output: 
+---------------------+
| SecondHighestSalary |
+---------------------+
| 200                 |
+---------------------+
```
Example 2:
Input: 
```table
Employee table:
+----+--------+
| id | salary |
+----+--------+
| 1  | 100    |
+----+--------+
Output: 
+---------------------+
| SecondHighestSalary |
+---------------------+
| null                |
+---------------------+
```
```sql
SELECT MAX(salary) AS SecondHighestSalary 
FROM Employee 
WHERE salary NOT IN (
    SELECT MAX(salary) FROM Employee
)
```
#### Write an SQL query to report the nth highest salary from the Employee table.
If there is no nth highest salary, the query should report null. The query result format is in the following example.
 
Example 1:
Input: 
```table
Employee table:
+----+--------+
| id | salary |
+----+--------+
| 1  | 100    |
| 2  | 200    |
| 3  | 300    |
+----+--------+
n = 2
Output: 
+------------------------+
| getNthHighestSalary(2) |
+------------------------+
| 200                    |
+------------------------+
```
Example 2:
Input: 
```table
Employee table:
+----+--------+
| id | salary |
+----+--------+
| 1  | 100    |
+----+--------+
n = 2
Output: 
+------------------------+
| getNthHighestSalary(2) |
+------------------------+
| null                   |
+------------------------+
```
```sql
CREATE FUNCTION getNthHighestSalary(N INT) RETURNS INT
BEGIN
SET N = N - 1;
  RETURN (
      SELECT DISTINCT salary FROM Employee 
      ORDER BY salary DESC LIMIT N, 1
  );
END
```
#### Write an SQL query to rank the scores.
The ranking should be calculated according to the following rules:
The scores should be ranked from the highest to the lowest.
If there is a tie between two scores, both should have the same ranking.
After a tie, the next ranking number should be the next consecutive integer value.
In other words, there should be no holes between ranks.
Return the result table ordered by score in descending order. The query result format is in the following example.
 
Example 1:
Input: 
```table
Scores table:
+----+-------+
| id | score |
+----+-------+
| 1  | 3.50  |
| 2  | 3.65  |
| 3  | 4.00  |
| 4  | 3.85  |
| 5  | 4.00  |
| 6  | 3.65  |
+----+-------+
Output: 
+-------+------+
| score | rank |
+-------+------+
| 4.00  | 1    |
| 4.00  | 1    |
| 3.85  | 2    |
| 3.65  | 3    |
| 3.65  | 3    |
| 3.50  | 4    |
+-------+------+
```
```sql
SELECT score, 
DENSE_RANK() OVER(ORDER BY score DESC) AS 'rank' 
FROM Scores
```
#### Write an SQL query to find all numbers that appear at least three times consecutively.
Return the result table in any order. The query result format is in the following example.
 
Example 1:
Input: 
```table
Logs table:
+----+-----+
| id | num |
+----+-----+
| 1  | 1   |
| 2  | 1   |
| 3  | 1   |
| 4  | 2   |
| 5  | 1   |
| 6  | 2   |
| 7  | 2   |
+----+-----+
Output: 
+-----------------+
| ConsecutiveNums |
+-----------------+
| 1               |
+-----------------+
```
Explanation: 1 is the only number that appears consecutively for at least three times.
```sql
SELECT DISTINCT l1.num AS ConsecutiveNums 
FROM Logs l1, Logs l2, Logs l3
WHERE l2.id = l1.id - 1 AND l3.id = l2.id - 1 
AND l1.num = l2.num AND l2.num = l3.num
```
#### Write an SQL query to find employees who have the highest salary in each of the departments.
Return the result table in any order. The query result format is in the following example.
 
Example 1:
Input: 
```table
Employee table:
+----+-------+--------+--------------+
| id | name  | salary | departmentId |
+----+-------+--------+--------------+
| 1  | Joe   | 70000  | 1            |
| 2  | Jim   | 90000  | 1            |
| 3  | Henry | 80000  | 2            |
| 4  | Sam   | 60000  | 2            |
| 5  | Max   | 90000  | 1            |
+----+-------+--------+--------------+
Department table:
+----+-------+
| id | name  |
+----+-------+
| 1  | IT    |
| 2  | Sales |
+----+-------+
Output: 
+------------+----------+--------+
| Department | Employee | Salary |
+------------+----------+--------+
| IT         | Jim      | 90000  |
| Sales      | Henry    | 80000  |
| IT         | Max      | 90000  |
+------------+----------+--------+
```
Explanation: Max and Jim both have the highest salary in the IT department and Henry has the highest salary in the Sales department.
```sql
SELECT d.name AS Department, 
e.name AS Employee, Salary 
FROM Employee e JOIN Department d 
ON e.departmentId = d.id 
WHERE (e.departmentId, Salary) IN (
    SELECT departmentId, MAX(salary) AS Salary 
    FROM Employee 
    GROUP BY departmentId
)
```
#### Write an SQL query to report the type of each node in the tree.
Return the result table ordered by id in ascending order. The query result format is in the following example.
Each node in the tree can be one of three types:
"Leaf": if the node is a leaf node.
"Root": if the node is the root of the tree.
"Inner": If the node is neither a leaf node nor a root node.

Example 1:
Input: 
```table
Tree table:
+----+------+
| id | p_id |
+----+------+
| 1  | null |
| 2  | 1    |
| 3  | 1    |
| 4  | 2    |
| 5  | 2    |
+----+------+
Output: 
+----+-------+
| id | type  |
+----+-------+
| 1  | Root  |
| 2  | Inner |
| 3  | Leaf  |
| 4  | Leaf  |
| 5  | Leaf  |
+----+-------+
```
Explanation: 
Node 1 is the root node because its parent node is null and it has child nodes 2 and 3.
Node 2 is an inner node because it has parent node 1 and child node 4 and 5.
Nodes 3, 4, and 5 are leaf nodes because they have parent nodes and they do not have child nodes.

Example 2:
Input: 
```table
Tree table:
+----+------+
| id | p_id |
+----+------+
| 1  | null |
+----+------+
Output: 
+----+-------+
| id | type  |
+----+-------+
| 1  | Root  |
+----+-------+
```
Explanation: If there is only one node on the tree, you only need to output its root attributes.
```sql
SELECT id, 
CASE WHEN p_id IS NULL THEN 'Root' 
     WHEN p_id IS NOT NULL AND id IN (SELECT p_id FROM Tree) THEN 'Inner' 
     ELSE 'Leaf' 
END AS type 
FROM Tree
```
#### Write an SQL query to swap the seat id of every two consecutive students.
If the number of students is odd, the id of the last student is not swapped. Return the result table ordered by id in ascending order. The query result format is in the following example.
 
Example 1:
Input: 
```table
Seat table:
+----+---------+
| id | student |
+----+---------+
| 1  | Abbot   |
| 2  | Doris   |
| 3  | Emerson |
| 4  | Green   |
| 5  | Jeames  |
+----+---------+
Output: 
+----+---------+
| id | student |
+----+---------+
| 1  | Doris   |
| 2  | Abbot   |
| 3  | Green   |
| 4  | Emerson |
| 5  | Jeames  |
+----+---------+
```
Explanation: 
Note that if the number of students is odd, there is no need to change the last one's seat.
```sql
SELECT CASE
        WHEN id % 2 = 0 THEN id - 1 
        WHEN id % 2 = 1 AND id != (SELECT MAX(id) FROM Seat) THEN id + 1 
        ELSE id 
END AS id, student 
FROM Seat
ORDER BY id
```
#### Write an SQL query to find for each user, the join date and the number of orders they made as a buyer in 2019.
Return the result table in any order. The query result format is in the following example.
 
Example 1:
Input: 
```table
Users table:
+---------+------------+----------------+
| user_id | join_date  | favorite_brand |
+---------+------------+----------------+
| 1       | 2018-01-01 | Lenovo         |
| 2       | 2018-02-09 | Samsung        |
| 3       | 2018-01-19 | LG             |
| 4       | 2018-05-21 | HP             |
+---------+------------+----------------+
Orders table:
+----------+------------+---------+----------+-----------+
| order_id | order_date | item_id | buyer_id | seller_id |
+----------+------------+---------+----------+-----------+
| 1        | 2019-08-01 | 4       | 1        | 2         |
| 2        | 2018-08-02 | 2       | 1        | 3         |
| 3        | 2019-08-03 | 3       | 2        | 3         |
| 4        | 2018-08-04 | 1       | 4        | 2         |
| 5        | 2018-08-04 | 1       | 3        | 4         |
| 6        | 2019-08-05 | 2       | 2        | 4         |
+----------+------------+---------+----------+-----------+
Items table:
+---------+------------+
| item_id | item_brand |
+---------+------------+
| 1       | Samsung    |
| 2       | Lenovo     |
| 3       | LG         |
| 4       | HP         |
+---------+------------+
Output: 
+-----------+------------+----------------+
| buyer_id  | join_date  | orders_in_2019 |
+-----------+------------+----------------+
| 1         | 2018-01-01 | 1              |
| 2         | 2018-02-09 | 2              |
| 3         | 2018-01-19 | 0              |
| 4         | 2018-05-21 | 0              |
+-----------+------------+----------------+
```
```sql
SELECT u.user_id AS buyer_id, u.join_date, 
COUNT(o.order_id) AS orders_in_2019 
FROM Users u LEFT JOIN Orders o 
ON u.user_id = o.buyer_id AND YEAR(o.order_date) = '2019'
GROUP BY u.user_id
```
#### Write an SQL query to report the Capital gain/loss for each stock.
The Capital gain/loss of a stock is the total gain or loss after buying and selling the stock one or many times. Return the result table in any order. The query result format is in the following example.
 
Example 1:
Input: 
```table
Stocks table:
+---------------+-----------+---------------+--------+
| stock_name    | operation | operation_day | price  |
+---------------+-----------+---------------+--------+
| Leetcode      | Buy       | 1             | 1000   |
| Corona Masks  | Buy       | 2             | 10     |
| Leetcode      | Sell      | 5             | 9000   |
| Handbags      | Buy       | 17            | 30000  |
| Corona Masks  | Sell      | 3             | 1010   |
| Corona Masks  | Buy       | 4             | 1000   |
| Corona Masks  | Sell      | 5             | 500    |
| Corona Masks  | Buy       | 6             | 1000   |
| Handbags      | Sell      | 29            | 7000   |
| Corona Masks  | Sell      | 10            | 10000  |
+---------------+-----------+---------------+--------+
Output: 
+---------------+-------------------+
| stock_name    | capital_gain_loss |
+---------------+-------------------+
| Corona Masks  | 9500              |
| Leetcode      | 8000              |
| Handbags      | -23000            |
+---------------+-------------------+
```
Explanation: 
Leetcode stock was bought at day 1 for 1000$ and was sold at day 5 for 9000$. Capital gain = 9000 - 1000 = 8000$.
Handbags stock was bought at day 17 for 30000$ and was sold at day 29 for 7000$. Capital loss = 7000 - 30000 = -23000$.
Corona Masks stock was bought at day 1 for 10$ and was sold at day 3 for 1010$. It was bought again at day 4 for 1000$ and was sold at day 5 for 500$. 
At last, it was bought at day 6 for 1000$ and was sold at day 10 for 10000$. 
Capital gain/loss is the sum of capital gains/losses for each 
('Buy' --> 'Sell') operation = (1010 - 10) + (500 - 1000) + (10000 - 1000) = 1000 - 500 + 9000 = 9500$.
```sql
SELECT stock_name, SUM(
    CASE WHEN operation = 'Buy' THEN -price 
         ELSE price 
    END
) AS capital_gain_loss 
FROM Stocks 
GROUP BY stock_name
```
#### Write an SQL query to find the employees who are high earners in each of the departments.
Return the result table in any order. The query result format is in the following example.
A company's executives are interested in seeing who earns the most money in each of the company's departments. 
A high earner in a department is an employee who has a salary in the top three unique salaries for that department.

Example 1:
Input: 
```table
Employee table:
+----+-------+--------+--------------+
| id | name  | salary | departmentId |
+----+-------+--------+--------------+
| 1  | Joe   | 85000  | 1            |
| 2  | Henry | 80000  | 2            |
| 3  | Sam   | 60000  | 2            |
| 4  | Max   | 90000  | 1            |
| 5  | Janet | 69000  | 1            |
| 6  | Randy | 85000  | 1            |
| 7  | Will  | 70000  | 1            |
+----+-------+--------+--------------+
Department table:
+----+-------+
| id | name  |
+----+-------+
| 1  | IT    |
| 2  | Sales |
+----+-------+
Output: 
+------------+----------+--------+
| Department | Employee | Salary |
+------------+----------+--------+
| IT         | Max      | 90000  |
| IT         | Joe      | 85000  |
| IT         | Randy    | 85000  |
| IT         | Will     | 70000  |
| Sales      | Henry    | 80000  |
| Sales      | Sam      | 60000  |
+------------+----------+--------+
```
Explanation: 
In the IT department:
- Max earns the highest unique salary
- Both Randy and Joe earn the second-highest unique salary
- Will earns the third-highest unique salary

In the Sales department:
- Henry earns the highest salary
- Sam earns the second-highest salary
- There is no third-highest salary as there are only two employees
```sql
SELECT d1.name AS Department, 
e1.name AS Employee, 
e1.salary AS Salary 
FROM Department d1, Employee e1 
WHERE d1.id = e1.departmentId 
AND 3 > (
    SELECT COUNT(DISTINCT e2.salary) 
    FROM Employee e2 
    WHERE e2.departmentId = e1.departmentId 
    AND e2.salary > e1.salary
)
```
#### Write a SQL query to find the cancellation rate of requests with unbanned users (both client and driver must not be banned) each day between "2013-10-01" and "2013-10-03". Round Cancellation Rate to two decimal points.
Return the result table in any order. The query result format is in the following example.
The cancellation rate is computed by dividing the number of canceled (by client or driver) 
requests with unbanned users by the total number of requests with unbanned users on that day.

Example 1:
Input: 
```table
Trips table:
+----+-----------+-----------+---------+---------------------+------------+
| id | client_id | driver_id | city_id | status              | request_at |
+----+-----------+-----------+---------+---------------------+------------+
| 1  | 1         | 10        | 1       | completed           | 2013-10-01 |
| 2  | 2         | 11        | 1       | cancelled_by_driver | 2013-10-01 |
| 3  | 3         | 12        | 6       | completed           | 2013-10-01 |
| 4  | 4         | 13        | 6       | cancelled_by_client | 2013-10-01 |
| 5  | 1         | 10        | 1       | completed           | 2013-10-02 |
| 6  | 2         | 11        | 6       | completed           | 2013-10-02 |
| 7  | 3         | 12        | 6       | completed           | 2013-10-02 |
| 8  | 2         | 12        | 12      | completed           | 2013-10-03 |
| 9  | 3         | 10        | 12      | completed           | 2013-10-03 |
| 10 | 4         | 13        | 12      | cancelled_by_driver | 2013-10-03 |
+----+-----------+-----------+---------+---------------------+------------+
Users table:
+----------+--------+--------+
| users_id | banned | role   |
+----------+--------+--------+
| 1        | No     | client |
| 2        | Yes    | client |
| 3        | No     | client |
| 4        | No     | client |
| 10       | No     | driver |
| 11       | No     | driver |
| 12       | No     | driver |
| 13       | No     | driver |
+----------+--------+--------+
Output: 
+------------+-------------------+
| Day        | Cancellation Rate |
+------------+-------------------+
| 2013-10-01 | 0.33              |
| 2013-10-02 | 0.00              |
| 2013-10-03 | 0.50              |
+------------+-------------------+
```
Explanation: 
On 2013-10-01:
  - There were 4 requests in total, 2 of which were canceled.
  - However, the request with Id=2 was made by a banned client (User_Id=2), so it is ignored in the calculation.
  - Hence there are 3 unbanned requests in total, 1 of which was canceled.
  - The Cancellation Rate is (1 / 3) = 0.33
On 2013-10-02:
  - There were 3 requests in total, 0 of which were canceled.
  - The request with Id=6 was made by a banned client, so it is ignored.
  - Hence there are 2 unbanned requests in total, 0 of which were canceled.
  - The Cancellation Rate is (0 / 2) = 0.00
On 2013-10-03:
  - There were 3 requests in total, 1 of which was canceled.
  - The request with Id=8 was made by a banned client, so it is ignored.
  - Hence there are 2 unbanned request in total, 1 of which were canceled.
  - The Cancellation Rate is (1 / 2) = 0.50
```sql
SELECT t.request_at AS Day, 
ROUND(SUM(t.status != "completed") / COUNT(*), 2) AS 'Cancellation Rate' 
FROM Trips t 
INNER JOIN Users u1 ON t.driver_id = u1.users_id 
INNER JOIN Users u2 ON t.client_id = u2.users_id 
WHERE u1.banned = "No" AND u2.banned = "No" 
AND t.request_at BETWEEN "2013-10-01" AND "2013-10-03" 
GROUP BY t.request_at
```
#### Write an SQL query to display the records with three or more rows with consecutive id's, and the number of people is greater than or equal to 100 for each.
Return the result table ordered by visit_date in ascending order. The query result format is in the following example.
 
Example 1:
Input:
```table
Stadium table:
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
| 8    | 2017-01-09 | 188       |
+------+------------+-----------+
Output: 
+------+------------+-----------+
| id   | visit_date | people    |
+------+------------+-----------+
| 5    | 2017-01-05 | 145       |
| 6    | 2017-01-06 | 1455      |
| 7    | 2017-01-07 | 199       |
| 8    | 2017-01-09 | 188       |
+------+------------+-----------+
```
Explanation: 
The four rows with ids 5, 6, 7, and 8 have consecutive ids and each of them has >= 100 people attended. 
Note that row 8 was included even though the visit_date was not the next day after row 7.
The rows with ids 2 and 3 are not included because we need at least three consecutive ids.
```sql
SELECT DISTINCT s1.* 
FROM Stadium s1, 
Stadium s2, 
Stadium s3
WHERE s1.people >= 100 
AND s2.people >= 100 
AND s3.people >= 100 
AND ((s1.id = s2.id - 1 AND s1.id = s3.id - 2) 
OR (s1.id = s2.id + 1 AND s1.id = s3.id - 1) 
OR (s1.id = s2.id + 2 AND s1.id = s3.id + 1)) 
ORDER BY s1.id
```
