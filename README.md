# Documentation for SMSECHOS BULK SMS API

- To be able to use this API, it is recommended you create an account at [https://api.smsechos.com](https://api.smsechos.com) but if you have an account on [https://api.smsechos.com](https://api.smsechos.com) or [https://smsechos.com](https://smsechos.com) then you can use it for your API calls.

## Special Character Support
| Tables   |      Are      |  Cool |
|----------|:-------------:|------:|
| col 1 is |  left-aligned | $1600 |
| col 2 is |    centered   |   $12 |
| col 3 is | right-aligned |    $1 |

| Special Character | Meaning | Auto Encoded by smsechos.com |
|-------------------|:-------:|-----------------------------:|
| \r                | Carraige Return| Yes |
| \n                | Newline | Yes  |   

<td>#</td>
                            <td>Hash or Number sign</td>
                            <td>Yes</td>
                        </tr>
                        <tr>
                            <td>*</td>
                            <td>Asterisk</td>
                            <td>Yes</td>
                        </tr>
                        <tr>
                            <td>@</td>
                            <td>At sign</td>
                            <td>Yes</td>
                        </tr>
                        <tr>
                            <td>%</td>
                            <td>Percent sign</td>
                            <td>Yes</td>
                        </tr>
                        <tr>
                            <td>"</td>
                            <td>Quote marks</td>
                            <td>Yes</td>
                        </tr>
                        <tr>
                            <td>'</td>
                            <td>Apostrophe</td>
                            <td>Yes</td>
                        </tr>
                        <tr>
                            <td>$</td>
                            <td>Dollar sign</td>
                            <td>Yes</td>
                        </tr>
                        <tr>
                            <td>?</td>
                            <td>Question mark</td>
                            <td>Yes</td>
                        </tr>
                        <tr>
                            <td>!</td>
                            <td>Exclamation mark</td>
                            <td>Yes</td>
                        </tr>
                        <tr>
                            <td>=</td>
                            <td>Equals sign</td>
                            <td>Yes</td>
                        </tr>
                    </table>
                    <div class="alert alert-warning">If a symbol is not listed above, then you have to manually insert its code into your <br>
                        SMS following the GSM 3.38 coding standard here <a href="https://en.wikipedia.org/wiki/GSM_03.38" target="_blank">https://en.wikipedia.org/wiki/GSM_03.38</a> or any other source. Remember codes are preceded with a % for example # symbol is %23 and @ symbol is %00 <br> If you have doubts, write customer support.</div>


[Skip to Sample code section](#sample-code)

## To send an sms to a set of contacts, do a POST call to

```
https://api.smsechos.com/index.php?r=site/apiwebsendmsg
```

## Fields required in the post array are

```
phoneNumber = Online account Login. Attach country code to it.

Password = Online account password

msg = Message to be sent
```
## Fields optional in the post array are;
```
$sender_id = This is optional field, If you wish to override default sender id, then set this field
```
## There rest of the POST array should hold the contacts as;
### When sending to multiple contacts
```
SerialNumber-country code=contact number
```
### When sending to one contact
```
SerialNumber-country code=contact number
```
OR
```
country code=contact number
```
Will work fine. Note the serial number in this case are sequential integers you will generate at your end.

For example assuming I have to send SMS to (237)650218839 and (237)670879560, also assuming my online account phone number is 1111111111 with country code 237 and password is 1234 and message is "Hello World". Then using any approach to do the POST call (CURL, AJAX, etc) my URL for the call should be formed to look like this,

```
https://api.smsechos.com/index.php?r=site/apiwebsendmsg&phoneNumber=2371111111111&Password=1234&msg=Hello World&1-237=650218839&2-237=670879560
```
Notice each country code is preceded by a sequential number, 1, 2, etc. Without this sequential number separated using - from the country code might lead to unreliable results. But if sending only to a single contact, only country code will suffice but if you set it up as 1-country code=number for the single number it will work fine.

And POST array will be received by our API like this,
```
POST['phoneNumber'] = 2371111111111
POST['Password'] = 1234
POST['msg'] =  Hello World
POST['1-237'] = 650218839
POST['2-237'] = 670879560
```

The rest will be handled by the API.

## Response After Call;


JSON encoded 2 dimensional array containing history of how message was handled for each contact. The first dimension holds an array of status for each contact. The following information can be gotten out of the array:

For the above example call, something like this for the response $rep can be gotten like this,

### For contact 650218839 for example

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
```
unknownstatus = this is set when an error 500 above is set
success = set when message went or never went well
creditfinish = set only when airtime is finish
Error = set when contact list is empty
ise = set when API throws an error
``` 

### Response vars

- genreplystatus

values = <br>
0 //Success, No problems<br>
500 //Unknown error<br>
600 //credit finished<br>
700 //Contacts List supplied is empty<br>
800 //Internal server error <br>
900 //Wrong login<br>

- minireplystatus

values = <br>
-11 //Not enough airtime <br>
-21 //No sender ID supplied <br>

- balance

value = current balance airtime


- generalr

values = String with a message associated with the transaction in general



### Contacts Based status

Any other array element in the response is the status of each contacts sending transaction, for each contact, the returned array for that contact has the following fields

- record

values = Serial number of the transaction queue

- contact

value = associated contact number for this transaction

- returncode

In this section, any code different from one should be reported to the whisper team under help of the platform. A whisper expert will attend to you.

values = 
1 //message was sent successfully
100 //The airtime got finished somehow when processing request for this contact
404 //Wrong platform credentials
0 //General network problem
-500 //Slow or internet connection bandwidth too low
Any other code means unknow error occurred
{: id="sample-code"}
# Sample API Function call 
{#sample-code}

```
public function methodCall() {
     //sendsms code
        //CURL
        $url = 'https://api.smsechos.com/index.php?r=site/apiwebsendmsg&';
        $username = '+237670000000';
        $password = md5('test');
        $sender_id = "ABC Ltd"; // This is optional field // If you wish to override default sender id, then set this field
        $verification_code = rand(1000,9999);
        $receiver = "670879560";//696449761
        //$timeout=10;
        $request = $url . "phoneNumber=" . urlencode($username) . "&Password=" . $password;
        $request.="&msg=" . urlencode("Hello World: " . $verification_code) . "&237=" . urlencode($receiver). "&sender_id=" . urlencode($sender_id);

        $url = $request;
        $ch = curl_init();
        curl_setopt($ch, CURLOPT_URL, $url);
        curl_setopt($ch, CURLOPT_RETURNTRANSFER, 1);
        //curl_setopt($ch, CURLOPT_POST, 1);

        $response = curl_exec($ch);
        curl_close($ch);

        $response = json_decode($response);
        foreach($response as $k){
            if(isset($k->success))            
                echo $k->success;
            if(isset($k->genreplystatus))
                echo $k->genreplystatus;
            if(isset($k->minireplystatus))
                echo $k->minireplystatus;
            if(isset($k->balance))
                echo $k->balance;
            if(isset($k->generalreason))
                echo $k->generalreason;
            if(is_object($k) && isset($k->contact))
                var_dump($k);
        }

        //additionally, you can do $k->contactnumber
        exit;
        //return $response; 
    }
```
