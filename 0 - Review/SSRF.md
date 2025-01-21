# 📌 High-Level Summary

This lab demonstrates **Server-Side Request Forgery (SSRF)**, where the `stockAPI` parameter in the stock check feature is exploited to make unauthorized internal requests. By modifying the `stockAPI` parameter, the attacker accesses the admin interface and issues a command to delete the user "carlos," bypassing authentication mechanisms.

---

# 🎯 What to Look Out For During Testing

1. **User-Controlled URL Parameters:**
    
    - Look for parameters like `stockAPI` or `url` that send user-supplied URLs to the server.
    - Test if the server follows these URLs to internal or external locations.
2. **Restricted Resources:**
    
    - Identify internal endpoints (e.g., `/admin`) that might process server-side requests.
    - Test for admin-only actions accessible via SSRF (e.g., delete, update, or modify operations).
3. **Response Behavior:**
    
    - Observe HTTP responses (e.g., `302 Found` or partial HTML admin interfaces) for signs of successful SSRF exploitation.

---

# ✅ Key Steps for Exploitation

1. **Intercept Vulnerable Requests:**
    
    - Use tools like Burp Suite to capture the request that includes the parameter being exploited (e.g., `stockAPI`).
2. **Modify the Parameter:**
    
    - Replace the `stockAPI` value with internal URLs, such as:
        
        ```plaintext
        http://localhost/admin
        ```
        
        or
        
        ```plaintext
        http://localhost/admin/delete?username=carlos
        ```
        
3. **Issue Admin Commands:**
    
    - Identify admin functionality from the responses and use the SSRF to execute unauthorized commands (e.g., deleting a user).
4. **Validate Exploitation:**
    
    - Observe response status codes (`200 OK` or `302 Found`) and verify if the action (e.g., user deletion) was performed.

---

# ✅ Key Takeaways

- **Core Cause of SSRF:**
    
    - The application blindly trusts user-provided URLs in the `stockAPI` parameter, enabling unauthorized internal requests.
- **Effective Mitigations:**
    
    1. Validate and sanitize user input to ensure URLs are restricted to approved domains.
    2. Implement strict allowlists for permissible URLs or IP ranges.
    3. Disable unnecessary server-side URL fetch functionality when not required.
- **Testing Tools:**
    
    - Use **Burp Suite** or similar tools to intercept and modify requests to explore SSRF vulnerabilities.

By identifying and exploiting SSRF vulnerabilities, you can uncover significant risks that allow attackers to access sensitive internal systems and perform unauthorized actions.