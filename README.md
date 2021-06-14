#  Privado Network KYC API Implementation Guide

This is a reference manual for the Privado Network KYC API. It describes how to initialize a verification process and onboard users into the network. Also offers ways to check the status of the transactions.

<br>

## Authentication and encryption
API calls are protected using [HTTP Basic Authentication](https://tools.ietf.org/html/rfc7617). Your Basic Auth credentials are constructed using your API token as the user-id and your API secret as the password.

|⚠️ Never share your API token, API secret, or Basic Auth credentials with *anyone* — not even Privado Network Support.
|:----------|

<br>



Sandbox environment for privado network APIs:

```https://sandbox.privado.network/api/v1```

<br>

The [TLS Protocol](https://tools.ietf.org/html/rfc5246) is required to securely transmit your data, and we strongly recommend using the latest version.

<br>

# Initiate KYC Process

Call the RESTful API POST endpoint **/verifications** with a JSON object containing the properties described below to start a verification process.

## Request headers

The following fields are required in the header section of your request:<br>

`Accept: application/json`<br>
`Content-Type: application/json`<br>
`Content-Length:`  (see [RFC-7230](https://tools.ietf.org/html/rfc7230#section-3.3.2))<br>
`Authorization:` (see [RFC 7617](https://tools.ietf.org/html/rfc7617))<br>
`User-Agent: YourCompany YourApp/v1.0`<br>

|ℹ️ Privado Network requires the `User-Agent` value to reflect your business or entity name for API troubleshooting.|
|:---|

<br>

## Request body

<br>

**Required items appear in bold type.**

| Name | Type | Max. length | Description |
| ---- | ---- | ----------- | ----------- |
| customerInternalReference | String     | 100 | VASP's internal reference for the transaction. |
| **userReference** | String | 100 | VASP's internal reference for the user. |
| **email**<sup>1</sup>    | String | 100 | Email user used to register into the VASP. |
| **mobile**<sup>1</sup>    | String | 100 | Mobile user used to register into the VASP. |
| **ethAddress**<sup>1</sup>    | String | 42 | Ethereum Address user used to register into the VASP. |
| **successUrl** | String | 2047 | Redirects to this URL after a successful transaction. |
| **errorUrl** | String | 255 | Redirects user to this URL after an unsuccessful transaction. |
| **callbackUrl** | String | 255 | Call this URL upon completion.
| locale | String | 5 | Renders content in the specified language. |

<br>

<sup>1</sup> Only one parameter is required.

<br>

## Supported `locale` values
Hyphenated combination of [ISO 639-1:2002 alpha-2](https://en.wikipedia.org/wiki/ISO_639-1) language code plus [ISO 3166-1 alpha-2](https://en.wikipedia.org/wiki/ISO_3166-1_alpha-2) country (where applicable).


|Value  |Locale|
|:--------------|:--------------|
|ar|Arabic|
|bg|Bulgarian|
|cs|Czech|
|da|Danish|
|de|German|
|el|Greek|
|en|American English (**default**)|
|en-GB|British English|
|es|Spanish|
|es-MX|Mexican Spanish|
|et|Estonian|
|fi|Finnish|
|fr|French|
|he|Hebrew|
|hr|Croatian|
|hu|Hungarian|
|hy|Armenian|
|id|Indonesian|
|it|Italian|
|ja|Japanese|
|ka|Georgian|
|km|Khmer|
|ko|Korean|
|lt|Lithuanian|
|ms|Malay|
|nl|Dutch|
|no|Norwegian|
|pl|Polish|
|pt|Portuguese|
|pt-BR|Brazilian Portuguese|
|ro|Romanian|
|ru|Russian|
|sk|Slovak|
|sv|Swedish|
|th|Thai|
|tr|Turkish|
|vi|Vietnamese|
|zh-CN|Simplified Chinese|
|zh-HK|Traditional Chinese|

---

<br>

## Response

Unsuccessful requests will return the relevant [HTTP status code](https://tools.ietf.org/html/rfc7231#section-6) and information about the cause of the error.

Successful requests will return HTTP status code ```200 OK```  along with a JSON object containing the information described below.

| Name | Type | Max. length | Description |
| ---- | ---- | ----------- | ----------- |
| timestamp | String     | 24 | Timestamp (UTC) of the response. Format: YYYY-MM-DDThh:mm:ss.SSSZ |
| redirectUrl | String     |  | URL used to load the ID Verification client.|
| transactionReference | String     | 36  | Reference number for the transaction.|

<br>

## Examples
### Sample Request

```JSON=
POST https://sandbox.privado.network/api/v1/verifications/ HTTP/1.1
Accept: application/json
Content-Type: application/json
Content-Length: 1234
User-Agent: Vasp Name or Identifier/1.0.0
Authorization: Basic xxxxxxxxxxxxxxxxxxxxxxxxxxxxx
{
  "customerInternalReference" : "transaction_1",
  "userReference" : "1234",
  "userEmail" : "user@example.com",
  "successUrl" : "https://www.vasp1.com/success",
  "errorUrl" : "https://www.vasp1.com/error",
  "callbackUrl" : "https://www.vasp1.com/callback?userReference=1234",
  "locale" : "en-GB"
}
```

### Sample Response

```json=
{
  "timestamp": "2018-07-03T08:23:12.494Z",
  "transactionReference": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "redirectUrl": "https://yourcompany.netverify.com/web/v4/app?locale=en-GB&authorizationToken=xxx"
}
```
<br>
<br>

# ID Verification Process Status

Call the RESTful API GET endpoint **/verifications** to ask the status of a verification process.

## Request headers

The following fields are required in the header section of your request:<br>

`Accept: application/json`<br>
`Content-Type: application/json`<br>
`Content-Length:`  (see [RFC-7230](https://tools.ietf.org/html/rfc7230#section-3.3.2))<br>
`Authorization:` (see [RFC 7617](https://tools.ietf.org/html/rfc7617))<br>
`User-Agent: YourCompany YourApp/v1.0`<br>

|ℹ️ Privado Network requires the `User-Agent` value to reflect your business or entity name for API troubleshooting.|
|:---|

<br>

## Request

To request the verification process status the endpoint must be called specifying the transaction reference of an existing transaction as a path parameter.

## Response

| Name | Type | Max. length | Description |
| ---- | ---- | ----------- | ----------- |
| timestamp | String     |  | Timestamp (UTC) of the response. Format: YYYY-MM-DDThh:mm:ss.SSSZ |
| transactionReference | String     | 36  | Transaction reference number |
| status | String     |   | Possible states: • PENDING • DONE • FAILED |

<br>

## Examples

### Sample Request

```json=
GET https://sandbox.privado.network/api/v1/verifications/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx HTTP/1.1
Accept: application/json
User-Agent: Vasp Name or Identifier/1.0.0
Authorization: Basic xxxxxxxxxxxxxxxxxxxxxxxxxxxxx
```

### Sample Response

```json=
{
    "timestamp": "2019-01-01T16:23:55.039Z",
    "transactionReference": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "status": "DONE"
}
```

<br>
<br>

# Check if a user already exists in Privado Network

Call the RESTful API GET endpoint **/onboarded-users** passing an email address or mobile phone number as query parameters to check if a user is already onboarded in Privado Network. 

<br>

## Request headers

The following fields are required in the header section of your request:<br>

`Accept: application/json`<br>
`Content-Type: application/json`<br>
`Content-Length:`  (see [RFC-7230](https://tools.ietf.org/html/rfc7230#section-3.3.2))<br>
`Authorization:` (see [RFC 7617](https://tools.ietf.org/html/rfc7617))<br>
`User-Agent: YourCompany YourApp/v1.0`<br>

|ℹ️ Privado Network requires the `User-Agent` value to reflect your business or entity name for API troubleshooting.|
|:---|

<br>

## Request Parameters

<br>

**Required items appear in bold type.**


| Name | Type | Max. length | Description |
| ---- | ---- | ----------- | ----------- |
| **email**<sup>1</sup>| String     | 100 | Email user is trying to register with |
| **mobile**<sup>1</sup> | String     | 100 | Mobile user is trying to register with |
| **ethAddress**<sup>1</sup> | String     | 42 | Ethereum address user is trying to register with |

<br>

<sup>1</sup> Only one parameter is required.

<br>

## Response

Unsuccessful requests will return the relevant [HTTP status code](https://tools.ietf.org/html/rfc7231#section-6) and information about the cause of the error.

Successful requests will return HTTP status code ```200 OK```  along with a JSON object containing the information described below.

| Name | Type | Max. length | Description |
| ---- | ---- | ----------- | ----------- |
| timestamp | String     | 24 | Timestamp (UTC) of the response. Format: YYYY-MM-DDThh:mm:ss.SSSZ |
| id | String     | 36 | The ID of the user in Privado Network if exists
| exists | Boolean     |  | True if user exists in Privado Network and is valid
| onboarded | Boolean     |   | True if the user is already onboarded for the requesting VASP |

## Examples

### Sample Request

```JSON=
GET https://sandbox.privado.network/api/v1/onboarded-users?email=test@example.com HTTP/1.1
Accept: application/json
Content-Type: application/json
Content-Length: 1234
User-Agent: Vasp Name or Identifier/1.0.0
Authorization: Basic xxxxxxxxxxxxxxxxxxxxxxxxxxxxx
```

### Sample Response

```json=
{
  "timestamp": "2018-07-03T08:23:12.494Z",
  "id": "1f27d649-c8d2-46ed-af57-072919d87fd6"
  "exists": "true",
  "onboarded": "false"
}

```
<br>
<br>

# Onboard existing user

Call the RESTful API POST endpoint **/onboarded-users** with a JSON object containing the properties described below to onboard an existing user in the network into your VASP. Notice that the user will be notified and must give his/her consent in order to fullfill this onboard process.

## Request headers

The following fields are required in the header section of your request:<br>

`Accept: application/json`<br>
`Content-Type: application/json`<br>
`Content-Length:`  (see [RFC-7230](https://tools.ietf.org/html/rfc7230#section-3.3.2))<br>
`Authorization:` (see [RFC 7617](https://tools.ietf.org/html/rfc7617))<br>
`User-Agent: YourCompany YourApp/v1.0`<br>

|ℹ️ Privado Network requires the `User-Agent` value to reflect your business or entity name for API troubleshooting.|
|:---|

<br>

## Request body

**Required items appear in bold type.**

| Name | Type | Max. length | Description |
| ---- | ---- | ----------- | ----------- |
| **id** | String     | 36 | The ID of existing user in Privado Network  |
| **userReference** | String     | 100 | Vasp's internal reference for the user |
| **callbackURL** | String     | 100 | Sends onboarding result to this URL upon completion |

<br>

## Response

Unsuccessful requests will return the relevant [HTTP status code](https://tools.ietf.org/html/rfc7231#section-6) and information about the cause of the error.

Successful requests will return HTTP status code ```200 OK```  along with a JSON object containing the information described below.

| Name | Type | Max. length | Description |
| ---- | ---- | ----------- | ----------- |
| timestamp | String     | 24 | Timestamp (UTC) of the response. Format: YYYY-MM-DDThh:mm:ss.SSSZ |
| success | Boolean     |  | Indicates the onboarding process has started correctly|

<br>

## Examples

### Sample Request

```JSON=
POST https://sandbox.privado.network/api/v1/onboarded-users/ HTTP/1.1
Accept: application/json
Content-Type: application/json
Content-Length: 1234
User-Agent: Vasp Name or Identifier/1.0.0
Authorization: Basic xxxxxxxxxxxxxxxxxxxxxxxxxxxxx
{
  "id": "1f27d649-c8d2-46ed-af57-072919d87fd6",
  "userReference": "1234",
  "callbackURL": "https://vasp.com/onboardcallback?uRef=1234"
}
```

### Sample Response

```json=
{
  "timestamp": "2018-07-03T08:23:12.494Z",
  "success": "true"
}
```
