Social Invite API 2.0
============

REST API for Social Invite


# How It Works
The Social Invite API will allow mobile app to invite friends via SMS. 
Simply call our API with a personalized message and recipient phone number and let Social Invitation API take care of the rest. 
Our service will apply host of filters, rules and scheduling to achieve better consumer experience and better campaign results.  
Some of our features are highlighed as follow:

1. Remove landline numbers.
2. Remove known feature phone numbers.
3. Rotate from a pool of numbers to send the list. Dedicated numbers supported.  Contact Hook Mobile for more details.
4. Manage sleep time to ensure invitation are sent during day time.
5. Manage opt out for each application.
6. Prevent duplicate invitations to recipient from same app in past 7 days .

# Click-Through Feedback Loop
Hook Mobile constantly strives to improve the of Social Invite Service.  We ask our customer to provide click-through 
data in real-time. This continous loopback of data will help improve the routing, accuracy and overall 
effectiveness of the Social Invite API Service.  We will aggregate the click-through data into our analytic dashboard.

# How To Get Started
Please contact Hook Mobile to request for new Social Invite API account.  We will need following information for provisioning:

- Company name
- Email
- Mobile app identifier (Android Market App ID or iTunes app ID)
- Callback URL for receiving invitation message status (Optional)

We will then assign you set of credential for accessing our API as well as login credential for analytic dashboard.

The next step is to begin integrating against our simple REST-based API.  You will need to build both REST client and server 
logic integrate with your service:

- Send Invitation : You are the REST client, Social Invite is the REST server.
- Receive Invitation Delivery Status : You are the REST server, Social Invite is the REST client.
- Report Click-Through : You are the REST client, Social Invite is the REST server.

# Authentication
Social Invite API use Basic authentication to authenticate API requests.  Once you are provisioned with an account, 
you will be assigned an <code>apiKey</code> and a <code>secret</code>.  You will be using both the <code>apiKey</code> and the <code>secret</code> as Basic authentication username and password.

# Send Invitations
The REST method for sending new invitations are accessible via following service endpoint:

Production Server: 
<pre><code>https://si.hookmobile.com/ws/invitation</code></pre>

<H3>HTTP Method</H3>
POST

<H3>Content Type</H3>
application/x-www-form-urlencoded

<H3>Form Variables</H3>
Here are the valid variables defined for this operation.  Be sure to apply appropriate URL encoding to each form variable value.

- <code>phone</code> : (required) recipient phone number.
- <code>osType</code> : (required) Targeting criteria based on mobile OS. Possible values are: <code>IOS</code>,<code>ANDROID</code>,<code>IOS_ANDROID</code>
- <code>message</code> : (required) Invitation message.  
- <code>appId</code> : (required) Android Market Application Id or iTune App Store App Id.  This identifier must be privisioned into our sytem prior to using the API.
- <code>isoCountryCode</code> : (optional, default to "US") Inviter ISO country code.  
- <code>senderPhone</code> : (optional) Inviter phone number.
- <code>referenceId</code> : (optional) Unique id assigned by customer to each message.

The ISO country code is used for normalizing phone numbers to international E164 format.  The ISO country code of mobile device is accessible via IOS and Android SDK.

<H3>Response Status</H3>
Upon invoking this method, client shall receive HTTP Status Code 200, indicating server has accepted and queued the request. If Client receive HTTP Status Code 4XX, it means server did not accept the request.

<H3>Response Body</H3>
The HTTP response body shall contain data in JSON format. For successful or Status Code 200 scenario, the JSON data contains following name value pairs:

- <code>messageId</code> : Tracking identifier assigned to the submitted request.  You may use this tracking identifier to query for delivery status.

In event of unsuccessful or Status 4XX Code scenario, the JSON data contains following name value pairs:

- <code>code</code> : Error code
- <code>message</code> : Detailed error description

<H3>Examples of valid API requests using cURL utility:</H3>
In this example, the customer has been assigned <code>apiKey</code> = "myApiKey" and <code>secret</code> = "mySecret".  

<pre><code>curl --user myApikey:mySecret --data "message=Download+this+awsome+game+http://goo.gl/XYZ&amp;phone=3012223333&amp;osType=ANDROID&amp;appId=com.mediocre.sprinklefree&amp;referenceId=8s3k3g" "https://si.hookmobile.com/ws/invitation"</code></pre>

<H3>Receive Invitation Delivery Status</H3>
Optionally, Social Invite Service can issue callback to your server to notify status update of previously submitted request.
The callback request will be an HTTP GET request to your designated URL.  Your URL may contain following variables, which will be
replaced with actual value during callback:

-  <code>{messageId}</code> : id from Social Invite request submission.
-  <code>{referenceId}</code> : reference id supplied by customer from Social Invite request submission.  This value may be empty if original request was not submitted with <code>referenceId</code> parameter.
-  <code>{phone}</code> : phone number
-  <code>{status}</code> : final status code. 1=delivered, 100+=failure
-  <code>{completionDate}</code> : Completion date in UNIX millisecond

# Query Invitation Status
The REST method for querying status of previously sent invitation is accessible via following service endpoint:

Production Server: 
<pre><code>https://si.hookmobile.com/ws/invitation/{messageId}</code></pre>

<H3>HTTP Method</H3>
GET

<H3>Path Parameter</H3>
To query status of a particular invitation request, you must append the tracking id or <code>{messageId}</code> of the invitation request.

<H3>Response Status</H3>
Upon invoking this method, client shall receive HTTP Status Code 200, indicating server has accepted and queued the request. If Client receive HTTP Status Code 4XX, it means server did not accept the request.

<H3>Response Body</H3>
The HTTP response body shall contain data in JSON format. For successful or Status Code 200 scenario, the JSON data contains following name value pairs:

- <code>processedDate</code> : Date request was processed. Format is UNIX Epoch Time.
- <code>status</code> : Status of request. Possible values: <code>pending</code>, <code>sent</code>, <code>discarded</code>. 

In event of unsuccessful or Status 4XX Code scenario, the JSON data contains following name value pairs:

- <code>code</code> : Error code
- <code>message</code> : Detailed error description

<H3>Examples of valid API requests using cURL utility:</H3>
In this example, the customer has been assigned <code>apiKey</code> = "myApiKey" and <code>secret</code> = "mySecret".  

<pre><code>curl --user myApikey:mySecret "https://si.hookmobile.com/ws/invitation2/3"</code></pre>

# Repoorting Back Invitation Click-Through
The REST method for customer to notify Hook Mobile of invitation click-through. This method accessible via following service endpoint:

Production Server: 
<pre><code>https://si.hookmobile.com/ws/click</code></pre>

<H3>HTTP Method</H3>
POST

<H3>Content Type</H3>
application/x-www-form-urlencoded

<H3>Form Variables</H3>
Here are the valid variables defined for this operation.  Be sure to apply appropriate URL encoding to each form variable value.

- <code>messageId</code> : id from Social Invite request submission.
- <code>referenceId</code> : reference id supplied by customer from Social Invite request submission.  This value may be empty if original request was not submitted with <code>referenceId</code> parameter.
- <code>userAgent</code> : Mobile device browser user-agent captured from click-through.

NOTE: You must provide either <code>messageId</code> or <code>referenceId</code> in the callback.

<H3>Response Status</H3>
Upon invoking this method, client shall receive HTTP Status Code 200, indicating server has accepted your notification. 
If Client receive HTTP Status Code 4XX, it means server did not accept the request.

In event of unsuccessful or Status 4XX Code scenario, the JSON data contains following name value pairs:

- <code>code</code> : Error code
- <code>message</code> : Detailed error description

<H3>Examples of valid notification requests using cURL utility:</H3>
In this example, the customer has been assigned <code>apiKey</code> = "myApiKey" and <code>secret</code> = "mySecret".  

<pre><code>curl --user myApikey:mySecret --data "messageId=234234&amp;userAgent=Mozilla%2F5.0%20(iPhone%3B%20CPU%20iPhone%20OS%206_0_2%20like%20Mac%20OS%20X)%20AppleWebKit%2F536.26%20(KHTML%2C%20like%20Gecko)%20Version%2F6.0%20Mobile%2F10A551%20Safari%2F8536.25" "https://si.hookmobile.com/ws/invitation"</code></pre>

# Status Codes

- <code>0</code> : Invitation request accepted 
- <code>1</code> : Invitation delivered
- <code>2</code> : Invitation clicked

- <code>100</code> : Permission denied 
- <code>101</code> : Invalid parameter(s) in your API call
- <code>102</code> : Missing parameter(s) in your API call
- <code>103</code> : Resource not found

- <code>123</code> : Invalid phone
- <code>124</code> : Send failed
- <code>125</code> : Not a mobile phone
- <code>126</code> : Mobile device not compatible
- <code>127</code> : User opted-out
- <code>128</code> : Duplicate invitation

