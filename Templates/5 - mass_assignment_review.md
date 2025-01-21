
# 🔧 Mass Assignment Review

## 🧭 High-Level Summary
Mass assignment vulnerabilities occur when an application blindly assigns user-controlled inputs to object properties without proper validation. This can lead to unauthorized actions, such as privilege escalation or data tampering.

---

## 📚 Definitions and Key Terms
| **Term**                | **Definition**                                                        |
|--------------------------|----------------------------------------------------------------------|
| **Mass Assignment**      | Automatically binding user inputs to object properties without validation. |
| **Whitelisting**         | Explicitly allowing safe properties for assignment.                  |
| **Blacklisting**         | Preventing certain properties from being assigned.                   |
| **Privilege Escalation** | Gaining unauthorized access to higher privileges.                    |
| **Input Validation**     | Ensuring that user inputs adhere to expected formats or values.      |

---

## 🛠️ Detailed Methodology

### 1️⃣ Identifying Vulnerable Endpoints
**Steps**:
1. Inspect API endpoints that accept large JSON payloads.
2. Check for properties like `role`, `isAdmin`, or `privileges` in user-controlled inputs.
3. Submit unexpected or unauthorized values and observe the response.

### 2️⃣ Exploiting Mass Assignment
**Steps**:
1. Modify user inputs to include sensitive properties.
   ```json
   {
       "username": "testuser",
       "isAdmin": true
   }
   ```
2. Submit the modified payload and verify if unauthorized changes are accepted.
3. Test for hidden properties that may not appear in regular forms but are processed by the backend.

### 3️⃣ Mitigating Mass Assignment
- Use whitelisting to restrict assignable properties.
- Validate all inputs at the server level.
- Avoid directly binding user inputs to object models.

---

## 📌 Key Takeaways and Tools

### Tools
- **Burp Suite**: For intercepting and modifying API requests.
- **Postman**: For crafting and testing JSON payloads.
- **Fiddler**: For monitoring and modifying HTTP traffic.
- **Custom Scripts**: For automating payload modifications and testing.

### Key Takeaways
- Always validate user inputs against a whitelist.
- Avoid exposing sensitive fields in JSON responses.
- Log and monitor property changes for anomalies.

---

