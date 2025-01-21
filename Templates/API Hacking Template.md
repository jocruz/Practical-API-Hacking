

---

# 🛠️ API Hacking Template (Exam-Ready)

## 🏁 **Critical First Steps** (5-Minute Quick Start)

1. **Review API Documentation**:
    - [ ]  Identify authentication type (JWT, API Key, OAuth).
    - [ ]  Note endpoints requiring tokens or headers.
2. **Check Public Endpoints**:
    - [ ]  Test accessible endpoints without authentication.
    - [ ]  Look for sensitive parameters like `id`, `user`, `role`.
3. **Set Up Tools**:
    - [ ]  Import endpoints into **Postman** or configure **Burp Suite**.
    - [ ]  Run basic fuzzing with **FFUF**:
        
        ```bash
        ffuf -w /path/to/wordlist.txt -u https://target.com/FUZZ
        ```
        

---

## 🌐 General Info

- **Application Name**:
- **Base URL**:
- **Environment** (Production/Staging):
- **Authentication Type**: (e.g., JWT, API Key, OAuth)
- **Tech Stack**:

---

## 🔍 Reconnaissance

### 1️⃣ API Documentation

- **Endpoints**:
    - `/api/v1/login`
    - `/api/v1/users`
    - `/api/v1/...`
- **Parameters**:
    - Query: `id=123`, `token=xyz`
    - Body: `{ "username": "admin" }`
    - Headers: `Authorization: Bearer ...`
- **Sensitive Fields**:
    - [ ]  Token?
    - [ ]  API Key?

#### 🚩 Quick Checks:

- [ ]  Public endpoints accessible without auth.
- [ ]  Sensitive data in responses (e.g., passwords, tokens).

---

### 2️⃣ Enumeration Tools

- **Postman**:
    - [ ]  Test endpoints manually.
    - [ ]  Automate request sequences.
- **Burp Suite**:
    - [ ]  Intercept API traffic.
    - [ ]  Modify parameters in Repeater.
- **FFUF**:
    
    ```bash
    ffuf -w /path/to/wordlist.txt -u https://example.com/FUZZ
    ```
    
    - Results:

#### 🚩 Potential Issues:

- [ ]  Lack of rate limiting.
- [ ]  Misconfigured CORS policies.
- [ ]  Open or undocumented endpoints.

---

## 🔑 Authentication Testing

### 1️⃣ Login Analysis

- **Mechanism**:
    - [ ]  Username/Password
    - [ ]  API Key
    - [ ]  OAuth
- **Test Cases**:
    - [ ]  Brute force login.
    - [ ]  Default credentials.
    - [ ]  Token replay attacks.

### 2️⃣ Tokens (JWT/API Key)

- **Token Format**:
    - JWT:
        - Header:
        - Payload:
        - Algorithm:
    - API Key:
        - Format:
- **Test Scenarios**:
    - [ ]  Modify payload for privilege escalation (e.g., `"role": "admin"`).
    - [ ]  Test weak signing algorithms (`none` or `HS256`).

---

## 📄 Vulnerability Checklist

### Common API Vulnerabilities

- [ ]  **IDOR**:
    - Test object IDs for unauthorized access (`/api/v1/users/2`).
- [ ]  **Excessive Data Exposure**:
    - Verify responses for unnecessary fields.
- [ ]  **SSRF**:
    - Input malicious URLs (e.g., `http://127.0.0.1/admin`).
- [ ]  **Rate Limiting**:
    - Send repeated requests rapidly and observe server behavior.
- [ ]  **JWT Manipulation**:
    - Test with modified headers/payloads (`alg: none`).

---

## 🚦 Quick Reference Payloads

### SQL Injection

```sql
' OR 1=1--
```

### SSRF

```http
http://127.0.0.1/admin
```

### NoSQL Injection

```json
{ "username": { "$ne": null }, "password": { "$ne": null } }
```

### JWT None Algorithm

```json
{
  "alg": "none",
  "typ": "JWT"
}
```

---

## 🔧 Tool-Specific Notes

### 1️⃣ Postman

- **Endpoints Tested**:
- **Payloads Used**:

### 2️⃣ Burp Suite

- **Intercepted Requests**:
    - Endpoint:
    - Notes:
- **Repeater Findings**:

### 3️⃣ FFUF

- **Command**:
    
    ```bash
    ffuf -w /path/to/wordlist.txt -u https://example.com/FUZZ
    ```
    
- Results:

---

## 📝 Notes & Observations

- **Interesting Responses**:
    - Status `200`:
    - Status `403`:
    - Status `500`:
- ## **Unexpected Behavior**:
    
- ## **Potential Exploits**:
    

---
