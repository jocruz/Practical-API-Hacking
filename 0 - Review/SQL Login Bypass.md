
---

# 📌 High-Level Summary

This lab demonstrates a **SQL Injection login bypass** by exploiting logical flaws in the application's SQL query. By injecting the payload `a' OR 1=1'--`, the attacker manipulates the query to always evaluate to `true`, allowing unauthorized login without knowing the password.

---

# 🔍 Why Does This Work?

In this scenario:

1. **How the Back End is Handling the Query:**
    
    - The web application constructs a SQL query like this:
        
        ```sql
        SELECT * FROM users WHERE username='admin' AND password='a'
        ```
        
    - The back end does not hash the password or validate it against stored values but relies solely on this query to check credentials.
2. **What the Payload Does:**
    
    - When the payload `a' OR 1=1'--` is injected, the query becomes:
        
        ```sql
        SELECT * FROM users WHERE username='admin' AND (password='a' OR 1=1)--
        ```
        
    - **Key Points:**
        - `1=1` is always true.
        - The `--` comment tells the SQL interpreter to ignore everything after it, preventing syntax errors.
        - This causes the query to effectively ignore the password check, returning a result as long as the username exists.
3. **Why the Back End Doesn’t Verify Passwords:**
    
    - The back end is flawed because it:
        - Does not hash or securely compare passwords.
        - Relies on the SQL query’s logic to determine authentication.
        - Fails to sanitize user input, making it vulnerable to injection.

---

# 🛠️ What to Look Out For in Modern Apps

Modern applications typically implement proper authentication workflows, but older or poorly designed systems may still have vulnerabilities. Here's what to test for:

1. **Password Validation:**
    
    - Ensure passwords are hashed (e.g., using bcrypt) and compared securely after fetching from the database.
2. **Input Sanitization:**
    
    - Test for unescaped input in username and password fields by injecting simple payloads like `'` or `OR 1=1`.
3. **Query Logic:**
    
    - Check if the back end evaluates passwords as part of a true/false condition instead of secure validation.
4. **Error Messages:**
    
    - Look for database errors that leak information about the query structure or vulnerabilities.

---

# ✅ Key Takeaways

- SQL injection works in this lab because the application relies on a vulnerable query structure for authentication.
- Properly designed systems hash passwords and compare them securely, mitigating this attack.
- Testing for SQLi involves crafting payloads that manipulate query logic, exposing flaws in input handling and query validation.