# Lab: Basic SSRF against the local server

This lab has a stock check feature which fetches data from an internal system.

To solve the lab, change the stock check URL to access the admin interface at `http://localhost/admin` and delete the user `carlos`.

Portswigger solution:

1. Browse to `/admin` and observe that you can't directly access the admin page.
2. Visit a product, click "Check stock", intercept the request in Burp Suite, and send it to Burp Repeater.
3. Change the URL in the `stockApi` parameter to `http://localhost/admin`. This should display the administration interface.
4. Read the HTML to identify the URL to delete the target user, which is:
    
    `http://localhost/admin/delete?username=carlos`
5. Submit this URL in the `stockApi` parameter, to deliver the SSRF attack.

Instructor Solution:

The instructor visits a product, click "Check stock", intercept the request in Burp Suite, and send it to Burp Repeater.

From here we are able to see that burp suite captured a POST request with the URL being
/product/stock and if we investigate that the body has this 

stockAPI=http....

we send this to repeater and we now know we have to modify this stock api

we change the stockapi to the following

stockAPI=`http://localhost/admin?productId=1&storeId=1`

the instrcutor says this can still work even though the parameters are there

we send this to the web application, we review the response body, and we see we get some sort of admin panel 

we investigate further and find the following button Delete and we see it leads to another endpoint

we copy the endpoint which is /admin/delete?username=carlos

and we paste it in the request that we previously modified

so now its 

`http://localhost/admin/delete?username=carlos&storeId=1`


we hit send on burp /repeater

and we see we get a Follow Redirection, we click that the request shows 302 found

we follow the redirection and we get an unauthorized page in the body following the redirection but this is because the request is

GET /admin

and we arent admin but we managed to delete carlos proving SSRF 

and we solve the lab

