Username = wiener
password = bluecheese

## Query will be
SELECT * FROM users WHERE username='wiener' AND password 'bluecheese'

if you logedin queru sucessful if not login failed



## SQL Common sequence => an attacker can login by using this technique

Add query and boom
Here is the query for that 
## SELECT * FROM users WHERE username = 'administrator'
-- AND password = ''


## Retrieving data from other database tabels

This is done by using the union Keywords which lets you use additional SELECT query and appends the results to original query

If the application executes this query 

SELECT name, description FROM products WHERE
category = 'Gifts' then the attacker can submit this input

SELECT name, description FROM products WHERE category = 'Gifts' UNION SELECT username, password from users--'

This will cause the application to return all the usernames and passwords along with the Gifts Data


## Examining the Database

When you are dicovering the SQL Injection vulnerability its generally usefull to obtain information
about the database as well This can help us to move
to next processes
You can query for the version of database

The way this is done depends on the database type
we can inferer database type which ever techniques works
## For Example in ORACLE
SELECT * FROM v$version
We can find the version of oracle database version by the help of this

And we can also determine which database tables are exists and what columns they contain

On most of the databases we can execute this query 

SELECT * FROM information_schema.tables
to get the information about the tables in the database

## Blind SQL injection vulnerabilities
Many instances of SQL Injection are blind vulnerabilities

This means the application does not return the results of the queries and details of the database in return 
of the SQL commands
Blind vulnerabilities can still be exploite to access unauthorised data but the techniques involved are generaly more complicated to perform 
Depending the nature of the vulnerability and database involved a variety of techniques can be used to
exploite the vulnerability in this case

## Sol:1 We can change the logic of the query 
to trigger the detectible different in the application response depending on the truth of the this might involve injecting a new condition into a boolean
logic. Or Condionally /0

## SOL:2 You can conditionally trigger a time delay in the processing of the query
Allowing you to infer the the truth of the condition based on the time that the application takes to respond you

## You can trigger an out-of-band network interaction 
This technique is extremely powerful and works in the situation where the other techniques do not 
often you can directly exfiltrate data via the out of band channel for example by placing the data
into a DNS lookup for a domain you control 

## Detecting SQL injection vulnerabilities
The majority of Sql injection vulnerabilities can be found quickly and reliable using birp suites web vulnerability scanner you can find SQL injection
vulnerabilities manually by using a systematic set of tests against every entry point in the application you 
can submit the single quote character and look for the errors or other anomalies. You can submit some SQL syntax and look for systematic differences and the resulting application responses you can submit boolean conditions and look for the differences
in the applications Responses
. '
. ASCII (97)
. ' OR 1=1--

You can submit payloads designed to trigger time delays and look for differences in the time taken 
to respond and you can submit designed to trigger and out-of-band network interaction and monitor fro any resulting interactions


## SQL injection in different parts of the query
Most SQL injection vulnerabilities arise within the where clause of a SELECT query.
This type of SQL injection well understood by experienced testers but SQL injection vulnerability can in principle occure at any location in 
query and within different query types.
The most common other location where SQL injection arises are in the update statements within the updated values where clauses in  insert Statements than the inserted values in select statement within the table 
or column name and in select statements within the order by clause 

## Second-Order SQL injection

First order injection arises where the application where the application takes the user input from
HTTP request and in the course of processing that request incoperates the input an SQL Query in
and unsafe way 

In Second Order SQL injection is known as Stored SQL injection the application takes user input from 
an HTTP request and stores it for the future use this is unusally done by placing the input into database 
but no vulnerability arises at the point where the data is stored. Later when handling a different
HTTP request the application retrives the application retrieves the stored data and incoperates it into SQL query in an unsafe way 

Second Order SQL injection rises in situations where developers are aware of SQL injection vulnerabilities 
and safely handle the intial placement of the imput into the database. When data is later processed
it is deemed to be safe since it was previously placed into the database safely at this point data is haneled in an unsafe way because the developer wrongly deems it to be trusted


## Database-Sepcific factors
Some core features of the SQL are implemented in the same way across popular database platforms and so many ways of detecting and exploting SQL injection vulnerabilities work identically on different types of 
databases however there are also many differences between common databases these mean that some
techniques for detecting and exploting SQL injection
work differently on different platforms
Differences between databses arise in areas like 
## Sytax for String concatination
## Comments
## Batched (or Stacked) queries
## Platforms specific APIs
## Error messages



## Preventing SQL injection
1. Using parameterised queries instead of string
  String query = "SELECT * FROM products WHERE category = '"+ input + "'":
  Statement statement = connection.createStatement();
  ResultSet resultSet = statement.executeQuery(query);

