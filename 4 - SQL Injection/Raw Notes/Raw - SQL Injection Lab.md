For this lab the instructor shows us a simple way to do sql injection

We load up the lab he has for us and we see that there is a SQL injection demo

It asks us to put in a roast level (1-5) of the roast level of coffee we want, and we click get coffee

We get back a modal pop up that shows us 
ID: 5
Origin: Guatamela
Roast Level: 3

we head over to burp suite and see where this modal is being reflected in our http history

we see the GET Request with URL is 

GET /v1/001.php?roast=5 

this is where we want to start SQL attacking

so the instructor walks us through multiple steps, the first one is just using a ' to see if we get an error and we do, a lot of single quieries will use single quotes, and if we send the request with the single quote we get an error "uncaughtmysqli_sql_exception:..."

from here, we are able to see that the sql database is mysql and this is important to know

the instructor then goes on to show us an example of Union Select attack and he says he knows there is a username and password column so its just a matter of selecting it and guessing the number of columns needed but because we know from the base request what gets returned is 

{
ID:1
name:ethiopian Yirgacheffe
origin: ethiopia
roast_level:5
}

we see a total of 4 columns so we can craft our payload according to this 

so 5 UNION SELECT username, password, null, null from users

and we url encode this in repeater, and then we send it we can see in the response
that we get the name admin and their password and jeremy and his password as well 

ID:admin
name:iLikePasta

ID:jeremy
name:jeremymyspassword

The third way is with ffuf

and he does

ffuf -u `http://localhost/v1/001.php?roast-FUZZ` -w /usr/share/seclists/Fuzzing/SQLi/Generic-Sqli.txt

we get a lot of status codes and size we are getting some SQL injection but we can analyze this and the ones we believe are worth looking at we can try those payloads in our GET request in repeater and see how the web application handles it and gives back in the response

The fourth way is using sqlmap -r req.txt --ignore-code 401 and we walk through the sqlmap outputs and it will tell us at the end that our parameter roast (GET) is in fact injectable 

we see the payloads that were successful and tells us that the dmbs is MySql then we can dump the tables and database.