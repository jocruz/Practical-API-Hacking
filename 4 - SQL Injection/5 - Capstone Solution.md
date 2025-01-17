
---

# 📖 High-Level Summary:

This capstone lab demonstrates multiple security vulnerabilities, including **Broken Level Authorization (BOLA)**, **JWT Manipulation**, and weak endpoints discovered using **ffuf**. By exploiting these issues, we gain unauthorized access to another user's session, track their orders, and expose sensitive order details.

---

# 📚 Definition Bank:

|**Term**|**Definition**|**Key Notes**|
|---|---|---|
|**JWT (JSON Web Token)**|A token format used for securely transmitting information as a JSON object.|Manipulating the payload or algorithm (`alg: none`) can bypass security checks.|
|**Broken Level Authorization (BOLA)**|A vulnerability where users can access resources they are not authorized for.|Exploited here to perform actions on behalf of other users.|
|**ffuf**|A web fuzzer used to discover hidden endpoints.|Supports directory and file enumeration with extensions like `.php`.|
|**`-e` flag**|Specifies file extensions to append during fuzzing.|Example: `.php`, `.html`.|
|**Weak Endpoint**|An endpoint that lacks proper security controls, exposing sensitive data.|Here, `orders.php` exposes all user orders.|

---

# 🎯 Steps and Commands:

### Step 1: Manipulate JWT to Exploit BOLA

1. **Register Two Users**:
    
    - `test:test`
    - `test2:test2`
2. **Login as `test:test`**:
    
    - Use the **Cookie Editor** browser extension or developer tools to extract the session cookie.
    - Copy the cookie and go to [jwt.io](https://jwt.io/).
3. **Modify the JWT**:
    
    - Change the `username` from `test` to `test2` in the payload:
        
        ```json
        {
          "username": "test2"
        }
        ```
        
    - Set the `alg` (algorithm) to `none` for testing.
4. **Place an Order on Behalf of `test2`**:
    
    - In Burp Suite, intercept the **Place Order** request.
    - Replace the cookie with the modified JWT.
    - Modify the request body to change `username` from `test` to `test2`:
        
        ```json
        {
          "username": "test2"
        }
        ```
        
    - Forward the request to complete the order for `test2`.

---

### Step 2: Discover Hidden Endpoints Using ffuf

1. **Fuzz the Base URL**:
    
    ```bash
    ffuf -u http://localhost/v1/FUZZ -w /usr/share/wordlists/dirb/big.txt -e .php
    ```
    
    - **Flags Used**:
        - `-u`: Target URL with `FUZZ` as the placeholder.
        - `-w`: Specifies the wordlist for fuzzing.
        - `-e`: Appends file extensions (`.php`).
2. **Results**:
    
    - Discovered Endpoints:
        
        ```plaintext
        admin
        coffee.php
        track.php
        ```
        
3. **Fuzz the Admin Directory**:
    
    ```bash
    ffuf -u http://localhost/v1/admin/FUZZ -w /usr/share/wordlists/dirb/big.txt -e .php
    ```
    
    - **Result**:
        
        ```plaintext
        orders.php
        ```
        

---

### Step 3: Exploit the Weak `orders.php` Endpoint

1. **Access `orders.php`**:
    
    - Visit:
        
        ```plaintext
        http://localhost/v1/admin/orders.php
        ```
        
    - **Response**:
        
        ```json
        {
          "id": "4",
          "order_id": "6421736cc7070",
          "email": "test2",
          "coffee_id": "0",
          "quantity": "2",
          "order_status": "pending"
        }
        ```
        
2. **Track Another User’s Order**:
    
    - Take `order_id` from another user's order (`test2`).
    - As the `test` user, use the `order_id` in the tracking endpoint to confirm another user’s order.

---

# 🔍 Why the Exploits Work:

1. **JWT Manipulation**:
    
    - JWTs with the algorithm set to `none` bypass cryptographic verification.
    - Modifying the `username` field allows impersonation of another user.
2. **Weak Endpoint Security**:
    
    - `orders.php` lacks proper authorization checks, exposing all order data.
    - Tracking functionality does not verify the logged-in user against the `order_id`, enabling unauthorized access.
3. **Directory Fuzzing**:
    
    - ffuf efficiently discovers hidden directories and files, revealing weak endpoints like `admin/orders.php`.

---

# ✅ Key Takeaways:

1. **BOLA and JWT Vulnerabilities**:
    - Exploiting JWTs and broken authorization can lead to serious security breaches.
2. **Endpoint Discovery with ffuf**:
    - Directory and file enumeration is crucial for uncovering hidden weaknesses in web applications.
3. **Improper Authorization Checks**:
    - Weak endpoints like `orders.php` highlight the importance of robust access control mechanisms.

---