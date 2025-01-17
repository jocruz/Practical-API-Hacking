
For this lab the instructor goes to localhost:8888/shop

Here we have the option to buy items, but we have the option to enter a coupon code.

We enter an invalid coupon code to generate traffic for burp suite to capture

from there we go to burp suite

we go to proxy - http history

we go to POST request with URL /community/api/v2/coupon/validate-coupon

We see the body had 

{
"coupon_code":"asdasd"
}

from there we go to intruder

in there we are going to put a positional parker around asdasd

so we have to be the payload has to include the notes so the positional marker has to be around "asdasd" so the payload isnt interpreted as a string

so he loads in a burp suite list that is called Fuzzing - SQL injection and loads it in to see what intruder returns when we run the attack

we are looking for status codes and length

we find a status code of 422 and we are getting interesting results back like

"error":"invalid character 'z' in literal true (expecting'r')"

from there we know we might be able to get sql injection but perhaps the wrong list is being used

he moves on to a noSQL list located at /usr/share/seclists/Fuzzing/Databases/NoSQL.txt

he loads the payload in the payload set, he makes sure the payload encoding is turned off for this nosql

then we find a working payload that is 

{"$gt":""}

and we get a status of 200 and looking at the response

it returns a successsful coupod code with the amound and created at fields

and we complete the lab


