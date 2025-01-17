
This Capstone is fairly simple.

It is a coffee application similar to the other labs

at localhost/index.php

We can login and register a user

We register two users

test:test
and
test2:test2

We login with test:test and then we grab the cookie from the session either using the cookie editor browser login

from here we just go to jwt.io, we change the user from test to test2 and then we we turn on interceptor in burp suite

we capture the request when we place an order of coffee (we click on order coffee button and click on the button Place order)

and from there we intercept the request, then we change the cookie from test into the modified cookie that we modified in jwt.io that has the test2 username

then we change the username in the intercepted request from test to test 2 so it matches the cookie 

[the instructor removes the algorithm and puts it to none in the jwt.io site and he says this is just to test if it will accept it]

then we pass the request through and we placed an order on behalf of test2 user even though we are logged in as test user. This is Broken Level authorization

Next up he uses ffuf tool

he does

ffuf -u http://localhost/v1/FUZZ -w /usr/share/wordlists/dirb/big.txt -e .php

He does -e .php because he knows that most of the request end points have the .php 

and then in the results we are given back 

admin
coffee.php
track.php

we visit localhost/v1/admin.php and we get a forbidden page

we ffuf again 

ffuf -u http://localhost/v1/admin/FUZZ -w /usr/share/wordlists/dirb/big.txt -e .php

and we get orders.php in the results so we visit

http://localhost/v1/admin/orders.php

and the endpoint returns all the orders from the web application which is a weak endpoint

the object has 

{
id: "4"
order_id:"6421736cc7070"
email:"test2"
coffee_id:"0"
quantity:"2"
order_status:"pending"
}

We take another users order ID go back to test and we are able to track orders, and we confirm that we are able to track another users order even though we are logged in as another user that isn't the one that is the order being tracked


