# 📖 High-Level Summary:

Server-Side Request Forgery (SSRF) is a web security vulnerability that allows an attacker to manipulate a server to make HTTP requests to unintended domains or services. This can lead to the exposure of internal systems, sensitive data, or even unintended actions performed on the server's behalf. By exploiting poorly validated user inputs, attackers can target internal infrastructure or access unauthorized resources.

---

# 📚 Definition Bank:

|**Term**|**Definition**|**Key Notes**|
|---|---|---|
|**SSRF**|A vulnerability that lets attackers make the server send HTTP requests to any URL.|Often used to access internal resources or sensitive endpoints.|
|**Parameter Tampering**|Manipulating query parameters in a request to bypass restrictions or trigger vulnerabilities.|A common method for testing SSRF vulnerabilities.|
|**`file:///` Protocol**|A URL scheme used to access local files on the server.|Exploited to read sensitive files like `/etc/passwd`.|
|**Whitelist Domains**|A security measure that restricts URL inputs to a predefined list of allowed domains.|A primary defense mechanism against SSRF attacks.|
|**Port Scanning**|Testing specific ports to identify open or vulnerable services.|Exploited in SSRF to map internal networks.|

---

# 💡 What is SSRF?

SSRF occurs when a web application accepts user input and makes server-side HTTP requests without proper validation.

### Key Features:

- **Exploits Trust**: The server may have access to resources that are otherwise inaccessible to the attacker.
- **Flexible Targets**: Can target internal systems (e.g., `localhost` or private IP ranges) or external services.

---

# 🎯 Steps and Commands:

### Step 1: Identifying SSRF Vulnerability

1. **Scenario**:
    - The application has a feature to fetch resources from a URL (e.g., testing if a website is live).
    - Normal usage:
        
        ```plaintext
        http://example.com/resource?url=http://test.com
        ```
        
    - Injected payload:
        
        ```plaintext
        http://example.com/resource?url=http://127.0.0.1:8080/admin
        ```
        
    - Result: The server accesses internal resources that should not be exposed.

---

### Step 2: Testing with Tools

#### 1. Using `curl`:

- Send a request to a potentially vulnerable parameter:
    
    ```bash
    curl -X GET "http://example.com/resource?url=http://127.0.0.1:80"
    ```
    
- Observe the response for indicators of internal service interaction.

#### 2. Using Burp Suite:

- Capture the request and modify the URL parameter in **Repeater**:
    
    ```plaintext
    GET /resource?url=http://127.0.0.1:22 HTTP/1.1
    ```
    
- Analyze the server’s response to identify SSRF vulnerabilities.

#### 3. Using ffuf:

- Fuzz the URL parameter to discover exploitable endpoints:
    
    ```bash
    ffuf -u http://example.com/resource?url=FUZZ -w /usr/share/seclists/Fuzzing/URL.txt
    ```
    

---

### Step 3: Exploitation Techniques

1. **Access Local Files**:
    
    - Payload:
        
        ```plaintext
        http://example.com/resource?url=file:///etc/passwd
        ```
        
    - Expected Response:
        - The contents of the `/etc/passwd` file, if accessible.
2. **Internal Port Scanning**:
    
    - Test internal services by injecting URLs with ports:
        
        ```plaintext
        http://example.com/resource?url=http://127.0.0.1:3306
        ```
        
    - Results:
        - Open ports may return service banners or specific error messages indicating active services.

---

### Step 4: Preventing SSRF

1. **Implement Whitelisting**:
    
    - Restrict inputs to a known list of trusted domains.
    - Example:
        
        ```plaintext
        Allow: example.com, api.example.com
        ```
        
2. **URL Validation**:
    
    - Ensure user-provided URLs resolve to the expected domain using DNS validation.
3. **Limit Server Permissions**:
    
    - Restrict the server's access to internal or sensitive resources.

---

# 🔍 Visual Representation of SSRF:

1. **Normal Request Flow**:
    
    ```plaintext
    User Input → Server Fetches URL → Valid Response
    ```
    
2. **Malicious Request Flow**:
    
    ```plaintext
    User Input → Server Fetches Internal Resource → Unauthorized Access
    ```
    

---

# ✅ Key Takeaways:

1. **SSRF is Dangerous**:
    - It leverages the server's trust and access to internal systems, potentially exposing sensitive information.
2. **Testing Tools**:
    - Tools like `curl`, **Burp Suite**, and **ffuf** are effective for identifying and exploiting SSRF vulnerabilities.
3. **Mitigation**:
    - Use domain whitelisting, input validation, and restricted permissions to defend against SSRF.

---