The instructor goes on to login via admin, he mentions that its the same as the last lab which we figured out via the command

```bash
ffuf -request req.txt -request-proto http -mode cluster-mode -w /usr/share/seclists/Passwords/xato-net-10-million-passwords-10000.txt -mc 200
```

From here the password ended up being ramirez so the login is

admin:ramirez

Once we log in we just retrieve the cookie that we get when we log in we can get this via multiple ways

1. Opening up the browser console and writing document.cookie
2. Going to burp suite and finding the POST request with the url /v1/verify and seeing the response on the body

For this lab we are going to send the POST request with the url /v1/verify to sequencer. With sequencer we are going test the cookie to see if its predictable.

before we start the live capture this is what we are going to do, we are going to configure the token location within the response

We are going to click on configure, and we are going to highlight the token, and it will auto fill the necessary fields. 

Once we start the live request, it will start testing the cookie, and once its finished we are going to "Analyze now" and from there we are able to see the significance levels and characters 0-22 are predictable indicated by the red bars, and 23 is green meaning it is random.

The instructor says that this is misleading because we didn't check an option on, we head to Analysis Options, and from there we check off Base64-decode before analyzing and once we turn that option on, we click analyze now and now we see that 0-14 are random, and 15-17 are unique this means we can start brute forcing the last three characters instead of only one to give us a more precise brute force of the token.

However the instructor goes on to say that the token looks like base 64 so we can start using decoder on burp or we can use a command on our terminal to decode it 

```bash
echo -n 'YWRtaW4MTA6MzY6MTctY3N2' | base64 -d
```

the output is `admin-10:36:17-csv`

We can also view this in decoder as well and decode it as Base64

The instructor then goes on to save the tokens which is an option in sequencer and then we find the file wherever we saved it and we use head -n 100 tokens.txt

and we investigate the tokens, from here we are able to see that the beginning characters are all the same, which is what sequencer picked up in the analysis!

If we take the tokens from the file, go to decoder, decode them in base64, we see all of the tokens that were decoded were changed into admin-10:38:15-wyr
but it gets broken into admin-timeoflogin-random characters

so the instructor says that we can just brute force the last three characters. 


