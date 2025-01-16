
---

# 📖 High-Level Summary:

This note explains **BOLA**, **BLFA**, and **IDOR**, three critical web vulnerabilities often encountered in penetration testing. Each involves **improper authorization** checks that can lead to unauthorized access or actions within a web application.

---

# 📚 Definition Bank:

## 1. BOLA (Broken Object Level Authorization)

- **What:** Users can access data they shouldn't be able to due to improper authorization checks at the object level.
- **Example:** If user `123` can access their profile with:
    
    ```http
    GET /user/123
    ```
    
    But changing the ID to:
    
    ```http
    GET /user/124
    ```
    
    allows them to view another user's profile without authorization, that's a **BOLA vulnerability**.

---

## 2. BLFA (Broken Function Level Authorization)

- **What:** Users can access **restricted functions or actions** due to missing authorization checks at the **function level**.
- **Example:** A regular user should **not** be able to delete accounts, but calling:
    
    ```http
    POST /admin/delete-user/123
    ```
    
    allows them to delete another user's account without admin privileges.

---

## 3. IDOR (Insecure Direct Object Reference)

- **What:** **Direct access** to data by manipulating identifiers, typically a subset of **BOLA** vulnerabilities.
- **Example:** If a user can access their invoice at:
    
    ```http
    GET /invoice/123.pdf
    ```
    
    and changing the ID to:
    
    ```http
    GET /invoice/124.pdf
    ```
    
    lets them view **another user's invoice**, that's an **IDOR vulnerability**.

---

# 🎯 Key Differences:

- **BOLA:** Focuses on **object-level access control**, ensuring users can only access objects they're authorized for.
- **BLFA:** Focuses on **function-level access control**, preventing unauthorized actions.
- **IDOR:** A **specific example** of BOLA focusing on **direct object references** (like numerical IDs).

---

# ✅ When to Test for These Vulnerabilities:

- **BOLA:** Test for unauthorized access to **user data**.
- **BLFA:** Test for restricted **admin actions**.
- **IDOR:** Test for **direct file or data access** through predictable identifiers.

---