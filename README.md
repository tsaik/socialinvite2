Social Invite API
============

REST API for Social Invite


# How It Works
The Social Invite API will allow customer to submit a list of phone numbers, with variable to attach sender name, content of SMS and customizable URL; and our system will handle sending the messages based on following criteria:

1. Remove landline numbers
2. Remove known feature phone numbers
3. Rotate from a pool of numbers to send the list
4. Manage sleep time and insert time delay if necessary
5. Manage opt out for each application
6. Remove duplicate invitation to recipients

# Click-Through and Conversion Feedback Loop
Hook Mobile constantly strives to improve the of Social Invite Service.  We ask our customer to provide click-through and conversion data either in real-time or in batch (e.g. csv). This continous loopback of data will help improve the accuracy and overall effectiveness of the Social Invite API Service.
If data is provided in CSV format, each row maps to a unique click-through or conversion event.  Following is the recommended format:

<pre>phone,event,user-agent
3012223333,click,Mozilla/5.0 (Linux; U; Android 4.2; xx-xx; GT-I9500 Build/JDQ39) AppleWebKit/534.30 (KHTML, like Gecko) Version/4.0 Mobile Safari/534.30
7033334444,conversion,Mozilla/5.0 (Linux; U; Android 4.0.4; pl-pl; Sony Xperia Neo V Build/IMM76D) AppleWebKit/534.30 (KHTML, like Gecko) Version/4.0 Mobile Safari/534.30
</pre>

Alternatively, the feedback can be accepted real-time via callback against Hook Mobile server.  

# Authentication
Social Invite API use Basic authentication to authenticate API requests.  Once you are provisioned with an account, you will be assigned an <code>apiKey</code> and a <code>secret</code>.  You will be using both the <code>apiKey</code> and the <code>secret</code> as Basic authentication username and password.

# Send Invitations
The REST method for sending new invitations are accessible via following service endpoint:

Production Server: 
<pre><code>https://si.hookmobile.com/ws/invitation2</code></pre>

<H3>HTTP Method</H3>
POST

<H3>Content Type</H3>
application/x-www-form-urlencoded

<H3>Form Variables</H3>
Here are the valid variables defined for this operation.  Be sure to apply appropriate URL encoding to each form variable value.

- <code>phone</code> : (required) recipient phone number.
- <code>osType</code> : (required) Targeting criteria based on mobile OS. Possible values are: <code>IOS</code>,<code>ANDROID</code>,<code>IOS_ANDROID</code>
- <code>message</code> : (required) Invitation message.  
- <code>isoCountryCode</code> : (optional, default to "US") Inviter ISO country code.  - <code>senderPhone</code> : (optional) Inviter phone number.
- <code>appId</code> : (required) Android Market Application Id or iTune App Store App Id.
- <code>messageId</code> : (optional) Unique message Id assigned by customer.  Used in the callback.

The ISO country code is used for normalizing phone numbers to international E164 format.  The ISO country code of mobile device is accessible via IOS and Android SDK.

<H3>Response Status</H3>
Upon invoking this method, client shall receive HTTP Status Code 200, indicating server has accepted and queued the request. If Client receive HTTP Status Code 4XX, it means server did not accept the request.

<H3>Response Body</H3>
The HTTP response body shall contain data in JSON format. For successful or Status Code 200 scenario, the JSON data contains following name value pairs:

- <code>id</code> : Tracking identifier assigned to the submitted request.  You may use this tracking identifier to query for delivery status.

In event of unsuccessful or Status 4XX Code scenario, the JSON data contains following name value pairs:

- <code>code</code> : Error code
- <code>message</code> : Detailed error description

<H3>Error Codes</H3>
TBD

<H3>Examples of valid API requests using cURL utility:</H3>
In this example, the customer has been assigned <code>apiKey</code> = "myApiKey" and <code>secret</code> = "mySecret".  

<pre><code>curl --user myApikey:mySecret --data "message=Download+this+awsome+game+http://goo.gl/XYZ&amp;phone=3012223333&amp;osType=ANDROID&amp;appId=com.mediocre.sprinklefree&amp;messageId=8s3k3g" "http://dev.wupima.com:7060/ws/invitation"</code></pre>

<H3>Callback</H3>
Optionally, Social Invite Service can issue callback to your server to notify status update of previously submitted request.
The callback request will be an HTTP GET request containing following query parameters;

-  <code>id</code> : id from Social Invite request submission.
-  <code>messageId</code> : message id supplied by customer from Social Invite request submission.  This value may be empty if original request was not submitted with <code>messageId</code> parameter.


# Query Invitation Status
The REST method for querying status of previously sent invitation is accessible via following service endpoint:

Beta Server: 
<pre><code>http://dev.wupima.com:7071/ws/invitation/{id}</code></pre>
Production Server: 
<pre><code>https://si.hookmobile.com/ws/invitation/{id}</code></pre>

<H3>HTTP Method</H3>
GET

<H3>Path Parameter</H3>
To query status of a particular invitation request, you must append the tracking id or <code>{id}</code> of the invitation request.

<H3>Response Status</H3>
Upon invoking this method, client shall receive HTTP Status Code 200, indicating server has accepted and queued the request. If Client receive HTTP Status Code 4XX, it means server did not accept the request.

<H3>Response Body</H3>
The HTTP response body shall contain data in JSON format. For successful or Status Code 200 scenario, the JSON data contains following name value pairs:

- <code>processedDate</code> : Date request was processed. Format is UNIX Epoch Time.
- <code>status</code> : Status of request. Possible values: <code>pending</code>, <code>sent</code>, <code>discarded</code>. 

In event of unsuccessful or Status 4XX Code scenario, the JSON data contains following name value pairs:

- <code>code</code> : Error code
- <code>message</code> : Detailed error description

<H3>Error Codes</H3>
TBD

<H3>Examples of valid API requests using cURL utility:</H3>
In this example, the customer has been assigned <code>apiKey</code> = "myApiKey" and <code>secret</code> = "mySecret".  

<pre><code>curl --user myApikey:mySecret "http://si.hookmobile.com/ws/invitation2/3"</code></pre>



