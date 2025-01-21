# 📌 High-Level Summary: SSRF, Mass Assignment, and Privilege Escalation

This capstone demonstrates the exploitation of **Server-Side Request Forgery (SSRF)**, **Mass Assignment**, and **Privilege Escalation** vulnerabilities to compromise a web application. Using tools like `ffuf` for endpoint discovery and manual API manipulation, the attacker escalates privileges, retrieves sensitive data, and gains admin access to restricted resources.

---

# 🎯 What to Look Out For During Testing

1. **SSRF Vulnerabilities:**
    
    - Look for parameters that accept URLs (e.g., `original`) and test if the server fetches external/internal resources.
    - Test internal endpoints to access restricted data (e.g., `/v1/data/internal/users`).
2. **Mass Assignment Vulnerabilities:**
    
    - Test if you can add unexpected fields (e.g., `userlevel: admin`) in POST or PUT requests to manipulate privileges.
3. **Privilege Escalation:**
    
    - After exploiting vulnerabilities, test for elevated access to admin-only endpoints or data.
4. **Endpoint Discovery:**
    
    - Use tools like `ffuf` with wordlists (`dirb/big.txt`) to identify hidden endpoints for further exploitation.

---

# ✅ Key Steps for Exploitation

1. **Endpoint Enumeration:**
    
    - Use `ffuf` to discover sensitive endpoints:
        
        ```bash
        ffuf -u http://localhost:3000/v1/data/internal/FUZZ -w /usr/share/seclists/dirb/big.txt
        ```
        
    - Example findings: `/v1/data/internal/uptime`, `/v1/data/internal/users`.
2. **Test SSRF:**
    
    - Modify parameters like `original` to point to external or internal URLs:
        
        ```json
        {
          "original": "http://localhost:3000/v1/data/internal/users"
        }
        ```
        
    - Confirm SSRF by retrieving internal server data.
3. **Exploit Mass Assignment:**
    
    - Register a user with elevated privileges by adding unauthorized fields:
        
        ```json
        {
          "username": "newuser",
          "password": "newuser",
          "userlevel": "admin"
        }
        ```
        
4. **Privilege Escalation:**
    
    - Use admin session tokens to access restricted endpoints, such as:
        
        ```http
        GET /v1/data/admin/recipes
        ```
        

---

# ✅ Key Takeaways

- **SSRF**: Leverage server-side trust to fetch internal or external data using modified parameters.
- **Mass Assignment**: Prevent unauthorized data injection by validating and restricting allowable fields.
- **Privilege Escalation**: Harden admin-only endpoints to prevent access by unauthorized users.

---

By combining these vulnerabilities, attackers can compromise application security, access sensitive data, and escalate privileges to perform restricted actions. Robust validation, endpoint restrictions, and regular audits are critical to mitigating these risks.