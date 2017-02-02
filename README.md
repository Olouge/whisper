# Documentation for WHISPER BULK SMS API
===============================================================

- To be able to use this API, it is recommended you create an account at whisper.ngtltd.com
 
## To send an sms to a set of contacts, do a POST call
===================================================
```
whisper.ngtltd.com/index.php?r=site/apiwebsendmsg
```

## Fields required in the post array are;
=====================================
```
phoneNumber = Online account Login. Attach country code to it.

Password = Online account password

msg = Message to be sent
```

## There rest of the POST array should hold the contacts as;
=========================================================
```
country code=contact number
```
For example assuming I have to send SMS to (237)650218839 and (237)670879560, also assuming my online account phone number is 1111111111 with country code 237 and password is 1234 and message is "Hello World". Then using any approach to do the POST call (CURL, AJAX, etc) my URL for the call should be formed to look like this,

```
whisper.ngtltd.com/index.php?r=site/apiwebsendmsg&phoneNumber=2371111111111&Password=1234&msg=Hello World&237=650218839&237=670879560

And POST array will be received by WHISPER API like this,

POST['phoneNumber'] = 2371111111111
POST['Password'] = 1234
POST['msg'] =  Hello World
POST['237'] = 650218839
POST['237'] = 670879560
```

The rest will be handled by the API.

## Response After Call;
=======================================================

JSON ecoded 2 dimensional array containing history of how message was handled for each contact. The first dimension holds an array of status for each contact. The following information can be gotten out of the array:

For the above example call, something like this for the response $rep can be gotten like this,

### For contact 650218839 for example
---------------------------------
```
record = 237
returnstatus = 0 means did not go well and 1 means went well
650218839 = "Failure" or "Success"
reason = A string of message telling why it went or did not went well
Tech = Type of Route the SMS took as we support multiple routes for reliability
replystatus = 100 means not enough airtime, 200 means login or password incorrect, 300 means internal server error, 400 means slow bandwidth or connection and 500 means unknown error occured.
sents = A count of total sent
sentsaved =  A count of those that were sent and the contacts saved under your online account
sentdiscounted = A count of those that were sent and discounted from your account
success = True meaning it was successful for this current contact or False meaning it failed
```

### Conditional status codes
----------------------------------
```
unknownstatus = this is set when an error 500 above is set
success = set when message went or never went well
creditfinish = set only when airtime is finish
Error = set when contact list is empty
ise = set when API throws an error
``` 

