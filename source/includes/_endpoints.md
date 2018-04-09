# Endpoints - Account

Most of the endpoint require authentication of some type (user/API Key authentication). Where not required appropriate note will be provided.


## Check Account Existence

Create new account for user.

### HTTP Request
<code>GET https://[host]/api/v1/account/check-existence</code>


```shell
curl -X GET \
  'http://localhost:3000/api/v1/account/check-existence?email=test%40email.com&merchantId=00&apiKey=04af606c-38a3-11e8-b467-0ed5f89f718b' \
  -H 'cache-control: no-cache' \
  -H 'content-type: application/json' \
```

> The JSON encoded response on successful request

```shell
{
    "status": "success",
    "data": {
        "email": "test@email.com",
        "merchantId": "00",
        "message": "Account already exists."
    }
}
```

### Parameters

Argument | Type | Required
-------|-------|-----
email| String | yes
merchantId| String | yes 
apiKey| String | yes

## Create Account

Create new account for user.

### HTTP Request
<code>POST https://[host]/api/v1/account</code>


```shell
curl -X POST \
  http://localhost:3000/api/v1/account \
  -H 'cache-control: no-cache' \
  -H 'content-type: application/json' \
  -d '{
	"email":"test@email.com",
	"phone":"1234567890",
	"password":"123",
	"merchantName":"Xinfin",
	"merchantId":"00",
	"apiKey":"04af606c-38a3-11e8-b467-0ed5f89f718b"
}'
```

> The JSON encoded response on successful account creation

```shell
{
    "status": "success",
    "data": {
        "publicKey": "0xca9c3fc05f3dda885d77c0b4ae2e3a01cf92cec7",
        "message": "Account created successfully. Activation link sent on your email."
    }
}
```

### Parameters

Argument | Type | Required
-------|-------|-----
email| String | yes
phone| String | yes 
merchantName| String | yes 
merchantId| String | yes 
apiKey| String | yes

## Sign In

Sign into users account. <br>
2FA authentication is required for signin. 

Supported 2FA Auth methods : <br>

+ <code>Email</code>
+ <code>Google Authenticator (TOTP)</code>

If <code>otpNo</code> parameter is kept blank, email containing otp will be sent to user address given email based 2FA is enabled.<br>
Retry the request with <code>otpNo</code> parameter set as given otp.

On successful signin the jwt token will be issued.

### HTTP Request
<code>POST https://[host]/api/v1/signin</code>

```shell
curl -X POST \
  http://localhost:3000/api/v1/signin \
  -H 'cache-control: no-cache' \
  -H 'content-type: application/json' \
  -d '{
  "username" : "test@email.com",
  "password" : "123",
  "merchantId" : "00",
  "otpNo" : ""
  "apiKey" : "04af606c-38a3-11e8-b467-0ed5f89f718b"
}'
```

> The JSON encoded response when otp is blank 

```shell
{
    "status": "success",
    "data": {
        "twofaenabled": false,
        "message": "OTP sent Successfully on your Email."
    }
}
```
> The JSON encoded response when otp is set

```shell
{
    "status": "success",
    "data": {
        "message": "Successfully logged in.",
        "jwt": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJtZXJjaGFudElkIjoiMDAiLCJhcGlLZXkiOiIzYjQxNGVmMC0zMzMxLTExZTgtODNiYi04MWNhZjU0ZTU1YzMiLCJ2ZXJzaW9uIjoidjEuMCIsImlhdCI6MTUyMjkyNzA5MiwiZXhwIjoxNTIyOTI3NjkyfQ.4U-oYtIftbqrjGLIgfzmEBC_u4hVZaVcFu8znzcS2EI"
    }
}
```

### Parameters

Argument | Type | Required | Description
-------|-------|-----|-------
username| String | yes | user's email
phone| String | yes | 
merchantName| String | yes | 
otpNo| String | yes | keeping this field blank will result in api server sending otp email to the user. 
apiKey| String | yes


## Enable TOTP 2FA

Enable Google Authenticator based 2FA. <br>

### HTTP Request
<code>POST https://[host]/api/v1/account/enable2FA</code>

```shell
curl -X POST \
  http://localhost:3000/api/v1/account/enable2FA \
  -H 'cache-control: no-cache' \
  -H 'content-type: application/json' \
  -d '{
	"merchantId":"00",
	"username":"test@email.com",
	"apiKey":"04af606c-38a3-11e8-b467-0ed5f89f718b"
}'
```

> The JSON encoded response

```shell
{
  "status": "success",
  "data": {
    "account": "test@email.com",
    "message": "2FA authentication enabled.",
    "qrcode": "data:image\/png;base64,iVBORw0KGgoAAAANSUhEUgAAANQAAADUCAYAAADk.....redacted...",
    "secret": "OR5GKNZUJ4YFKWJXKJKVIS2QOJ3EK4SZ"
  }
}
```


### Parameters

Argument | Type | Required | Description
-------|-------|-----|-------
username| String | yes | user's email
merchantId| String | yes | 
apiKey| String | yes


## Disable TOTP 2FA

Disable Google Authenticator based 2FA. <br>

### HTTP Request
<code>POST `https://[host]/api/v1/account/disable2FA</code>

```shell
curl -X POST \
  http://localhost:3000/api/v1/account/disable2FA \
  -H 'cache-control: no-cache' \
  -H 'content-type: application/json' \
  -d '{
	"merchantId":"00",
	"username":"test@email.com",
	"apiKey":"04af606c-38a3-11e8-b467-0ed5f89f718b"
}'
```

> The JSON encoded response

```shell
{
    "status": "success",
    "data": {
        "message": "2FA authentication disabled."
    }
}
```

### Parameters

Argument | Type | Required | Description
-------|-------|-----|-------
username| String | yes | user's email
merchantId| String | yes | 
apiKey| String | yes


## Verify TOTP 2FA

Disable Google Authenticator based 2FA. <br>

### HTTP Request
<code>POST `https://[host]/api/v1/account/verify2FA</code>

```shell
curl -X POST \
  http://localhost:3000/api/v1/account/verify2FA \
  -H 'cache-control: no-cache' \
  -H 'content-type: application/json' \
  -d '{
	"username":"test@email.com",
	"merchantId":"00",
	"otp":"050395",
	"apiKey":"04af606c-38a3-11e8-b467-0ed5f89f718b"
}'
```

> The JSON encoded response

```shell
{
	"status": "success",
	"data": {
	"message": "Otp is verified",
}
```

### Parameters

Argument | Type | Required | Description
-------|-------|-----|-------
username| String | yes | user's email
merchantId| String | yes | 
otp| String | yes | 
apiKey| String | yes



## Change Password

Change user account password. <br>

### HTTP Request
<code>POST https://[host]/api/v1/account/password</code>

```shell
curl -X POST \
  http://localhost:3000/api/v1/account/password \
  -H 'cache-control: no-cache' \
  -H 'content-type: application/json' \
  -d '{
	"email" : "test@email.com",
	"newPassword" : "1234",
	"oldPassword" : "123",
	"merchantId" : "00",
	"apiKey" : "04af606c-38a3-11e8-b467-0ed5f89f718b"
}'
```

> The JSON encoded response

```shell
{
    "status": "success",
    "data": {
        "message": "Password changed successfully."
    }
}
```

### Parameters

Argument | Type | Required | Description
-------|-------|-----|-------
email| String | yes | user's email
oldPassword| String | yes | 
newPassword| String | yes | 
merchantId| String | yes | 
apiKey| String | yes


## Forgot Password

Initiate forgot password/reset password process. <br><br>
Calling this API will result in sending reset link to the user's registered email account.

### HTTP Request
<code>POST https://[host]/api/v1/account/forgot-password</code>

```shell
curl -X POST \
  http://localhost:3000/api/v1/account/forgot-password \
  -H 'cache-control: no-cache' \
  -H 'content-type: application/json' \
  -d '{
	"email" : "test@email.com",
	"merchantId" : "00",
	"apiKey" : "04af606c-38a3-11e8-b467-0ed5f89f718b"
}'
```

> The JSON encoded response

```shell
{
    "status": "success",
    "data": {
        "message": "Reset link has been sent to your registered email."
    }
}
```

### Parameters

Argument | Type | Required | Description
-------|-------|-----|-------
email| String | yes | user's email 
merchantId| String | yes | 
apiKey| String | yes




#Endpoints - Wallet

## Get Balance

Get user's balance in tokens.

### HTTP Request
<code>GET https://[host]/api/v1/wallet/balance</code>


```shell
curl -X GET \
  'http://localhost:3000/api/v1/wallet/balance?address=0x3a4c7825695f22b693fc7bd42f3e8d9bc1af8bb6&apiKey=04af606c-38a3-11e8-b467-0ed5f89f718b&merchantId=00' \
  -H 'cache-control: no-cache' \
  -H 'content-type: application/json' \
  -d '{
  "address": "0x3a4c7825695f22b693fc7bd42f3e8d9bc1af8bb6"
}'
```

> The JSON encoded response on successful request

```shell
{
    "status": "success",
    "data": {
        "address": "0x3a4c7825695f22b693fc7bd42f3e8d9bc1af8bb6",
        "res": "0",
        "message": "Your Account Balance is 0",
        "balance": "0"
    }
}
```

### Parameters

Argument | Type | Required | Description
-------|-------|----- |------
address| String | yes | Public address
merchantId| String | yes | 
apiKey| String | yes | 

## Verify XDC address

Verify XDC address.

### HTTP Request
<code>GET https://[host]/api/v1/wallet/balance</code>


```shell
curl -X GET \
  'http://localhost:3000/api/v1/wallet/verify-xdc-address?address=0xca9c3fc05f3dda885d77c0b4ae2e3a01cf92cec7&apiKey=04af606c-38a3-11e8-b467-0ed5f89f718b&merchantId=00&xdc=1001' \
  -H 'cache-control: no-cache' \
  -H 'content-type: application/json' \
  -d '{
  "address": "0x3a4c7825695f22b693fc7bd42f3e8d9bc1af8bb6"
}'
```

> The JSON encoded response on successful request

```shell
{
    "status": "success",
    "data": {
        "message": "Address is valid"
    }
}
```

### Parameters

Argument | Type | Required | Description
-------|-------|----- |------
address| String | yes | Public address
merchantId| String | yes | 
apiKey| String | yes | 


## Get QRCode

Get qrcode for user's public address.

### HTTP Request
<code>GET https://[host]/api/v1/wallet/qrcode</code>


```shell
curl -X GET \
  'http://localhost:3000/api/v1/wallet/qrcode?address=0x3a4c7825695f22b693fc7bd42f3e8d9bc1af8bb6&apiKey=04af606c-38a3-11e8-b467-0ed5f89f718b&merchantId=00' \
  -H 'cache-control: no-cache' \
  -H 'content-type: application/json' \
```

> The JSON encoded response on successful request

```shell
{
    "status": "success",
    "data": {
        "url": "data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAJQAAACUCAYAAAB1PADUAAAAAklEQVR4AewaftIAAAS6SURBVO3BQY4kRxIEQdNA/f/Lun30vQSQSK+eIWki+CNVS06qFp1ULTqpWnRSteikatFJ1aKTqkUnVYtOqhadVC06qVp0UrXopGrRSdWik6pFn7wE5De...redacted...."
    }
}
```

### Parameters

Argument | Type | Required | Description
-------|-------|----- |------
address| String | yes | Public address
merchantId| String | yes | 
apiKey| String | yes | 


## Get Transactions

Get user account transactions.

### HTTP Request
<code>POST https://[host]/api/v1/wallet/qrcode</code>


```shell
curl -X POST \
  http://localhost:3000/api/v1/wallet/transactions \
  -H 'cache-control: no-cache' \
  -H 'content-type: application/json' \
  -d '{
  "walletNo" : "",
  "address" : "0x3a4c7825695f22b693fc7bd42f3e8d9bc1af8bb6",
  "apiKey" : "04af606c-38a3-11e8-b467-0ed5f89f718b",
  "merchantId" : "00",
  "amount" : 0,
  "fromDate" : "",
  "toDate" : ""
}'
```

> The JSON encoded response on successful request

```shell
{
    "status": 'success',
    "data": []
}
```

### Parameters

Argument | Type | Required | Description
-------|-------|----- |------
walletNo| String | yes | 
address| String | yes | Public address
amount| String | yes | 
fromDate| String | yes | 
toDate| String | yes |
merchantId| String | yes | 
apiKey| String | yes | 


## Send XDC

Transfers XDC between accounts.<br>


Keep otp parameter blank during first request - this will result in API server sending email contaning the otp to the users email account.
Once you get the otp, resend the request again with otp parameter set.
### HTTP Request
<code>POST https://[host]/api/v1/wallet/xdc</code>


```shell
curl -X POST \
  http://localhost:3000/api/v1/wallet/xdc \
  -H 'cache-control: no-cache' \
  -H 'content-type: application/json' \
  -d '{
	"address" : "0xca9c3fc05f3dda885d77c0b4ae2e3a01cf92cec7",
	"email" : "test@email.com",
	"password" : "123",
	"xdc" : 1001,
	"otp" : "",
	"merchantId" : "00",
	"apiKey" : "04af606c-38a3-11e8-b467-0ed5f89f718b"
}'
```

> The JSON encoded response on successful request

```shell
{
    "status": 'success',
    "data": []
}
```

### Parameters

Argument | Type | Required | Description
-------|-------|----- |------
address| String | yes | Public address
email| String | yes | 
password| String | yes | 
xdc| String | yes | xdc amount
otp| String | yes | otp
merchantId| String | yes | 
apiKey| String | yes | 


# Endpoints - General

## Get XDC Price

Get today's xdc price.

### HTTP Request
<code>POST https://[host]/api/v1/wallet/qrcode</code>


```shell
curl -X GET \
  http://localhost:3000/api/v1/today-xdc-price \
  -H 'cache-control: no-cache' \
  -H 'content-type: application/json'
```

> The JSON encoded response on successful request

```shell
{
    "status": "success",
    "data": {
        "todayxdcprice": "0.00277758"
    }
}
```

<aside class="notice">
This endpoint does not need authentication.
</aside>

## Contact

Creates support ticket.

### HTTP Request
<code>POST https://[host]/api/v1/contact</code>


```shell
curl -X POST \
  http://localhost:3000/api/v1/contact \
  -H 'cache-control: no-cache' \
  -H 'content-type: application/json' \
  -d '{
  "apiKey" : "04af606c-38a3-11e8-b467-0ed5f89f718b",
  "merchantId" : "00",
  "email" : "test@email.com",
  "name" : "John Doe",
  "query" : "body text"
}'
```

> The JSON encoded response on successful request

```shell
{
   	status: 'success',
	data: {
		message: 'Query Sent. Someone from Xinfin will contact you shortly.'
	}

}
```

<aside class="notice">
This endpoint does not need authentication.
</aside>