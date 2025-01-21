# 📖 BOLA Exploitation Walkthrough – crAPI Labs

# 📌 Why is This an Example of BOLA?

In the crAPI Labs example, the vulnerability exists because the API endpoint `/identity/api/v2/vehicle/{vehicleId}/location` allows access to vehicle data based solely on the `vehicleId` provided in the request. This means:

1. **Authorization Check Missing:** The API does not verify if the requester is authorized to access the data for the given `vehicleId`.
2. **Direct Object Reference:** The `vehicleId` is exposed in the forum response, enabling attackers to replace it with another user’s ID and access their sensitive data.

This lack of proper authorization validation directly illustrates a **Broken Object Level Authorization (BOLA)** flaw.

---

# 📚 Definition Bank

### BOLA (Broken Object Level Authorization)

- **Definition:** A vulnerability where an attacker accesses objects they are not authorized to access by modifying object identifiers in API requests.
- **Impact:** Unauthorized data access, privacy violations, and potential data breaches.
- **Example:** Changing a `vehicleId` in an API request to view another user’s vehicle details.

---

# 🎯 Steps to Identify and Exploit BOLA – crAPI Labs Walkthrough

### Logging In and Initial Exploration

- **Login Credentials:** [john@test.com](mailto:john@test.com)
- **Dashboard URL:**
    
    ```plaintext
    http://localhost:8888/dashboard
    ```
    
- Displays John’s vehicle details:
    
    ```
    VIN: 1GIGF95XMMN431588  
    Company: Mercedes-Benz  
    Model: GLA Class  
    Fuel Type: PETROL  
    Year: 2025  
    ```
    

### Navigating to the Community Forum

- Access the forum via:
    
    ```plaintext
    http://localhost:8888/forum
    ```
    
- View forum posts, each linked with a unique `post_id` in the URL:
    
    ```plaintext
    http://localhost:8888/post?post_id=p9YNVj5mQG9CssvvHfQrMm
    ```
    

### Capturing Traffic with Burp Suite

- Capture and inspect API requests using **Burp Suite**.
- Example Request:
    
    ```http
    GET /community/api/v2/community/posts/recent?limit=30&offset=0
    ```
    
- Response reveals sensitive data, including `vehicleId`, `email`, and `nickname` of other users.

### Testing for BOLA by Manipulating Vehicle IDs

- Original Request:
    
    ```http
    GET /identity/api/v2/vehicle/80c2d9f1-159f-4f79-a77d-ccf0ec47efb7/location
    ```
    
    Returns John’s own vehicle data.
    
- Modified Request: Replace John’s `vehicleId` with another user’s `vehicleId` captured from the forum data:
    
    ```http
    GET /identity/api/v2/vehicle/4bae9968-ec7f-4de3-a3a0-ba1b2ab5e5e5/location
    ```
    
    Returns sensitive data of another user, including:
    
    - Vehicle location (latitude, longitude)
    - Full name
    - Email

---

# ❓ Why is This a BOLA Vulnerability?

### Key Indicators

1. **Exposed Identifiers:**  
    `vehicleId` is exposed in the `/community/api/v2/community/posts/recent` response, making it vulnerable to tampering.
    
2. **Insufficient Authorization:**  
    The endpoint `/identity/api/v2/vehicle/{vehicleId}/location` does not validate the requester’s permissions to access the data.
    
3. **Direct Object Reference:**  
    Access is granted solely based on the `vehicleId`, with no additional checks for session tokens or user validation.
    

### Impact

- **Data Exposure:** Unauthorized access to user-specific information like names, emails, and vehicle locations.
- **Privacy Violation:** Critical data leakage enabling potential misuse.
- **Physical Risk:** Live vehicle tracking data can be exploited for malicious purposes.

---

# 🔍 Direct Example of Attack Authorization in the Lab

- **Lab Example:**  
    Exploiting the `/identity/api/v2/vehicle/{vehicleId}/location` endpoint by replacing John’s `vehicleId` with another user’s identifier to access their private vehicle data.
    
- **Key Request:**
    
    ```http
    GET /identity/api/v2/vehicle/4bae9968-ec7f-4de3-a3a0-ba1b2ab5e5e5/location
    ```
    

---

# ✅ Key Takeaways

- **BOLA Vulnerabilities:** Occur when API endpoints lack proper authorization checks for object-level access.
- **Mitigation Strategies:**
    - Validate user permissions server-side before processing API requests.
    - Implement token-based access controls and avoid exposing predictable identifiers.
    - Regularly test APIs for authorization flaws to prevent exploitation.

---

This structure is ready to transfer into your Obsidian notes for quick reference.