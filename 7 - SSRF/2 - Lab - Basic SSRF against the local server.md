# 📖 High-Level Summary

This lab demonstrates **Server-Side Request Forgery (SSRF)**, where an attacker manipulates the stock check feature of the web application to access an internal admin interface. The attacker uses this access to issue an admin-level command to delete the user "carlos," bypassing authentication mechanisms. SSRF is exploited by modifying the `stockAPI` parameter in the intercepted request.

---

# 📚 Definition Bank

|**Term**|**Definition**|**Key Notes**|
|---|---|---|
|**SSRF (Server-Side Request Forgery)**|A vulnerability where an attacker forces the server to make HTTP requests to unintended destinations.|Often used to access internal resources or execute unauthorized actions.|
|**`stockAPI` Parameter**|A parameter in the HTTP request body that specifies the URL the server will fetch stock data from.|Modified to point to internal admin endpoints during the attack.|
|**302 Found (Redirection)**|An HTTP status code indicating that the resource has been temporarily moved to another location.|Used in this lab after deleting the user to indicate redirection.|
|**Follow Redirection**|A Burp Suite feature to automatically follow 302 responses and view the redirected resource.|Demonstrates the outcome of the SSRF attack.|

---

# 💡 What is SSRF?

**Server-Side Request Forgery (SSRF)** allows attackers to manipulate a vulnerable server to send requests to unauthorized internal or external destinations.

### Why This Lab is SSRF:

- The application trusts user-supplied data in the `stockAPI` parameter without proper validation.
- By modifying this parameter, attackers can direct the server to access restricted resources like the admin panel (`http://localhost/admin`).
- The attacker leverages this to perform admin-only actions (e.g., deleting the user "carlos").

---

# 🛠️ Steps to Solve the Lab

### 🔹 **PortSwigger Solution**:

1. **Access the Stock Check Feature**:
    
    - Navigate to a product page and click **Check stock** to trigger the stock check functionality.
2. **Intercept and Modify the Request**:
    
    - Use Burp Suite to intercept the stock check request.
    - Send the intercepted POST request (with the URL `/product/stock`) to Burp Repeater.
3. **Modify the `stockAPI` Parameter**:
    
    - Change the `stockAPI` parameter to:
        
        ```plaintext
        http://localhost/admin
        ```
        
    - Send the request to confirm access to the admin panel.
4. **Delete the User "Carlos"**:
    
    - Read the HTML response to find the delete user endpoint:
        
        ```plaintext
        http://localhost/admin/delete?username=carlos
        ```
        
    - Update the `stockAPI` parameter with this URL:
        
        ```plaintext
        http://localhost/admin/delete?username=carlos
        ```
        
    - Send the request to complete the attack.

---

### 🔹 **Instructor Solution**:

1. **Intercept the Stock Check Request**:
    
    - Go to a product page and click **Check stock**.
    - Use Burp Suite to capture the POST request with the URL `/product/stock`.
    - The body of the request contains:
        
        ```plaintext
        stockAPI=http://...
        ```
        
2. **Modify the `stockAPI` Parameter**:
    
    - Change the `stockAPI` parameter to:
        
        ```plaintext
        http://localhost/admin?productId=1&storeId=1
        ```
        
    - Send the request and observe the response. A partial admin panel is displayed.
3. **Identify the Delete Endpoint**:
    
    - Examine the admin panel's HTML response to locate the "Delete" button.
    - The button points to the following endpoint:
        
        ```plaintext
        /admin/delete?username=carlos
        ```
        
4. **Execute the Delete Request**:
    
    - Update the `stockAPI` parameter with the delete endpoint:
        
        ```plaintext
        http://localhost/admin/delete?username=carlos&storeId=1
        ```
        
    - Send the request via Burp Repeater.
5. **Handle the Redirection**:
    
    - Observe the response with status code `302 Found` and follow the redirection.
    - Although the redirected page is unauthorized, the deletion action has been successfully performed.

---

# 🔍 Key Takeaways:

1. **Understanding SSRF**:
    
    - SSRF exploits the server's trust in user-supplied URLs to access restricted resources.
    - In this lab, the `stockAPI` parameter was the entry point for the attack.
2. **Validating SSRF Exploitation**:
    
    - The admin interface (`/admin`) was inaccessible directly but was successfully reached by modifying the `stockAPI` parameter.
    - The attacker issued admin commands (e.g., deleting "carlos") through the vulnerable stock check feature.
3. **Importance of Input Validation**:
    
    - Proper validation of user-supplied data could prevent this attack.
    - Restrict the `stockAPI` parameter to only accept URLs pointing to legitimate stock-check endpoints.

---