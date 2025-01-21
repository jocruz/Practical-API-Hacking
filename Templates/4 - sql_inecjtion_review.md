  

# 🛠️ SQL Injection

  

## 🧭 High-Level Summary

SQL injection (SQLi) occurs when attackers manipulate SQL queries by injecting malicious inputs. This can result in unauthorized access, data exfiltration, or even full system compromise.

---

## 📚 Definitions and Key Terms

|**Term**|**Definition**|
|---|---|
|**SQL Injection (SQLi)**|Exploiting SQL queries by injecting malicious inputs.|
|**Union-Based SQLi**|Uses the `UNION` operator to combine malicious queries with legitimate ones.|
|**Error-Based SQLi**|Leverages database error messages to gain insights into the database structure.|
|**Boolean-Based SQLi**|Determines query behavior through true/false responses.|
|**NoSQL Injection**|Exploits vulnerabilities in NoSQL databases by injecting malicious code.|

---


## 🛠️ Detailed Methodology

  

### 1️⃣ Identifying SQL Injection Vulnerabilities

**Steps**:

1. **Test Common Inputs**:

- `' OR 1=1--`

- `" OR 1=1--`

2. **Inspect Responses**:

- Look for changes in behavior, errors, or unauthorized data access.

3. **Test Parameters**:

- Inputs in URLs, forms, and headers.

  

### 2️⃣ Union-Based SQL Injection

**Steps**:

1. **Identify Number of Columns**:

```sql

' ORDER BY 1--

' ORDER BY 2--

```

2. **Perform Union Injection**:

```sql

' UNION SELECT NULL, NULL, username, password --

```

  

### 3️⃣ Error-Based SQL Injection

**Steps**:

1. **Trigger Errors**:

```sql

' AND 1=CAST('a' AS INT)--

```

2. **Extract Data from Errors**.

  

### 4️⃣ NoSQL Injection

**Steps**:

1. **Bypass Authentication**:

- Input:

```json

{

"username": { "$ne": null },

"password": { "$ne": null }

}

```

2. **Leverage Query Operators**:

- `$regex`, `$gt`, `$ne`.

  

---

  

## 📌 Key Takeaways and Tools

  

### Tools

- **Burp Suite**: For intercepting and modifying requests.

- **SQLMap**: For automated SQLi detection and exploitation.

- **NoSQLMap**: For testing NoSQL databases.

- **Postman**: For manual API testing.

  

### Key Takeaways

- Focus on dynamic queries with user inputs.

- Leverage error messages for insights into the database.

- Always test NoSQL databases for injection vulnerabilities.

  

---
