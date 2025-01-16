
---

# 📖 High-Level Summary:

In this lab, we brute force the **three random characters** in a user’s cookie by leveraging known patterns and **Base64 decoding**. By generating all possible permutations for the cookie's suffix and testing them, we identify the valid token, decode it, and gain access to another user’s account. The double `=` in Base64 encoding is crucial for padding to ensure the proper decoding of the string.

---

# 📚 Definition Bank:

|**Term**|**Definition**|**Key Notes**|
|---|---|---|
|**Base64 Encoding**|A method to encode binary data into a text format using 64 characters.|Commonly used for encoding tokens or cookies. Requires padding (`=`) to ensure proper decoding.|
|**Cookie**|A small piece of data stored in the browser used for session management or user authentication.|Here, it’s structured as `jeremy-<timestamp>-<random_chars>`.|
|**ffuf**|A command-line tool for web fuzzing to find valid inputs by testing various payloads.|Used here to brute force the correct cookie by testing generated Base64 tokens.|
|**`-fs` flag**|Filters results by matching the size of the response body.|Helps identify the valid response among many.|

---

# 🎯 Steps and Commands:

### Step 1: Base64 Decode Your Cookie

1. **Decode your own cookie to retrieve the timestamp:**
    
    ```bash
    echo -n 'Base64EncodedCookie' | base64 -d
    ```
    
    - **Example Output:**
        
        ```plaintext
        jeremy-15:29:08-abc
        ```
        
    - **Explanation:** The timestamp is part of the known cookie structure: `jeremy-<timestamp>-<random_chars>`.

---

### Step 2: Generate Possible Tokens

1. Use the following Python script to generate all permutations of the cookie:
    
```python
import base64
from itertools import product

chars = 'abcdefghijklmnopqrstuvwxyz'
prefix = 'jeremy-15:29:08-'

# Generate permutations using itertools.product
perms = (prefix + ''.join(p) for p in product(chars, repeat=3))

# Encode each permutation in base64 and print
for perm in perms:
    print(base64.b64encode(perm.encode()).decode())
```

---

### **Explanation of the Script**

This script generates Base64-encoded strings based on a known cookie structure (`jeremy-15:29:08-`) followed by all possible combinations of three lowercase letters (`a-z`). Here's how it works:

1. **Define the Character Pool (`chars`)**:
    
    - The string `chars` contains all lowercase letters (`'a'` to `'z'`).
    - These letters will be used to create the three-character suffix.
2. **Specify the Cookie Prefix (`prefix`)**:
    
    - The `prefix` (`'jeremy-15:29:08-'`) represents the fixed part of the cookie structure that precedes the random characters.
3. **Generate All Possible Suffixes (`product`)**:
    
    - Using `itertools.product(chars, repeat=3)`, the script generates every combination of three letters.
    - For example:
        - `('a', 'a', 'a')`
        - `('a', 'a', 'b')`
        - ...
        - `('z', 'z', 'z')`.
4. **Combine Prefix and Suffix**:
    
    - For each combination of three letters, the script appends the suffix to the prefix, forming complete strings such as:
        - `"jeremy-15:29:08-aaa"`
        - `"jeremy-15:29:08-aab"`
        - `"jeremy-15:29:08-aac"`
5. **Encode Strings in Base64**:
    
    - Each complete string is converted to bytes using `.encode()`.
    - The bytes are then encoded into Base64 format using `base64.b64encode()`.
    - The result is a Base64-encoded string, such as:
        - `"amVyZW15LTE1OjI5OjA4LWFhYQ=="`
6. **Save Encoded Strings to a File**:
    
    - The script opens a file (`tokens.txt`) in write mode.
    - Each Base64-encoded string is written to the file on a new line.
7. **Output**:
    
    - The file `tokens.txt` will contain a list of all possible Base64-encoded cookie strings, each on a separate line.

---

### **Example Flow**

Here’s what happens for a single iteration:

1. A suffix `('a', 'a', 'a')` is generated.
2. It’s combined with the prefix: `"jeremy-15:29:08-aaa"`.
3. The string is encoded into Base64: `"amVyZW15LTE1OjI5OjA4LWFhYQ=="`.
4. The encoded string is written to `tokens.txt`.

---

### **Script’s Purpose**

This script automates the generation of Base64-encoded cookies for all combinations of three random characters appended to a fixed prefix, storing the results in a file for later use or analysis.

---

### Step 3: Use ffuf to Test Tokens

1. Run the following **ffuf** command:
    
    ```bash
    ffuf -request req.txt -request-proto http -w tokens.txt -fs 834
    ```
    
    - **Flags Used:**
        - `-request req.txt`: Specifies the HTTP request template.
        - `-request-proto http`: Indicates the HTTP protocol for the request.
        - `-w tokens.txt`: Specifies the wordlist containing Base64-encoded tokens.
        - `-fs 834`: Filters responses by their size (834 bytes in this case).
2. **Identify the Valid Token:**
    
    - Look for the token with a `200` status code and the expected response size (e.g., `1741` bytes).

---

### Step 4: Decode the Valid Token

1. Use the valid token to decode and verify its contents:
    
    ```bash
    echo -n 'amVyZW15LTE10jI50jA4LWZxZA==' | base64 -d
    ```
    
    - **Output:**
        
        ```plaintext
        jeremy-15:29:08-fqd
        ```


---

### Step 5: Use the Valid Cookie

1. Add the cookie to your browser and refresh the page to log in as the target user.

---

# 🔍 Why is the Double Equal Sign (`==`) Used in Base64 Encoding?

- Base64 encoding ensures that the encoded string's length is a multiple of 4.
- The `=` or `==` serves as **padding**:
    - **`=`:** Adds 1 byte of padding.
    - **`==`:** Adds 2 bytes of padding.
- In this lab, the `==` is required for proper decoding of the cookie, as omitting it would lead to errors or truncated output.

---

# ✅ Key Takeaways:

1. **Pattern Recognition:** The cookie structure is predictable (`username-timestamp-random`), reducing the attack surface for brute force.
2. **Base64 Encoding:** Padding is essential for accurate decoding and validating the token format.
3. **Python Automation:** Generating permutations programmatically saves time and effort in brute-forcing tokens.
4. **ffuf Efficiency:** Filters like `-fs` narrow down valid responses, streamlining the brute-forcing process.
