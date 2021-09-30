# Privado Network Verification Wizard

A simple wizard for users to perform a Privado verification process to operate with a VASP (e.g. exchanges).

## Usage

Verification Wizard is a web page that can be used as an iframe.

To get the iframe URL, you will need to send a POST request to `https://api.sandbox.privadoid.com/api/v1/verifications/start` sending your API credentials (`token` and `password`) as headers and the parameters below:

| parameter              | description                                                       |
|------------------------|-------------------------------------------------------------------|
| verificationReference  | Exchange's internal reference to the verification                 |
| userReference          | Exchange's internal reference to the user                         |
| locale                 | Renders content in the specified language                         |
| callbackUrl            | URL to notify the VASP that the verification process has finished |

This is how a request would looks like:

```
const token = '5172ad1b-81ec-4a02-8e75-544fd8459888'
const password = '43e65025666647dfa43e953ce74e8548'

const headers = new Headers()
headers.set('Authorization', 'Basic ' + btoa(token + ':' + password))
const response = await fetch('https://api.sandbox.privadoid.com/api/v1/verifications/start', {
  method: 'POST',
  headers,
  body: JSON.stringify({
    verificationReference: '1',
    userReference: 'user1',
    locale: 'en',
    callbackUrl: 'https://deversifi.com/callback?verificationReference=${verificationReference}'
  })
})
```

And this is how the response would looks like:

```
{
  timestamp: "2021-09-17T13:21:10.294Z",
  url: "https://wizard.sandbox.privadoid.com?token=ef5bf131-297a-4a5c-8793-4e9e99cf469a"
}
```

Using an iframe, you can embed the Verification Wizard inside your site wherever you prefer. We recommend to use a modal.
You can check an example about how the iframe tag would looks like here:

```
<iframe allow='camera' src='https://wizard.sandbox.privadoid.com?token=ef5bf131-297a-4a5c-8793-4e9e99cf469a'></iframe>
```

Or, if you prefer, you can set the `src` prop using javascript:

```
// gets the iframe element
const iframeElement = document.querySelector('iframe')

// This is the response we got from the POST request
const content = await response.json()

// sets the 'src' tag with the URL we got from request
iframeElement.src = content.url
```

_note: if you are using a modal, remember to set the `src` through javascript just after having the modal element opened, otherwise it will not find the iframe tag. An example can be checked in the project's `example` folder._

---
**Important**

You need to set the iframe prop `allow` to `camera`, thus the users will be able to do the verification process using their webcam.

---

Also, after finishing the KYC verification process inside the Verification Wizard iframe, you might want to have an event to know when you can close the modal.
To do this, you can set an event listener to the `privado-verification` post message we sent after finishing the process. Check the example below:

```
// adds the event listener
window.addEventListener('message', function (e) {
  // checks if the post message is from 'privado-verification'
  if (e.data.source === undefined || e.data.source !== 'privado-verification') {
    return
  }

  // gets the modal element with the id 'modal'
  const modalElement = document.getElementById('modal')

  // sets the display to 'none', thus closing the modal
  modalElement.style.display = 'none'
})
```

## Built with

- [Create React App (CRA)](https://github.com/facebook/create-react-app).
- [Typescript](https://www.typescriptlang.org/)

Linting setup:

- [ts-standard](https://github.com/standard/ts-standard)
- [husky](https://github.com/typicode/husky)
- [lint-staged](https://github.com/okonet/lint-staged)

## License

TBD