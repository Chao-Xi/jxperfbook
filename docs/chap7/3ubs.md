# **3 UBS interview**

### 1. SQL Datatypes 

Which of the below is **NOT** data type in a SQL database? 

* DATE 
* NUMBER 
* VARCHAR2 
* **STRING** 


**varchar => string(not exist)**


#### 2. SQL joins 

Which of the following JOINs are valid in SQL statements? 


* **LEFT JOIN**
* BOTH JOIN 
* **RIGHT JOIN**
* **FULL JOIN** 
* CROSS JOIN 
* MIX JOIN 
* JOIN 
* **SELF JOIN** 


### 3. SQL DELETE 

Which of the following are correct statements about DELETE? 

* Remove the blocks in database 
* Rollback is not allowed after DELETE 
* Delete entire database 
* Remove column(s) from a table and then commit or rollback 
* **Remove row(s) from a table and then commit or rollback** 


### 4. SQL Select 

Here is EMPLOYEE table 

```
Name 
Annie 
Bob
Callie 
Derek 
```

which of these queries will display the complete contents of the table? 
 

* **Select name from employee** 
* Select name 
* **Select * from employee** 
* Select employee 

 
### 5. SQL equi-joins 

Select all correct answers if the query is equi-join? 

* select * from TABLE1 t1 where t1.id >= 2 
* **select * from TABLE1 t1, TABLE2 t2 where t1.id = t2.id** 
* select * from TABLE1 t1 where t1.id = 2 and t1.id = 45 
* **select * from TABLE2 t2 join TABLE1 t1 on t1.id = t2.id**

```
SELECT column_list 
FROM table1, table2....
WHERE table1.column_name =
table2.column_name; 
```
```
SELECT *
FROM table1 
JOIN table2
[ON (join_condition)]
```


### **6. SQL syntax**

In which sequence need the following elements of an simple SQL query which is not joining tables be? 

1. ORDER BY 
2. GROUP BY 
3. SELECT 
4. WHERE 
5. FROM 
6. HAVING 

* SELECT, HAVING, FROM, WHERE, GROUP BY, ORDER BY 
* SELECT, FROM, WHERE, HAVING, GROUP BY, ORDER BY 
* SELECT, FROM, WHERE, HAVING, ORDER BY, GROUP BY 
* SELECT, FROM, HAVING, WHERE, ORDER BY, GROUP BY 
* **SELECT, FROM, WHERE, GROUP BY, HAVING, ORDER BY**


###  **7. SQL syntax**

![Alt Image Text](../images/chap7_3_1.png "Body image")


Which are the correct SQL statements to extract the names of those who have **at least once won a race**? 

* select winner from races 
* **select distinct winner from races** 
* select unique winner from races 
* select winner from races having winner = UNIQUE 


**8. SQL remove data**

Which of the following SQL statements will delete all rows in a table **without deleting the table? **

* **DELETE FROM table_name;** 
* DELETE TABLE table_name; 
* DROP TABLE table_name;      [删除表结构]  
* **TRUNCATE table_name;**    [清空表结构] 


**9. SQL Trigger**

Is it possible to create trigger on views? 

* **Yes** 
* No 

**10. SQL Trigger**

What are the difference between Trigger and Stored Procedure? Choose which are correct only 

* Trigger is run automatically if the event is occured but Stored Procedure don't run automatically but you have to run it manually 
* Trigger execute implicitly whereas store procedure execute via procedure call from another block. 
* Within a Stored Procedure you can call specific trigger but within a Trigger you cannot call Stored Procedure 
* We can call a stored procedure from front end (.asp files, .aspx files, .ascx files etc.) but we can't call a trigger from these files. 
* **Stored procedure can take the input parameters, but we can't pass the parameters as an input to a trigger**
