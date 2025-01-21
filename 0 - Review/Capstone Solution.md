# 📌 High-Level Summary

This lab demonstrates how **JWT Manipulation**, **Broken Object Level Authorization (BOLA)**, and **weak endpoints** discovered via **ffuf** can be exploited to impersonate users, access sensitive data, and track another user's orders. The vulnerabilities arise from improper token validation, missing authorization checks, and exposed endpoints.

---

# 🎯 What to Look Out For During Testing

1. **JWT Vulnerabilities:**
    
    - Check if the `alg` field can be set to `none` to bypass cryptographic validation.
    - Test for modifications to token payloads, like changing the `username` field, to impersonate other users.
2. **BOLA (Broken Object Level Authorization):**
    
    - Verify if endpoints properly enforce access control by testing with other users' identifiers (e.g., `order_id`).
3. **Endpoint Security:**
    
    - Look for exposed endpoints (e.g., `orders.php`) that reveal sensitive data without authentication or authorization checks.
    - Test if endpoints cross-reference the logged-in user with requested resources.
4. **Directory Fuzzing:**
    
    - Use tools like **ffuf** to discover hidden files and directories.
    - Focus on admin directories, endpoints with sensitive actions, and file extensions like `.php`.

---

# ✅ Key Steps for Exploitation

1. **JWT Testing:**
    
    - Modify the JWT `alg` field to `none` and test if the server accepts the token.
    - Change payload values like `username` to access another user's data.
2. **Directory and Endpoint Fuzzing:**
    
    - Run **ffuf** with wordlists and extensions to uncover hidden endpoints.
    - Focus on admin or sensitive directories (e.g., `/admin/orders.php`).
3. **Test for Authorization Flaws:**
    
    - Inject identifiers for other users (e.g., `order_id`) into endpoints and observe responses.
    - Verify if access controls properly restrict data based on the logged-in user.

---

# ✅ Key Takeaways

- **JWT Security:** Always validate tokens cryptographically, disallowing `alg: none`.
- **Access Controls:** Implement strict authorization checks to prevent BOLA vulnerabilities.
- **Endpoint Discovery:** Directory fuzzing with tools like **ffuf** is critical for uncovering weak points in web applications.
- **Monitor Sensitive Endpoints:** Ensure admin pages and data-revealing endpoints are secured with authentication and authorization checks.

By systematically testing for these vulnerabilities, you can identify and mitigate significant security flaws in web applications.