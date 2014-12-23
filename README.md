# USPS API Notes

This repo is a collection of notes taken by 18F staff as they use different USPS APIs.

## Generally

There is no single "USPS API". Rather, USPS offers multiple APIs which can be more or less used independent of one another. All of the APIs are XML-based and usually follow the form of:

```
GET https://secure.shippingapis.com/ShippingAPI.dll?API=[NAME_OF_API]&XML=[AN_XML_STRING]
```

### Client Libraries

At 18F, we use the [USPS Ruby gem](https://github.com/gaffneyc/usps) on at least one project. The gem covers several of the APIs (but not all).

We also have developed another Ruby gem, [usps-proxy](https://github.com/18F/usps-proxy) which, provides a JSONic interface to the API. This lets us keep API keys secret and avoid generating and parsing XML with client-side Javascript.

## Shipping Labels

One current use case for printing shipping labels is a user completes a form online that generates a document that must be printed and mailed somewhere. Including a pre-filled shipping label for users is one way to make the process smoother. Because USPS offers several label-related APIs, it's useful to know each of their capabilities and limitations.

Currently, the USPS gem does not cover any of the shipping label APIs.

### Electronic Merchandise Return Service

This API is designed for merchants to simplifiy the merchandise return process for their customers. Postage is already paid by the merchant. The merchant establishes a financial relationship with USPS, usually by providing a credit card or account routing number.

In order to use this API, merchants must apply for a permit by filling submitting the [PS 3615](http://about.usps.com/forms/ps3615.pdf) form. There are two fees associated with this permit:

- $220 annual permit fee
- $685 annual account maintenance fee

See [PS 3621](http://about.usps.com/forms/ps3621a.pdf) for a full list of permit fees.
