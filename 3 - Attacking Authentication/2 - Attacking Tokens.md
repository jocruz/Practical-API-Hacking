
---

# 📖 High-Level Summary:

### Analyzing Cookie Predictability:

In this lab, we test the **predictability of cookies** using Burp Suite’s Sequencer. By analyzing the randomness of tokens, we identify weaknesses in their structure, which could allow an attacker to **brute force predictable sections** of the token, gaining unauthorized access.

### Key Findings:

- Initial analysis suggests a significant portion of the token is predictable.
- After **Base64 decoding**, most characters are random, but a small segment remains unique and predictable, making it a target for brute force attacks.

---

# 📚 Definition Bank:

|**Term**|**Definition**|**Key Notes**|
|---|---|---|
|**Sequencer**|A tool in Burp Suite used to analyze the randomness of tokens (e.g., cookies, session tokens).|Measures token randomness and identifies predictable sections.|
|**Base64 Encoding**|A method to encode binary data as text using a set of 64 characters.|Often used for encoding tokens; easily decoded using tools like Burp or `base64` in the terminal.|
|**Brute Force**|An attack method involving exhaustive attempts to guess a value, such as a token or password.|Targeting predictable parts of a token reduces the attack surface.|

---

# 🎯 Steps to Identify Token Predictability:

### Step 1: Logging in and Retrieving the Cookie

1. Use the following credentials:
    
    ```plaintext
    Username: admin
    Password: ramirez
    ```
    
2. Retrieve the **cookie** using one of these methods:
    - **Browser Console:**
        
        ```javascript
        document.cookie
        ```
        
    - **Burp Suite:** Inspect the POST request to `/v1/verify` and check the response body.

---

### Step 2: Sending the Request to Sequencer

1. In Burp Suite, send the POST request to `/v1/verify` to **Sequencer**.
2. Configure the **token location** within the response:
    - Highlight the token to auto-fill the necessary fields.

---

### Step 3: Initial Token Analysis

1. Start **Live Capture** in Sequencer.
2. Once completed, click **Analyze Now**.
3. Review the **Significance Levels**:
    - Characters `0-22` appear predictable (red bars), and `23` is random (green bar).

---

### Step 4: Adjust Analysis Options

1. Navigate to **Analysis Options** and enable:
    
    ```plaintext
    [✔] Base64-decode before analyzing
    ```
    
2. Reanalyze the tokens:
    - Characters `0-14` are random, while `15-17` are unique and predictable.

---

### Step 5: Decoding the Token

1. Decode the token using Burp Suite's **Decoder** or the terminal:
    
    ```bash
    echo -n 'YWRtaW4MTA6MzY6MTctY3N2' | base64 -d
    ```
    
    **Output:**
    
    ```plaintext
    admin-10:36:17-csv
    ```


---

### Step 6: Saving and Investigating Tokens

1. Save the tokens from Sequencer:
    - Export them to a file (e.g., `tokens.txt`).
2. Review the tokens:
    
    ```bash
    head -n 100 tokens.txt
    ```
    
3. Analyze patterns:
    - Beginning characters (`admin-timeoflogin`) remain constant.
    - The last three characters are random (e.g., `csv`, `wyr`).

---

# ✅ Key Takeaways:

### Why This Token is Vulnerable:

- **Predictable Structure:** The first part of the token (`admin-timeoflogin`) is predictable.
- **Limited Randomness:** Only the last three characters introduce randomness, reducing the token's entropy.

### Exploitation Strategy:

- Focus brute-forcing efforts on the **last three characters** (`csv`, `wyr`, etc.), as identified by Sequencer and manual analysis.
- Use tools like Burp Intruder or custom scripts for efficient brute force attacks.

---