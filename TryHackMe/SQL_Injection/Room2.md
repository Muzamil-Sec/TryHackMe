Room 2 focuses on a more dangerous form of SQL Injection where the attack happens on an UPDATE statement instead of a SELECT statement. The key idea introduced here is that UPDATE queries modify data, so exploiting them allows an attacker not just to read information but to change it. This makes UPDATE-based SQL injection more destructive because it can alter user records, escalate privileges, or reset passwords.

The scenario uses an employee profile edit page where users are allowed to update only limited fields such as nickname or email. The application enforces this restriction at the application logic level, not at the database level. If the SQL query behind this form is vulnerable, an attacker can bypass the intended restrictions and update fields they should never have access to, including sensitive fields or even records belonging to other users.

The core vulnerability comes from how the UPDATE query is constructed. A typical vulnerable query looks like UPDATE table SET nickName='value', email='value' WHERE some_condition. If user input is directly placed inside this query without sanitization, the attacker can break out of one field and inject additional column updates. This works because SQL does not know which parts were intended as data and which parts are injected logic.

The first step shown in the room is confirming that the UPDATE form is vulnerable. This is done by injecting a payload that attempts to update multiple fields at once. By entering asd',nickName='test',email='hacked into one of the input fields, the attacker tests whether the database accepts injected column assignments. If more than one field changes, it confirms both the vulnerability and the correctness of the guessed column names.

Column name guessing is an important concept here. Even without database access, attackers can often guess common column names such as email, password, nickName, or name. The form’s HTML source code helps further because input name attributes often closely match database column names. If the guessed column names were wrong, the query would fail and no fields would update.

Once the vulnerability and column names are confirmed, the next goal is to identify the database type. This matters because each database has slightly different syntax and functions. The room shows that the database can identify itself by returning its version. Different payloads are used for MySQL, MSSQL, Oracle, and SQLite, each using a database-specific function or variable to return version information.

In this room, injecting sqlite_version() reveals that the backend database is SQLite. Knowing this allows the attacker to use SQLite-specific system tables and functions for enumeration. This step highlights an important principle that database fingerprinting is often the first step before deeper exploitation.

After identifying SQLite, the room demonstrates how to enumerate database tables. SQLite stores metadata about tables in a system table called sqlite_master. By injecting a subquery into the UPDATE statement, the attacker forces the database to return table names into a visible field like nickName. The group_concat function is used to merge multiple rows into a single string so all table names can be viewed at once.

Once the table name is discovered, in this case usertable, the next step is enumerating column names. This is done by querying the SQL definition of the table from sqlite_master. The returned CREATE TABLE statement reveals all column names and their order, giving the attacker a complete map of the table structure.

With column names known, data extraction becomes possible. The attacker uses another injected subquery to dump values such as profileID, usernames, and passwords. The group_concat function is again used, along with the SQLite string concatenation operator ||, to format multiple columns and rows into a readable output.

At this stage, the room introduces password hashing. The extracted passwords are not stored in plaintext but as hashes. This is an important real-world defense, but it does not stop SQL injection. The attacker simply needs to identify the hash type. By using a hash identification tool, the hash is recognized as SHA256.

Instead of cracking the hash, the attacker chooses a simpler and more powerful approach: overwriting the password. By generating a new SHA256 hash for a known password and injecting it into the UPDATE query, the attacker directly replaces the admin’s password. This demonstrates how UPDATE-based SQL injection can fully compromise accounts without needing to break cryptography.

The final injected payload updates the password field for the Admin user and comments out the rest of the query to avoid syntax errors. After this, the attacker can log in as Admin using the new password and access restricted content, including the flag.

The task reinforces that the same enumeration process must be repeated to find the flag because it is stored in a different table. This highlights that SQL injection is a systematic process: confirm vulnerability, identify database type, enumerate tables, enumerate columns, extract or modify data.

The main takeaway from Room 2 is that SQL injection is not limited to reading data. When UPDATE statements are vulnerable, attackers gain write access to the database, allowing them to modify records, reset passwords, and fully compromise the application. This makes UPDATE-based SQL injection one of the most dangerous variants and underscores why parameterized queries and strict server-side validation are essential defenses.
