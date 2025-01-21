# 📌 High-Level Summary

In this lab, we exploit a vulnerability by brute-forcing the **three random characters** in a Base64-encoded user cookie. By generating permutations for the cookie's suffix and testing them using tools like **ffuf**, we identify the valid token, decode it, and gain unauthorized access to another user’s account. The use of Base64 decoding and padding (`=` or `==`) is critical for proper token manipulation.

---

# 🎯 Steps to Exploit the Vulnerability

### **Step 1: Decode Your Cookie**

- Decode your own cookie to retrieve the predictable structure:
    
    ```bash
    echo -n 'Base64EncodedCookie' | base64 -d
    ```
    
    - **Example Output:** `username-timestamp-<random_chars>`

### **Step 2: Generate Tokens**

- Use a Python script to create all possible permutations of the random suffix, encode them in Base64, and save them to a file.

### **Step 3: Test Tokens with ffuf**

- Use **ffuf** to test the generated tokens against the vulnerable API endpoint:
    
    ```bash
    ffuf -request req.txt -w tokens.txt -fs <response_size>
    ```
    
    - Filter by response size to identify the valid token.

### **Step 4: Decode the Valid Token**

- Decode the identified Base64 token to verify its structure:
    
    ```bash
    echo -n 'ValidBase64Token' | base64 -d
    ```
    

### **Step 5: Exploit the Cookie**

- Use the valid token in your browser to impersonate the target user.

---

# 🔍 Key Indicators of the Vulnerability

1. **Predictable Cookie Structure:** The cookie follows a pattern (`username-timestamp-<random_chars>`) that simplifies brute-forcing.
2. **Exposed Base64 Encoding:** The token is Base64-encoded, which is easily reversible.
3. **Lack of Validation:** The server does not sufficiently validate the authenticity of the decoded token, allowing brute force to succeed.

---

# 🛠️ What to Look Out for in Web App Pen Tests

1. **Weak or Predictable Tokens:**
    
    - Look for Base64-encoded strings or other easily reversible encoding.
    - Analyze cookie structures for patterns or predictable components.
2. **Insufficient Rate Limiting:**
    
    - Test whether brute-force attacks on tokens or credentials are possible without triggering account lockouts or rate limits.
3. **Response Behavior:**
    
    - Use tools like **ffuf** to analyze response sizes, headers, or content for patterns that indicate success or failure.
4. **Base64 Padding and Decoding:**
    
    - Check for errors caused by missing padding (`=` or `==`) in Base64 strings, which can reveal improper token handling.
5. **Automation Opportunities:**
    
    - Automate brute-force attacks using scripts and tools to test for permutations of tokens or other vulnerable inputs efficiently.

By systematically applying these steps and observations, you can identify and exploit vulnerabilities similar to the one demonstrated in this lab.