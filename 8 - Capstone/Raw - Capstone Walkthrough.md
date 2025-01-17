Capstone Project Walkthrough
---

The instructor begins to add in each endpoint to postman, and after that he starts by registering a user (all using postman by the way)

When we register a user the response we get is

{
"message":"Registration successful",
"SessionId":"dGvzdGJkRW1DOQ== "
}

The instructor grabs the token and we can see its base 64 and we go to decoder on burp suite and we decode the token and we see that its just the word test + some random characters

Instructor says its always good to check the tokens via sequencer to see if they are vulnerable in anyway

From here we see that it is somewhat weak but not worth to continue taking a look at since it is mostly random 

he continues down the end points adding them to post man and he lands in creating a reciept

which is a post request to /v1/recipes

However we come across the PUT request to  /v1/recipes/:id

and here we get an interesting test data object key:value

{
  "sessionId": "sessionId",
  "name": "Pasta Carbonara",
  "original": "link-to-original-recipe",
  "ingredients": {
    "Pasta": "Spaghetti",

as you can see it says "original":"link-to-original-recipe"

the instructor says that perhaps here it accepts an URL and that it might be SSRF and he makes note of it

We keep moving down the list and the instrutor lands on the following endpoints

/v1/data/admin/recipes we cannot access this because we aren't admin

and 

/v1/data/internal/:action - this is in the documentation
but in the test data we find /v1/data/internal/uptime

the instrcutor goes on to say we might be able to find different actions if we ffuf it

he uses the dirb/big.txt file

we get two results back which is uptime and users

so now we know that we can possibly use this for further exploitation

he goes back to the PUT request to  /v1/recipes/:id

he confirms SSRF by putting google.com, and from there we send the request and we do get back a google.com html code back so we confirm ssrf

and now we enter the following in the original key and give it a value of 

localhost:3000/v1/data/internal/update

we send the put request again and we get an uptime, so now we give it the /users end point that we found when enumerating

localhost:3000/v1/data/internal/users

{
  "sessionId": "sessionId",
  "name": "Pasta Carbonara",
  "original": "localhost:3000/v1/data/internal/users",
  "ingredients": {
    "Pasta": "Spaghetti",

{
  "sessionId": "sessionId",
  "name": "Pasta Carbonara",
  "original": "localhost:3000/v1/data/internal/update",
  "ingredients": {
    "Pasta": "Spaghetti",



sending the following json data will return both the users and uptime

so now that we confirm the returns users and return SSRF

we take a closer look at the return user data in json object and we get the following

origina:[
"userlevel":admin
"session":admin
{
userlevel:user
session:thrudti43qrf
password:er454jtifgov
}
{
userlevel:user
session:rjerejur
password:fkjsdhi45q5
}
]

okay so what can we conclude from this users json object is that we go back to our test data on how to create a user we see this

```json
{
  "username": "newuser",
  "password": "newuser"
}
```


So what does this mean? is that we can add the userlevel:admin to a new user and see if it will accept it and make a new user admin!

we test it out by using postman again and sending a POST request to http:localhost:3000/v1/auth/register

from here we send

```json
{
  "username": "newuser",
  "password": "newuser",
  "userlevel":"admin"
}
```

we get a registration successul message with a sessionID which we will use 

we go to /v1/data/admin/recipes

in the body we send a GET request with the body haveing "sessionId":(the session id we got from the last post request)

and we get back all the of the receipes in the response confirming we are admin, and we successfully completed the captsone project




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

