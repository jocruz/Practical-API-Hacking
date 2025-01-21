# 📌 High-Level Summary

### **Why is This an Example of BOLA?**

In this lab, the user accessed another person's mechanic report by modifying the `report_id` in the API request (`/workshop/api/mechanic/mechanic_report?report_id=4`). This occurred because the API did not verify whether the user was authorized to view the report, exposing sensitive data like vehicle owner emails and phone numbers.

### **Why is This an Example of BLFA?**

The user bypassed function-level restrictions by modifying the API endpoint from a user-level endpoint (`/identity/api/v2/user/videos/52`) to an admin-level endpoint (`/identity/api/v2/admin/videos/52`). This allowed them to delete another user's video, a privilege reserved for admin users, due to missing authorization checks.

---

# 🎯 Steps to Identify and Exploit

### BOLA:

1. **Capture API Requests:** Use tools like Burp Suite or Postman to inspect API requests for exposed object identifiers (e.g., `report_id`).
2. **Modify Object Identifiers:** Test if altering parameters (e.g., changing `report_id=6` to `report_id=4`) grants unauthorized access to sensitive data.
3. **Confirm Unauthorized Access:** Verify if the response contains data belonging to another user, such as emails, phone numbers, or vehicle details.

### BLFA:

1. **Identify Restricted Actions:** Look for endpoints associated with admin functions (e.g., `DELETE` or `POST` under admin APIs).
2. **Capture and Analyze Requests:** Use Burp Suite to analyze request patterns for regular vs. privileged users.
3. **Modify API Endpoints:** Change user-level endpoints to admin-level endpoints and check if actions (like deleting a video) succeed without proper authorization.

---

# 🔍 What to Look Out for During a Web App Pen Test

1. **Authorization Flaws:**
    
    - Test object-level access by modifying identifiers (e.g., `id`, `user_id`, `vehicleId`).
    - Verify function-level access controls for admin or restricted actions.
2. **Exposed Endpoints:**
    
    - Look for sensitive APIs in response payloads or error messages.
    - Check hidden or undocumented endpoints by analyzing network traffic.
3. **Session and Token Validation:**
    
    - Ensure APIs validate user sessions and tokens for every request.
    - Verify that role-based access controls (RBAC) are implemented correctly.
4. **Error Messages:**
    
    - Inspect error responses for hints about accessible functions or objects.
5. **Predictable Identifiers:**
    
    - Test whether identifiers (e.g., sequential `id` values) can be guessed or iterated.
6. **Tools to Use:**
    
    - **Burp Suite:** For intercepting and analyzing requests.
    - **Postman:** For testing endpoint functionality and parameter manipulation.
    - **OWASP ZAP:** For automated vulnerability scanning.

By systematically testing for BOLA and BLFA, you can identify critical authorization vulnerabilities and ensure proper access controls are enforced.