#  Privado Network API Implementation Guide

This is a reference manual for the Privado Network KYC API. It describes how to initialize a verification process and onboard users into the network. Also offers ways to check the status of the transactions.

<br>

## Authentication and encryption
API calls are protected using [HTTP Basic Authentication](https://tools.ietf.org/html/rfc7617). Your Basic Auth credentials are constructed using your API token as the user-id and your API secret as the password. The API credentials can be obtained in the settings area of Privado Network Admin website.

|⚠️ Never share your API token, API secret, or Basic Auth credentials with *anyone* — not even Privado Network Support.
|:----------|

<br>



Sandbox environment for privado network APIs:

```https://api.sandbox.privadoid.com/v1```

<br>

The [TLS Protocol](https://tools.ietf.org/html/rfc5246) is required to securely transmit your data, and we strongly recommend using the latest version.

<br>

# Initiate KYC Process

Call the RESTful API POST endpoint **/verifications/start** with a JSON object containing the properties described below to start a verification process.

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
| **verificationReference** | String     | 100 | VASP's chosen ID for the transaction. |
| **userReference** | String | 100 | VASP's chosen ID for the user. |
| **locale** | String | 5 | Renders content in the specified language. *|
| **callbackUrl** | String | 255 | Call this URL upon completion.

\* currently locale is only applied to the verification process

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
| url | String     |  | URL used to load the ID verification wizard.|


<br>

## Examples
### Sample Request

```JSON=
POST https://api.sandbox.privadoid.com/v1/verifications/start HTTP/1.1
Accept: application/json
Content-Type: application/json
Content-Length: 987
User-Agent: Vasp Name or Identifier/1.0.0
Authorization: Basic xxxxxxxxxxxxxxxxxxxxxxxxxxxxx
{
  "verificationReference" : "6a4d2c81-ada4-498b-94fe-449335cebd01",
  "userReference" : "5172ad1b-81ec-4a02-8e75-544fd8459888",
  "locale" : "en",
  "callbackUrl" : "https://www.vasp1.com/users/5172ad1b-81ec-4a02-8e75-544fd8459888/callback",
}
```

### Sample Response

```json=
{
  "timestamp": "2018-07-03T08:23:12.494Z",
  "url": "https://wizard.internal.privadoid.com?vasp=00000000-0000-0000-0000-000000000001\u0026token=df9bc110-f5a4-44cb-8ad1-900e0baad37b"
}
```
<br>

# ID Verification Process Status

Call the RESTful API GET endpoint **/verifications/:verificationRefrence/status** to ask the status of a verification process.

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

To request the verification process status the endpoint must be called specifying the verification process ID of an existing process as a query parameter.

## Response

| Name | Type | Max. length | Description |
| ---- | ---- | ----------- | ----------- |
| timestamp | String     |  | Timestamp (UTC) of the response. Format: YYYY-MM-DDThh:mm:ss.SSSZ |
| verificationReference | String     | 100  |  VASP's chosen ID for the transaction. |
| status | String     |   | Possible states: PENDING, DONE, FAILED |
| rejectReason | String     |   | Detail in case of FAILED validation |

<br>

## Examples

### Sample Request

```json=
GET https://api.sandbox.privadoid.com/v1/verifications/6a4d2c81-ada4-498b-94fe-449335cebd01/status HTTP/1.1
Accept: application/json
User-Agent: Vasp Name or Identifier/1.0.0
Authorization: Basic xxxxxxxxxxxxxxxxxxxxxxxxxxxxx
```

### Sample Response

```json=
{
    "timestamp": "2019-01-01T16:23:55.039Z",
    "verificationReference": "6a4d2c81-ada4-498b-94fe-449335cebd01",
    "status": "DONE"
}
```