### **API Documentation**

| **Endpoint**                 | **Method** | **Summary**                                        |
| ---------------------------- | ---------- | -------------------------------------------------- |
| **/v1/auth/register**        | `POST`     | Register a new account with username and password. |
| **/v1/auth/login**           | `POST`     | Log in with username and password.                 |
| **/v1/auth/check**           | `POST`     | Validate the session token.                        |
| **/v1/recipes**              | `GET`      | Search for recipes by query (e.g., `?q=pasta`).    |
| **/v1/recipes**              | `POST`     | Add a new recipe. Example request body below.      |
| **/v1/recipes/:id**          | `PUT`      | Update an existing recipe by ID.                   |
| **/v1/data/title**           | `GET`      | Retrieve a single genius pasta pun.                |
| **/v1/data/titles**          | `GET`      | Retrieve all genius pasta puns.                    |
| **/v1/data/admin/recipes**   | `GET`      | Retrieve all recipes (admin-level access).         |
| **/v1/data/internal/uptime** | `GET`      | Check server uptime (locally accessible only).     |

---

### **Example Request for Creating a Recipe**

**Endpoint**: `POST /v1/recipes`  
**Request Body**:

```json
{
  "sessionId": "sessionId",
  "name": "Pasta Carbonara",
  "original": "undefined",
  "ingredients": {
    "Pasta": "Spaghetti",
    "Bacon": "200g",
    "Egg": "2",
    "Parmesan Cheese": "50g",
    "Black Pepper": "To taste",
    "Salt": "To taste"
  },
  "method": {
    "Step 1": "Cook spaghetti in salted boiling water until al dente.",
    "Step 2": "Fry bacon until crispy and remove from heat.",
    "Step 3": "In a bowl, whisk together eggs, grated Parmesan cheese, and black pepper.",
    "Step 4": "Drain pasta and reserve 1/2 cup of pasta water.",
    "Step 5": "Add cooked spaghetti to the bacon and toss until coated with bacon fat.",
    "Step 6": "Add reserved pasta water to the egg mixture and whisk to combine.",
    "Step 7": "Pour the egg mixture over the spaghetti and bacon and toss until coated.",
    "Step 8": "Serve immediately, garnished with additional grated Parmesan cheese and black pepper."
  },
  "rating": 4,
  "isPrivate": true
}
```

---

### **Example Request for Updating a Recipe**

**Endpoint**: `PUT /v1/recipes/:id`  
**Request Body**:

```json
{
  "sessionId": "sessionId",
  "name": "Pasta Carbonara",
  "original": "link-to-original-recipe",
  "ingredients": {
    "Pasta": "Spaghetti",
    "Bacon": "200g",
    "Egg": "2",
    "Parmesan Cheese": "50g",
    "Black Pepper": "To taste",
    "Salt": "To taste"
  },
  "method": {
    "Step 1": "Cook spaghetti in salted boiling water until al dente.",
    "Step 2": "Fry bacon until crispy and remove from heat.",
    "Step 3": "In a bowl, whisk together eggs, grated Parmesan cheese, and black pepper.",
    "Step 4": "Drain pasta and reserve 1/2 cup of pasta water.",
    "Step 5": "Add cooked spaghetti to the bacon and toss until coated with bacon fat.",
    "Step 6": "Add reserved pasta water to the egg mixture and whisk to combine.",
    "Step 7": "Pour the egg mixture over the spaghetti and bacon and toss until coated.",
    "Step 8": "Serve immediately, garnished with additional grated Parmesan cheese and black pepper."
  },
  "rating": 4,
  "isPrivate": true
}
```

---

### **Sample Request**

**Endpoint**: `POST /v1/auth/register`  
**Headers**:

```plaintext
Content-Type: application/json
```

**Request Body**:

```json
{
  "username": "newuser",
  "password": "newuser"
}
```

---

