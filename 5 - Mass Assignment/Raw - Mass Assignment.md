
The instructor tells us we can do Mass assignment attacks but investigating and ffufing endpoints and see how the application handles the data

starting at localhost:8888/shop

we see the web application loads 2 images a car seat and a wheel, we have a 10 dollar car seat and a 10 dollar wheel and we can click the buy button here

if we go to burp suite, we see that burp suite collected a GET request with url /workshop/api/shop/products

we take this to ffuf

we do

ffuf -u http://localhost:8888/workshop/api/shop/products -w /usr/share/seclists/Fuzzing/http-request-method.txt -X FUZZ -fc 405

we get 200 status codes here

we get OPTIONS
HEAD GET POST

we go back to repeater, and we now know we can use POST in repeater in burp with http://localhost:8888/workshop/api/shop/products 

so we send the post request to the web application and we dont get anything, however the response tells us we are missing fields and this is very useful

we get

{
name:["This field is required"],
"price":["This field is required"],
"image_url":["This field is required"]
}

so this now tells us how to fill out our POST request, from there we can use this to our advantage

We are going to now modify the json object to send it in our web application

{
name:"cheesecake",
"price":"-500",
"image_url":"url taken from web app"
}

We send this post request and now the data is being shown in our web application so we succesfully did Mass Assignment
