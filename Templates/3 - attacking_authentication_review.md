
# 🔑 Attacking Authentication - JWT



## 🧭 High-Level Summary

JSON Web Tokens (JWTs) are widely used for authentication and session management in web applications. They consist of three parts: header, payload, and signature. Weak implementations of JWTs can lead to severe vulnerabilities such as **token forgery**, **replay attacks**, or **privilege escalation**.

---

## 📚 Definitions and Key Terms

|**Term**|**Definition**|
|---|---|
|**JWT (JSON Web Token)**|A compact, self-contained token used for securely transmitting information.|
|**Replay Attack**|Reusing valid tokens or credentials to gain unauthorized access.|
|**None Algorithm**|A vulnerable algorithm allowing unsigned JWTs.|
|**JWK Injection**|Manipulating the JWK header to accept malicious keys.|
|**Base64 Encoding**|Encoding format for JWT parts; it does not provide security.|


---

  

## 🛠️ Detailed Methodology

  

### 1️⃣ Analyzing JWT Structure

**Steps**:

1. **Decode JWT**:

- Use tools like **jwt.io** or `base64` to decode the token.

```bash

echo '<jwt>' | base64 -d

```

2. **Inspect Header**:

- Check the algorithm (e.g., `HS256`, `RS256`, `none`).

3. **Analyze Payload**:

- Identify claims like `sub`, `iat`, `exp`, or `role`.

  

**Common Issues**:

- Tokens without expiration (`exp` claim).

- Weak or absent signature verification.

  

---

  

### 2️⃣ Exploiting JWT Vulnerabilities

#### None Algorithm

**Steps**:

1. Modify the JWT header to `"alg": "none"`.

2. Remove the signature and resend the token.

  

#### Weak Secret Cracking

**Steps**:

1. Extract the JWT secret using tools like `jwt_tool` or `hashcat`.

```bash

jwt_tool <jwt> --crack -d /usr/share/wordlists/rockyou.txt

```

2. Resign the token with the cracked secret.

  

#### JWK Injection

**Steps**:

1. Replace the `jwk` header with a malicious public key.

```json

{

"alg": "RS256",

"jwk": {

"kty": "RSA",

"n": "malicious-key",

"e": "AQAB"

}

}

```

2. Sign the token with the private counterpart.

  

---

  

### 3️⃣ Advanced Testing Techniques

#### Replay Attacks

1. Capture a valid token using Burp Suite.

2. Resend the token after logout to check for reuse vulnerabilities.

  

#### Role Escalation

1. Modify the `role` claim in the payload (e.g., `user` to `admin`).

2. Resign the token and verify if privileges escalate.

  

---

  

## 📌 Key Takeaways and Tools

  

### Tools

- **Burp Suite**: For intercepting and modifying JWTs.

- **jwt_tool**: For cracking and testing JWTs.

- **Postman**: For testing API endpoints with modified tokens.

- **jwt.io**: For decoding and analyzing JWTs.

  

### Key Takeaways

- Always test for weak or missing token validation.

- Check if JWTs lack proper expiration or audience (`aud`) claims.

- Exploit vulnerable algorithms like `none` or weak `RS256` implementations.
