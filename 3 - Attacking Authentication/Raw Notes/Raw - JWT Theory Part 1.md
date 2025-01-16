
---
The instructor just shows us how to utilize the curl command on how to use curl, and how to use the token in curl and how to go to jwt.io to decode the token to identify the header, payload and verify the signature

he just makes it a point that if we verify the signature and know the secret if we guess that right then the payload

which is

"userid":""user"
"iat": 1675347297

to 

"userid":"admin"
"iat": 1675347297

if we know the secret then we would technicanlly be able to get access to the admin user /dashboard


From there 
### Commands and Outputs:

#### 1. Login with valid credentials:

```bash
curl -X POST http://localhost/login --header "Content-Type: application/json" --data '{"username": "user", "password": "user"}'
```

**Output:**

```json
{
  "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VyIjoidXNlciIsIWF0IjoxNjc1MzQ3Mjk3fQ.A0K78mjU2F6mO7DtA4ryO21PJxqDLhlha4AhP18BIsM"
}
```

---

#### 2. Login with invalid credentials:

```bash
curl -X POST http://localhost/login --header "Content-Type: application/json" --data '{"username": "user", "password": "user123"}'
```

**Output:**

```json
{
  "message": "The username and password you provided are invalid"
}
```

---

#### 3. Access the dashboard with a valid JWT:

```bash
curl -i localhost/dashboard --Header 'Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VyIjoidXNlciIsIWF0IjoxNjc1MzQ3Mjk3fQ.A0K78mjU2F6mO7DtA4ryO21PJxqDLhlha4AhP18BIsM'
```

**Output:**

```http
HTTP/1.1 200 OK
X-Powered-By: Express
Content-Type: application/json; charset=utf-8
Content-Length: 44
ETag: W/"2c-hi3G+J0BbQUuFlADhNRaHxSg3EI"
Date: Thu, 02 Feb 2023 14:15:26 GMT
Connection: keep-alive
Keep-Alive: timeout=5

{
  "message": "Welcome to your dashboard user"
}
```

---