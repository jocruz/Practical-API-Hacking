For this lab there is a login form we can use to login, our credentials are admin:admin

The instructor says we can test for sql injection by using a single quote, and from here we can attempt to see how the web app response to that

We log in, and we find the POST request with url /v1/002.php and the body contains
{
"username":"admin"
"password":"admin"
}

from here we just replace admin with a single quote and it causes the application to return a SQL error similar to the previous lab, we have confirmed it is SQL injection.

Our goal is to login through sql injection so the instructor says we have to think of the payload in this manner:

```sql
SELECT * FROM users WHERE username='$username' AND password='a' or 1=1''
```

if we think of it in this way, we can try to login because we are giving it a wrong password, however we are saying or, meaning one of the condition has to be true

so as long as the username is correct, the AND portion has to be true for it to login as admin, we confirm our admin username is correct, so the part is true, so the next statement has to be true but that statement is password = a and then our pay load is OR 1=1

so 1=1 is true but password is a is not true so that means the statement is true.

So a or 1=1' is our payload

so the quotes are confusing but lets look at it like this

password = ' |a' or 1=1'|   '

You see how our payload is in the middle but the outside quote completes the necessary payload for the query for it not to return a syntax error?

we send this in repeater and we get a successful login message in the response

if we enter the payload in the sign in form then we get a successful modal pop up that we logged in 