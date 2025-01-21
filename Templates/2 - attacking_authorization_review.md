
# 🚀 Attacking Authorization

## 🧭 High-Level Summary
Authorization vulnerabilities occur when applications fail to properly enforce access controls. These flaws include:
- **BOLA (Broken Object Level Authorization)**: Users access objects they shouldn’t, such as viewing another user’s data by manipulating object identifiers.
- **BFLA (Broken Function Level Authorization)**: Users access restricted functionality by manipulating roles or API endpoints.

---

## 📚 Definitions and Key Terms
| Term                  | Definition                                                             |
|-----------------------|------------------------------------------------------------------------|
| **BOLA**             | Broken Object Level Authorization; allows unauthorized object access. |
| **BFLA**             | Broken Function Level Authorization; exploits poorly enforced roles.  |
| **IDOR**             | Insecure Direct Object Reference; exposing internal object IDs.       |
| **Access Controls**  | Rules that enforce user permissions for objects or functions.         |
| **Privilege Escalation** | Gaining higher privileges than initially granted.                   |

---

## 🛠️ Detailed Methodology

### 1️⃣ Testing for BOLA
**Steps**:
1. **Identify Object IDs**:
   - Locate parameters such as `userId`, `orderId`, or `docId` in URLs or payloads.
2. **Manipulate IDs**:
   - Increment, decrement, or replace with other known IDs.
   ```http
   GET /api/v1/users/2/profile
   ```
3. **Analyze Responses**:
   - Confirm unauthorized access to another user’s data.

**Common Signs**:
- Access to private resources (e.g., viewing another user’s account or orders).
- Lack of object-level permissions.

---

### 2️⃣ Testing for BFLA
**Steps**:
1. **Identify Restricted Functions**:
   - Look for features like `/admin`, `/delete`, or `/change-role`.
2. **Analyze User Roles**:
   - Determine differences in role permissions (e.g., regular user vs admin).
3. **Modify API Calls**:
   - Use an unauthorized account to attempt restricted actions.
   ```http
   POST /api/v1/admin/delete-user
   ```
4. **Validate Exploitation**:
   - Check if unauthorized functionality is accessible.

**Examples**:
- Regular users performing admin-only actions.
- Manipulating tokens to escalate roles.

---

## 📌 Key Takeaways and Tools

### Tools
- **Burp Suite**: Intercept and modify requests for ID and role tampering.
- **Postman**: Test API endpoints for role-based access.
- **Browser Developer Tools**: Inspect hidden inputs and payloads.

### Key Takeaways
- **Focus on Object IDs**: Test IDs in URLs, payloads, and headers.
- **Role Analysis**: Explore differences between user roles in the application.
- **Error Messages**: Use verbose errors to identify privilege checks.

---
