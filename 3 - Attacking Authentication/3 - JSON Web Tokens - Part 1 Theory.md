
---

# 📖 High-Level Summary:

This lab demonstrates how to use the `curl` command to interact with an API by logging in, retrieving a JSON Web Token (JWT), and using the token to access protected resources. The instructor emphasizes the importance of decoding JWTs using **jwt.io** and the security implications if the signing secret is known or guessed.

---

# 📚 Definition Bank:

|**Term**|**Definition**|**Key Notes**|
|---|---|---|
|**curl**|A command-line tool for transferring data with URLs.|Supports HTTP requests like GET, POST, and more, often used for API interaction and testing.|
|**JWT (JSON Web Token)**|A token format containing encoded header, payload, and signature sections.|Used for stateless authentication. Can be decoded at [jwt.io](https://jwt.io/).|
|**Authorization Header**|An HTTP header used to send credentials (e.g., bearer tokens) to authenticate with APIs.|Example: `Authorization: Bearer <token>`|
|**`-X` flag in curl**|Specifies the HTTP request method (e.g., POST, GET).|Default is GET.|
|**`--header` flag**|Adds custom headers to an HTTP request (e.g., Content-Type, Authorization).|Example: `--header "Authorization: Bearer <token>"`|
|**`--data` flag**|Sends data in the request body, typically for POST requests.|Example: `--data '{"key": "value"}'`|
|**`-i` flag in curl**|Includes the **HTTP headers** in the response output.|Useful for viewing status codes, content types, and response headers.|

---

# 🎯 Steps and Commands:

### Step 1: Login with Valid Credentials

- Use the `curl` command to log in and retrieve a **JWT** token:
    
    ```bash
    curl -i -X POST http://localhost/login --header "Content-Type: application/json" --data '{"username": "user", "password": "user"}'
    ```
    
- **Response (with headers):**
    
```http
    HTTP/1.1 200 OK
    Content-Type: application/json; charset=utf-8
    {
      "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VyIjoidXNlciIsIWF0IjoxNjc1MzQ3Mjk3fQ.A0K78mjU2F6mO7DtA4ryO21PJxqDLhlha4AhP18BIsM"
    }
```


---

### Step 2: Login with Invalid Credentials

- Test login with incorrect credentials:
    
    ```bash
    curl -i -X POST http://localhost/login --header "Content-Type: application/json" --data '{"username": "user", "password": "user123"}'
    ```
    
- **Response:**
    
```http
    HTTP/1.1 401 Unauthorized
    Content-Type: application/json; charset=utf-8
    {
      "message": "The username and password you provided are invalid"
    }
```


---

### Step 3: Access the Dashboard with a Valid JWT

- Use the retrieved JWT to access a protected resource:
    
    ```bash
    curl -i localhost/dashboard --header 'Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VyIjoidXNlciIsIWF0IjoxNjc1MzQ3Mjk3fQ.A0K78mjU2F6mO7DtA4ryO21PJxqDLhlha4AhP18BIsM'
    ```
    
- **Response (with headers):**
    
```http
    HTTP/1.1 200 OK
    Content-Type: application/json; charset=utf-8
    {
      "message": "Welcome to your dashboard user"
    }
```


---

# 🔍 JWT Analysis:

### Decoding the Token:

1. Copy the token:  
    `eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VyIjoidXNlciIsIWF0IjoxNjc1MzQ3Mjk3fQ.A0K78mjU2F6mO7DtA4ryO21PJxqDLhlha4AhP18BIsM`
2. Go to [jwt.io](https://jwt.io/) and paste the token to decode it.
3. **Decoded Payload:**
    
    ```json
    {
      "userid": "user",
      "iat": 1675347297
    }
    ```
    

### Modifying the Payload:

- Change the payload to:
    
    ```json
    {
      "userid": "admin",
      "iat": 1675347297
    }
    ```
    
- Re-sign the token with the known secret.

---

# ✅ Key Takeaways:

- **curl's `-i` flag**: Essential for viewing HTTP headers alongside the response body, useful for debugging and testing.
- **JWT Vulnerability:** If the secret used to sign tokens is known or guessed, attackers can manipulate the payload to escalate privileges.
- **Authorization Header:** Always required to access protected resources using tokens.

---

