# 📌 High-Level Summary

This lab demonstrates how to exploit a **NoSQL Injection** vulnerability in a coupon validation feature by injecting the payload `{"$gt": ""}`. The payload manipulates the NoSQL query to bypass validation logic and return sensitive coupon details, including the discount amount and creation date.

---

# 🎯 What to Look Out For During Testing

1. **Parameters Vulnerable to Injection:**
    
    - Test parameters in JSON structures (e.g., `coupon_code`) with NoSQL-specific payloads like `{"$gt": ""}`, `{"$ne": null}`, or `{"$exists": true}`.
2. **Response Patterns:**
    
    - Look for status codes (e.g., `200`, `422`) or variations in response lengths that indicate a successful injection.
3. **Error Messages:**
    
    - Analyze error responses for clues about the database structure or query behavior (e.g., invalid characters or unexpected tokens).
4. **Payload Encoding:**
    
    - Ensure payloads are not URL-encoded when targeting NoSQL queries, as encoding can break the structure of operators like `$gt`.

---

# ✅ Key Steps for Testing NoSQL Injection

1. **Capture the Request:**
    
    - Use tools like Burp Suite to identify where user input interacts with the database (e.g., `POST /validate-coupon`).
2. **Inject Basic Payloads:**
    
    - Start with NoSQL operators (`$gt`, `$ne`, `$exists`) to test if the application processes them as part of the query.
3. **Automate with Intruder:**
    
    - Use Burp Suite Intruder to send payload lists targeting specific JSON fields, disabling payload encoding for precise injection.
4. **Validate Successful Injection:**
    
    - Check for a `success: true` response or returned data (e.g., coupon details) that confirms the injection worked.

---

# ✅ Key Takeaways

- **NoSQL Injection Relies on Query Manipulation:** Exploits improperly sanitized inputs to insert operators like `$gt` into the database query.
- **Error Analysis is Crucial:** Errors can reveal injection points and query structures.
- **Automation Simplifies Testing:** Tools like Burp Suite Intruder and NoSQL-specific payload lists streamline the discovery of vulnerabilities.
- **Payload Placement Matters:** Ensure payloads fit into JSON structures correctly, especially in key-value pairs.

By following these steps, you can efficiently test for and exploit NoSQL injection vulnerabilities.