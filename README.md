# USPS API Notes

This repo is a collection of notes taken by 18F staff as they use different USPS APIs.

## Generally

There is no single "USPS API". Rather, USPS offers multiple APIs which can be more or less used independent of one another. All of the APIs are XML-based and usually follow the form of:

```
GET https://secure.shippingapis.com/ShippingAPI.dll?API=[NAME_OF_API]&XML=[AN_XML_STRING]
```

### API Permissions

Several of the APIs, such as the shipping label APIs, require permissions before they can be accessed. An email requesting the permission sent to uspstechnicalsupport@mailps.custhelp.com, along with a short summary of how you will use the API should be enough.

### Transactional vs. Bulk Use

It's important to note that USPS does not allow its APIs to be used for bulk processing. Rather, API access should be transactional.

### Client Libraries

At 18F, we use the [USPS Ruby gem](https://github.com/gaffneyc/usps) on at least one project. The gem covers several of the APIs (but not all).

We also have developed another Ruby gem, [usps-proxy](https://github.com/18F/usps-proxy) which, provides a JSONic interface to the API. This lets us keep API keys secret and avoid generating and parsing XML with client-side Javascript.

## Shipping Labels

One current use case for printing shipping labels is a user completes a form online that generates a document that must be printed and mailed somewhere. Including a pre-filled shipping label for users is one way to make the process smoother. Because USPS offers several label-related APIs, it's useful to know each of their capabilities and limitations.

Currently, the USPS gem does not cover any of the shipping label APIs.

### Electronic Merchandise Return Service API

Official documentation: https://www.usps.com/business/web-tools-apis/merchandise-return-service-label-api.htm

This API is designed for merchants to simplifiy the merchandise return process for their customers. Postage is already paid by the merchant. The merchant establishes a financial relationship with USPS, usually by providing a credit card or account routing number.

In order to use this API, merchants must apply for a permit by filling submitting the [PS 3615](http://about.usps.com/forms/ps3615.pdf) form. There are two fees associated with this permit:

- $220 annual permit fee
- $685 annual account maintenance fee

See [PS 3621](http://about.usps.com/forms/ps3621a.pdf) for a full list of permit fees.

#### Example Request

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<EMRSV4.0CertifyRequest USERID="XXXXXX" PASSWORD="XXXXXX">
  <Option>RIGHTWINDOW</Option>
  <CustomerName>Janice Dickens</CustomerName>
  <CustomerAddress1>Ste 201</CustomerAddress1>
  <CustomerAddress2>7 N Wilkes Barre Blvd</CustomerAddress2>
  <CustomerCity>Wilkes Barre</CustomerCity>
  <CustomerState>PA</CustomerState>
  <CustomerZip5>18702</CustomerZip5>
  <CustomerZip4 />
  <RetailerName>XYZ Corp.</RetailerName>
  <RetailerAddress>123 Main St</RetailerAddress>
  <PermitNumber>293829</PermitNumber>
  <PermitIssuingPOCity>New York</PermitIssuingPOCity>
  <PermitIssuingPOState>NY</PermitIssuingPOState>
  <PermitIssuingPOZip5>10001</PermitIssuingPOZip5>
  <PDUFirmName>PDU Firm Co.</PDUFirmName>
  <PDUPOBox>PO Box 100</PDUPOBox>
  <PDUCity>Wilkes Barre</PDUCity>
  <PDUState>PA</PDUState>
  <PDUZip5>18702</PDUZip5>
  <PDUZip4>1234</PDUZip4>
  <ServiceType>Priority</ServiceType>
  <DeliveryConfirmation>False</DeliveryConfirmation>
  <InsuranceValue />
  <MailingAckPackageID>ID00001</MailingAckPackageID>
  <WeightInPounds>0</WeightInPounds>
  <WeightInOunces>10</WeightInOunces>
  <RMA>RMA 123456</RMA>
  <RMAPICFlag>False</RMAPICFlag>
  <ImageType>TIF</ImageType>
  <RMABarcode>False</RMABarcode>
</EMRSV4.0CertifyRequest>
```

#### Example Response

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<EMRSV4.0CertifyResponse>
  <Zone>1</Zone>
  <MerchandiseReturnLabel>
    SUkqAAgAAAASAP4ABAABAAAAAAAAAAABBAABAAAAp...<!--Truncated-->
  </MerchandiseReturnLabel>
  <MerchandiseReturnNumber>420207709184301555121000000009</MerchandiseReturnNumber>
  <InsuranceCost />
  <PDUFirmName>PDU FIRM CO.</PDUFirmName>
  <PDUPOBox>PO BOX 100</PDUPOBox>
  <PDUCity>WILKES BARRE</PDUCity>
  <PDUState>PA</PDUState>
  <PDUZip5>18703</PDUZip5>
  <PDUZip4/>
  <Postnet>18703</Postnet>
  <CustomerAddress1>STE 201</CustomerAddress1>
  <CustomerAddress2>7 N WILKES BARRE BLVD</CustomerAddress2>
  <CustomerCity> WILKES BARRE </CustomerCity>
  <CustomerState>PA</CustomerState>
  <CustomerZip5>18701</CustomerZip5>
  <CustomerZip4>5241</CustomerZip4>
  <CustomerPostNet>18702524151</CustomerPostNet>
</EMRSV4.0CertifyResponse>
```

### Express Mail Label API

Official documentation: https://www.usps.com/business/web-tools-apis/priority-mail-express-api.htm

This API allows users to "[g]enerate a single-ply Priority Mail Express shipping label complete with return and delivery addresses, a barcode, and a mailing record for your use."

Postage is unpaid, meaning the user must buy stamps, or weigh the parcel and buy postage at a post office. However, a permit is not required.

#### Example Request

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<ExpressMailLabelCertifyRequest  USERID="XXXXXX">
  <Option />
  <Revision>2</Revision>
  <EMCAAccount />
  <EMCAPassword />
  <ImageParameters />
  <FromFirstName>Adam</FromFirstName>
  <FromLastName>Smith</FromLastName>
  <FromFirm/>
  <FromAddress1>RM 2100</FromAddress1>
  <FromAddress2>475 L’Enfant Plaza SW</FromAddress2>
  <FromCity>Washington</FromCity>
  <FromState>DC</FromState>
  <FromZip5>20260</FromZip5>
  <FromZip4/>
  <FromPhone>2125551234</FromPhone>
  <ToFirstName>Janice</ToFirstName>
  <ToLastName>Dickens</ToLastName>
  <ToFirm>XYZ Corporation</ToFirm>
  <ToAddress1>Ste 100</ToAddress1>
  <ToAddress2>2 Massachusetts Ave NE</ToAddress2>
  <ToCity>Washington</ToCity>
  <ToState>DC</ToState>
  <ToZip5>20212</ToZip5>
  <ToZip4 />
  <ToPhone>2125551234</ToPhone>
  <WeightInOunces>105</WeightInOunces>
  <FlatRate/>
  <SundayHolidayDelivery/>
  <StandardizeAddress/>
  <WaiverOfSignature/>
  <NoHoliday/>
  <NoWeekend/>
  <SeparateReceiptPage/>
  <POZipCode>20212</POZipCode>
  <FacilityType>DDU</FacilityType>
  <ImageType>PDF</ImageType>
  <LabelDate>10/19/2010</LabelDate>
  <CustomerRefNo/>
  <SenderName>Adam Smith</SenderName>
  <SenderEMail>asmith@email.com</SenderEMail>
  <RecipientName>Janice Dickens</RecipientName>
  <RecipientEMail>jdickens@email.com</RecipientEMail>
  <HoldForManifest/>
  <CommercialPrice>false</CommercialPrice>
  <InsuredAmount>425.00</InsuredAmount>
  <Container>NONRECTANGULAR</Container>
  <Size>LARGE</Size>
  <Width>7</Width>
  <Length>20.5</Length>
  <Height>15</Height>
  <Girth>60</Girth>
</ExpressMailLabelCertifyRequest>
```

#### Example Response

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<ExpressMailLabelCertifyResponse xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <ToFirstName>Janice</ToFirstName>
  <ToLastName>Dickens</ToLastName>
  <ToFirm>XYZ CORPORATION</ToFirm>
  <ToAddress1>Ste 100</ToAddress1>
  <ToAddress2>2 MASSACHUSETTS AVE NE</ToAddress2>
  <ToCity>WASHINGTON</ToCity>
  <ToState>DC</ToState>
  <ToZip5>20212</ToZip5>
  <ToZip4>0002</ToZip4>
  <Postage>28.60</Postage>
  <EMConfirmationNumber>4202021294712XXXXXXXXXXXXXXXXX</EMConfirmationNumber>
  <EMLabel>ZGVmYXVsdA==</EMLabel>
  <EMReceipt>ZGVmYXVsdA==</EMReceipt>
  <RDC>0007</RDC>
  <InsuranceFee>2.40</InsuranceFee>
  <ExtraServices />
  <Zone>1</Zone>
  <CarrierRoute>C000</CarrierRoute>
</ExpressMailLabelCertifyResponse>
```






