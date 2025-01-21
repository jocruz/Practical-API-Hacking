# 📌 High-Level Summary

This lab demonstrates a **Mass Assignment Attack**, where client-provided data is automatically mapped to internal application objects without proper validation. By injecting unauthorized fields in a POST request, we added an invalid product to the application with a manipulated price and image.

---

# 🎯 What to Look Out For During Testing

1. **Automatic Data Mapping:**
    
    - Test endpoints that accept JSON payloads to identify if input fields are directly mapped to internal objects.
    - Look for responses indicating required fields or available object properties.
2. **Input Validation:**
    
    - Check if the application validates critical fields (e.g., `price`, `roles`, or `permissions`).
    - Test with invalid or unauthorized values (e.g., negative prices or unauthorized roles).
3. **Exploitable Endpoints:**
    
    - Identify endpoints that accept POST, PUT, or PATCH requests.
    - Use tools like **ffuf** to fuzz HTTP methods and discover accessible endpoints.
4. **Unrestricted Fields:**
    
    - Test adding extra fields to the request payload to observe if they are processed or stored by the application.

---

# ✅ Key Steps for Exploitation

1. **Fuzz HTTP Methods:**
    
    - Use **ffuf** to discover which HTTP methods (POST, PUT, etc.) are supported:
        
        ```bash
        ffuf -u http://localhost/endpoint -w http-methods.txt -X FUZZ -fc 405
        ```
        
2. **Analyze Responses:**
    
    - Identify required fields by observing error messages from incomplete requests.
3. **Inject Unauthorized Fields:**
    
    - Craft a payload with additional fields (e.g., `name`, `price`, `roles`) to test for unrestricted mass assignment:
        
        ```json
        {
          "name": "cheesecake",
          "price": "-500",
          "image_url": "http://example.com/image.jpg"
        }
        ```
        
4. **Validate Exploitation:**
    
    - Confirm the application processes the injected fields (e.g., displaying an unauthorized product).

---

# ✅ Key Takeaways

- **Validate Client Input:** Applications must validate and sanitize all incoming data to prevent unauthorized field manipulation.
- **Restrict Assignable Fields:** Use allowlists or frameworks that enforce strict control over which fields can be updated via client requests.
- **Discover Vulnerable Endpoints:** Tools like **ffuf** are invaluable for identifying supported HTTP methods and exploitable API endpoints.

By testing for mass assignment vulnerabilities, you can uncover serious risks that allow attackers to manipulate application behavior and data integrity.