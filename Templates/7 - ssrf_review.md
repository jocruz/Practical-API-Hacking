
# 🌐 Server-Side Request Forgery (SSRF) Review

## 🧭 High-Level Summary
Server-Side Request Forgery (SSRF) vulnerabilities occur when an attacker can manipulate a server to make unauthorized requests to internal or external resources. This can lead to information disclosure, port scanning, or access to internal services.

---

## 📚 Definitions and Key Terms
| **Term**              | **Definition**                                                         |
|-----------------------|------------------------------------------------------------------------|
| **SSRF**              | Exploiting a server to make unauthorized requests to internal/external endpoints.|
| **Internal Network**  | Private infrastructure not accessible to the public internet.          |
| **Open Redirect**     | Redirecting a request to an unintended or malicious endpoint.          |
| **Metadata Service**  | Internal service that provides details about the environment (e.g., AWS metadata).|

---

## 🛠️ Detailed Methodology

### 1️⃣ Identifying SSRF Vulnerabilities
**Steps**:
1. Inspect endpoints that take URLs as input (e.g., file fetchers, webhooks).
2. Test for unauthorized requests:
   - Example Input: `http://127.0.0.1:80` or `http://localhost/admin`.
3. Observe server responses for indications of successful SSRF.

### 2️⃣ Exploiting Basic SSRF
**Steps**:
1. **Target Internal Services**:
   - Test URLs like `http://169.254.169.254/latest/meta-data/` (AWS Metadata).
2. **Perform Port Scanning**:
   - Modify the target URL to test different ports (e.g., `http://127.0.0.1:22`).
3. **Access Private APIs**:
   - Leverage SSRF to query internal APIs or admin panels.

### 3️⃣ Mitigating SSRF
- Use allowlists to validate acceptable URLs.
- Block requests to private IP ranges (e.g., `127.0.0.0/8`).
- Avoid user-controlled input in server-side requests.

---

## 📌 Key Takeaways and Tools

### Tools
- **Burp Suite**: For intercepting and modifying HTTP requests.
- **FFUF**: For fuzzing SSRF endpoints.
- **HTTP Toolkit**: For testing and analyzing HTTP traffic.
- **Custom Scripts**: To automate SSRF exploitation.

### Key Takeaways
- Always validate URLs against a strict allowlist.
- Test internal services carefully to identify sensitive resources.
- Monitor logs for unusual outbound requests originating from the server.

---

