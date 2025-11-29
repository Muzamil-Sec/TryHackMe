TryHackMe Room: Database - Comprehensive Learning Summary
The "Database" room on TryHackMe serves as a practical introduction to database security fundamentals, with a specific focus on MySQL/MariaDB environments. It teaches essential skills for interacting with databases, understanding their structure, and identifying critical security vulnerabilities like SQL injection.

Room Overview & Learning Objectives
This room is designed to teach you how to:

Connect to and interact with MySQL/MariaDB databases using command-line tools

Navigate database structures and extract schema information

Understand database user privileges and their security implications

Identify and exploit SQL injection vulnerabilities

Recognize the dangers of insecure database configurations

Key Concepts & Topics Covered
The room progresses through fundamental database operations to security exploitation techniques.

1. Database Fundamentals & Connection
Database Types: Introduction to Relational Database Management Systems (RDBMS) like MySQL/MariaDB

Connection Methods: Using the mysql command-line client to connect to remote databases

Basic Syntax: mysql -h <host> -u <username> -p<password>

Practical Application: Connecting to the target database instance using provided credentials to begin exploration

2. Database Navigation & Exploration
Information Gathering Commands:

SHOW DATABASES; - List all available databases

USE <database>; - Select a specific database to work with

SHOW TABLES; - Display all tables within the current database

DESCRIBE <table>; - Examine the structure and columns of a specific table

Practical Application: Systematically exploring the database structure to understand what data is stored and where

3. Data Querying & Manipulation
SQL Query Fundamentals:

SELECT * FROM <table>; - Retrieve all data from a table

SELECT <column1>, <column2> FROM <table>; - Retrieve specific columns

WHERE clauses for filtering results

Data Extraction Techniques: Learning to extract specific information like credentials, personal data, or other sensitive information from database tables

Practical Application: Querying user tables to find administrator credentials and other sensitive data

4. Database User Privileges & Security
Privilege Investigation:

SELECT * FROM mysql.user; - View all database users and their privileges

SELECT user(); - Check current database user

SELECT grantee, privilege_type FROM information_schema.user_privileges; - Detailed privilege analysis

Security Implications: Understanding how excessive privileges (like FILE_priv, SUPER_priv) can lead to serious security breaches

Privilege Escalation: How database privileges can sometimes be leveraged for system access

5. SQL Injection Fundamentals
Core Vulnerability: Understanding how unsanitized user input can be interpreted as SQL code

Injection Detection:

Using single quotes (') to test for error-based SQL injection

Boolean-based detection (' OR 1=1 --)

Union-Based Exploitation:

Determining the number of columns using ORDER BY

UNION SELECT statements to extract data from other tables

UNION SELECT 1,2,3... for column enumeration

Practical Application: Exploiting vulnerable web parameters to extract database information through UNION attacks

6. Advanced Database Exploitation
Database-to-OS Escalation:

Using SELECT ... INTO OUTFILE for file write operations

Understanding the requirements for successful file writing (FILE_priv, secure_file_priv)

Web Shell Deployment: Writing PHP web shells to gain remote code execution

Privilege Abuse: Examples of how database privileges can be misused for broader system compromise

7. Security Best Practices & Defense
Input Validation: The critical importance of parameterized queries and prepared statements

Principle of Least Privilege: Database users should only have minimum necessary permissions

Secure Configurations: Proper database hardening (disabling unnecessary features, restricting file privileges)

Error Handling: Preventing information leakage through database errors

Practical Tools and Skills Gained
MySQL/MariaDB Proficiency: Comfort with command-line database interaction and SQL queries

Database Enumeration: Methodical approach to discovering database structure and contents

SQL Injection Exploitation: Hands-on experience with detecting and exploiting SQL injection vulnerabilities

Privilege Analysis: Ability to assess database user privileges and understand their security impact

Web Application Testing: Understanding the relationship between web apps and their backend databases