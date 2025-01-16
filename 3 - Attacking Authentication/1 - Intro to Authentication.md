Here’s the updated **Definition Bank** with a table format for better readability in Obsidian:

---

# 📖 High-Level Summary:

### Steps to Identify and Exploit BOLA:

**BOLA (Broken Object Level Authorization)** occurs when an application fails to enforce proper access control on individual objects. In this lab, BOLA is confirmed when the user (John) is able to access another user's mechanic report and vehicle details by modifying the `report_id` in the API endpoint. This demonstrates a lack of authorization checks at the object level, exposing sensitive data like vehicle owner information, emails, and phone numbers.

---

# 📚 Definition Bank:

|**Term**|**Definition**|**Weakness**|
|---|---|---|
|**Authentication**|The process of verifying the identity of a user or system to ensure they are who they claim to be.|N/A|
|**HTTP Basic Authentication**|Credentials (username and password) are Base64-encoded and sent with every request.|Credentials are exposed without encryption (e.g., over HTTP), making it insecure.|
|**Bearer Tokens**|Tokens passed in the **Authorization** header to prove identity.|If stolen, tokens can be used without further verification.|
|**JSON Web Tokens (JWTs)**|Self-contained tokens containing user identity and claims, digitally signed for integrity.|Vulnerable to exploitation if poorly configured or if signing keys are exposed.|
|**OAuth 2.0**|A framework for delegating access, often used for third-party app integrations.|Complex to implement correctly.|
|**API Keys**|Unique keys assigned to users or applications to authenticate API requests.|Easy to share, copy, or leak. Typically do not have expiration or revocation by default.|

---

# 🎯 Key Points on Authentication Methods:

### HTTP Basic Authentication:

- **How it works:**
    - The client sends an **Authorization** header with Base64-encoded credentials (`username:password`).
    - Example header:
        
        ```plaintext
        Authorization: Basic dXNlcm5hbWU6cGFzc3dvcmQ=
        ```
        
- **Why it’s insecure:**
    - Credentials are sent with every request.
    - Without HTTPS, they can be intercepted.
- **When to use:** Rarely; only in secure, internal systems or for basic testing.

---

### Bearer Tokens:

- **How they work:**
    - The client includes a token in the **Authorization** header:
        
        ```plaintext
        Authorization: Bearer <token>
        ```
        
    - The token contains the user's identity and permissions.
- **Weakness:** The server trusts the token implicitly, so if stolen, an attacker can use it without the server knowing.
- **Examples:**
    - **JWTs:** Tokens with encoded user information and claims.
    - **OAuth 2.0 tokens:** Issued as part of the OAuth flow.
    - **API keys:** Simple tokens assigned to users or apps.

---

### JSON Web Tokens (JWTs):

- **Structure:**
    - Three parts: Header, Payload, and Signature.
    - Example:
        
        ```plaintext
        eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIn0.SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c
        ```
        
- **Why they’re popular:**
    - Self-contained: Can be validated without a database lookup.
    - Signed: Tamper-evident via cryptographic signatures.
- **Weakness:** If poorly configured (e.g., weak signature keys), they can be exploited.

---

### OAuth 2.0:

- **How it works:**
    - Allows users to authorize third-party apps to access their data without sharing credentials.
    - Example: “Sign in with Google.”
- **Why it’s secure:**
    - Tokens have scopes limiting what they can do.
    - Includes mechanisms for token expiration and refresh.

---

### API Keys:

- **How they work:**
    - Sent as part of the request (e.g., in headers or query parameters).
    - Example:
        
        ```plaintext
        GET /data?api_key=abcdef12345
        ```
        
- **Weakness:**
    - Easy to share, copy, or leak.
    - Typically do not expire.

---

# ✅ Summary of Weaknesses:

1. **HTTP Basic Authentication:** Insecure without HTTPS; credentials sent with every request.
2. **Bearer Tokens:** If stolen, tokens can be misused without further validation.
3. **JWTs:** Vulnerable if poorly configured or if signing keys are exposed.
4. **OAuth 2.0:** Complex to implement correctly.
5. **API Keys:** Lack built-in expiration or revocation, making them easy to misuse.

---

# 🔍 What’s Next:

- Explore attacks on authentication mechanisms (e.g., stealing tokens, brute-forcing API keys).
- Learn how to secure these methods using encryption, scopes, expiration policies, and validation techniques.

---