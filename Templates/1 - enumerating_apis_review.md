
# 🚀 Enumerating APIs

## 🧭 High-Level Summary
API enumeration involves discovering endpoints, parameters, and hidden functionalities within web applications. This process helps uncover potential vulnerabilities such as unauthorized access, insecure data exposure, or injection flaws.

Key strategies include:
- **Fuzzing**: Sending automated requests to identify hidden endpoints.
- **Source Code Analysis**: Reviewing exposed code for API calls, keys, and other sensitive information.

---

## 📚 Definitions and Key Terms
| Term                  | Definition                                                   |
|-----------------------|-------------------------------------------------------------|
| **API Fuzzing**       | Automated testing by sending unexpected inputs to an API.    |
| **Endpoints**         | Specific URLs where the server processes API requests.       |
| **Headers**           | Key-value pairs in HTTP requests containing metadata.        |
| **Rate Limiting**     | Controls to limit API requests per user or IP address.       |
| **Error Messages**    | Server responses that may leak sensitive implementation details.|

---

## 🛠️ Detailed Methodology

### 1️⃣ API Fuzzing
**Steps**:
1. **Prepare Wordlists**:
   - Use tools like `SecLists` or custom wordlists for endpoint discovery.
2. **Run Fuzzing Tools**:
   - Command:
     ```bash
     ffuf -u https://example.com/FUZZ -w /path/to/wordlist.txt -X GET
     ```
3. **Analyze Responses**:
   - Identify endpoints based on response status codes (e.g., `200`, `403`, `500`).
4. **Log Findings**:
   - Document discovered endpoints and their observed behavior.

**Common Issues**:
- Missing authentication on sensitive endpoints (e.g., `/admin`).
- Parameter tampering leading to unauthorized actions.

### 2️⃣ Discovery via Source Code
**Steps**:
1. **Inspect Source Code**:
   - Use browser developer tools (`Ctrl+U`) or download the source files.
2. **Search for Keywords**:
   - Look for terms like `apiKey`, `fetch`, or `$.ajax`.
3. **Analyze API Calls**:
   - Note endpoints, methods (`GET`, `POST`, etc.), and parameters.
4. **Test the Endpoints**:
   - Use tools like **Burp Suite** to intercept and modify requests.

**Pro Tip**:
- Automate search with tools like `grep`:
  ```bash
  grep -iR 'api' /path/to/source
  ```

---

## 📌 Key Takeaways and Tools

### Tools
- **FFUF**: Fuzzing for hidden endpoints.
- **Burp Suite**: Intercepting and modifying API traffic.
- **SecLists**: Pre-built wordlists for discovery.
- **Developer Tools**: Viewing and analyzing source code.

### Key Takeaways
- Always test endpoints with various HTTP methods.
- Look for API keys or sensitive tokens in source code comments.
- Test discovered endpoints for common vulnerabilities like IDOR or XSS.

---

