
---

## 📖 High-Level Summary:

This methodology involves identifying **hidden API endpoints** by inspecting JavaScript files within a web application. The process uses **Burp Suite**, **browser dev tools**, and **code beautification tools** to uncover API paths and services that may not be immediately visible.

---

## 📚 Definition Bank:

### 🛠️ Key Steps and Tools Explained:

- **Burp Suite:** A web vulnerability scanner and proxy tool used to capture and inspect HTTP traffic between the browser and the server.
- **Browser Inspector (DevTools):** The browser's built-in tool to inspect network traffic, JavaScript files, and application resources. (Shortcut: `Ctrl + Shift + I`)
- **JavaScript (JS) Files:** Files that may contain client-side logic, including API endpoints and configurations.
- **Beautifier.io:** A free online tool used to format and beautify obfuscated or minified JavaScript code for better readability.
- **Minified JavaScript:** JavaScript files compressed into a single line of code, often hard to read manually.

---

## 🎯 Step-by-Step Process:

### 1. Capture Traffic with Burp Suite:

- Launch **Burp Suite** and enable the **intercept proxy**.
- Browse through the application normally.
- Review traffic captured in **HTTP history** to identify API endpoints.

---

### 2. Inspect the Application Using Browser DevTools:

- Open the **Browser Inspector** (`Ctrl + Shift + I`).
- Go to the **Sources Tab**.
- Navigate to:  
    `Main Thread` → `localhost:8888` → `modules` → `utils`
- **Action:** Skim through JavaScript files for clues, especially those named **config.js**.

---

### 3. Identifying API Endpoints in `config.js`:

Upon discovering a file named `config.js`, the instructor finds the following export:

```javascript
export const crapienv = {
    JAVA_MICRO_SERVICES: "identity/",
    PYTHON_MICRO_SERVICES: "workshop/",
    GO_MICRO_SERVICES: "community/",
}
```

- These values reveal **top-level API paths** for the application's microservices.
- ✅ **Key Insight:** Each value maps to a **service route**, indicating API segmentation.

---

### 4. Dealing with Obfuscated/Minified JavaScript Files:

- The instructor discovers a **minified** file: `main.e9428675.chunk.js`.
- **Minified JavaScript:** This file is difficult to read because it's compressed into a single line.
- **Solution:** Copy the contents and paste it into **[beautifier.io](https://beautifier.io/)** to make it human-readable.

---

### 5. Extracting API Endpoints Post-Beautification:

- Once beautified, the instructor scans through the formatted code.
- Endpoints related to the **crAPI** are identified and can be tested via **Postman** for further enumeration.

---

## ✅ When to Use This Technique:

- 🔍 **API Discovery:** To find hidden or undocumented API endpoints.
- 🔐 **Security Testing:** Identifying potentially exposed services for pen testing.
- 🧩 **Reverse Engineering:** Understanding the structure of a web application's microservices.
- 📦 **Microservice Enumeration:** Detecting specific services handling different tasks (e.g., Identity, Workshop, Community).

---