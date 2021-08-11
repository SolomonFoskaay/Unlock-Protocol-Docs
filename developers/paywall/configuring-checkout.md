---
description: >-
  When building a checkout URL or configuring the paywall, you can customize
  things. Here are docs on how to achieve this.
---

# Configuring Checkout

## Building a Purchase address 

If you are using the paywall application, you can skip this section and move to the next one \(the `paywallConfig` object\).

All of the purchase URsL start with the following base

```
https://app.unlock-protocol.com/checkout?
```

After this, you will need to include the following parameters:

* `paywallConfig=...` where `...` is replaced with the URL-encoded version of a JSON `paywallConfig` object. The next section will show you how to build this object. You can use online tools such as [https://www.urlencoder.io/](https://www.urlencoder.io/) to build the encoded version of the object.
* `redirectUri=...` where `...` is replaced with the URL-encodded address of a webpage where the user will be redirected when their membership is valid. 

These parameters are all separated by the `&` sign.

#### Example

```javascript
https://app.unlock-protocol.com/checkout?redirectUri=https://ouvre-boite.com&paywallConfig=%7B%22locks%22%3A%7B%220x15F67811Beb43aCE162693fe1415916F87B8C5C2%22%3A%7B%22network%22%3A137%7D%7D%2C%22persistentCheckout%22%3Atrue%2C%22icon%22%3A%22https%3A%2F%2Frinkeby.locksmith.unlock-protocol.com%2Flock%2F0x15F67811Beb43aCE162693fe1415916F87B8C5C2%2Ficon%22%7D
```

This URL will redirect members to this page [`https://ouvre-boite.com/`](https://ouvre-boite.com/). 

## The paywallConfig object

The `paywallConfig` is a JSON object which includes a set of customizations for your experience. It includes the following elements:

* `locks` : _required object_, a list of lock objects \(see below\).
* `icon`: _optional string_, the URL for a icon to display in the top left corner of the modal.
* `callToAction`: _optional object_, a list of messages shown based on the state of the checkout modal \(see below\).
* `metadataInputs`:  _optional array_, a set of input fields [as explained there](collecting-metadata.md).
* `persistentCheckout`: _optional boolean_: `true` __if the modal cannot be closed, defaults to `false` when embedded. When closed, the user will be redirected to the `redirect` query param when using a purchase address \(see above\).
* `useDelegatedProvider`: _optional boolean._  To be announced.
* `network`: _optional integer._ defaults to `1`.  See below.
* `referrer`: _optional string_. The address which will [receive UDT tokens](../../governance/the-unlock-token/) \(if the transaction is applicable\)
* `messageToSign`: _optional string_. If supplied, the user is prompted to sign this message using their wallet. If using a checkout URL, the signature is then appended to the `redirectUri` \(see above\). If using the embedded paywall, the `unlockProtocol.authenticated` includes the signature.
* `pessimistic`: _optional boolean._ defaults to `false`_._ By default, to reduce friction, we do not require users to wait for the transaction to be mined before offering them to be redirected. By setting this to `true`, users will need to wait for the transaction to have been mined in order to proceed to the next step. 

#### Locks

The locks object is a list of objects indexed by the lock address, where each object can include the following:

* `network`: _recommended integer_. See below.
* `name`: _optional string_. name of the lock to display.

#### Calls to action

The `callToAction` object lets you customize the messages displayed on the checkout UI. They are all optional strings:

* `default` : displayed by default
* `expired` : displayed when the user had a membership previously that expired
* `metadata`: displayed when the user is prompted for metadata

#### Network values:

* `1`: mainnet, 
* `4`: rinkeby, 
* `100`: xdai, 
* `137`: polygon.

### Full example

```javascript
{
  network: 1, // Network ID (1 is for mainnet, 4 for rinkeby... etc)
  messageToSign: "Connecting my wallet to MyApp",
  pessimistic: true,
  locks: {
    '0xabc': { // 0xabc is the address of a lock.
      name: 'One Week',
      network: 1 // you can customize the network for each lock
    }, 
    '0xdef': {
      name: 'One Month' 
      network: 100 // lock on the xDai chain
    },
  },
  icon: 'https://app.unlock-protocol.com/static/images/svg/default.svg', 
  callToAction: {
    default: 'This content is locked. Pay with cryptocurrency to access it!',
  },
  referrer: '0xreferrer' // Address of the referrer who will earn UDT governance tokens if the transaction is eligible.
}
```


