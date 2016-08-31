# enigma-integration
Integration documentation for Enigma


## Authentication

The api uses the basic authentication scheme. So for example:
```
Username - test
password - test12345 
header - Authorization: Basic dGVzdDp0ZXN0MTIzNDU= 
```
This authentication header must be provided on every request to the api. If you do not you will get a 401 Unauthorised response with "Invalid username or password" in the body of the response.

## Issuing: 

The path to issue a new value voucher is : 
https://ENIGMA_URL/{tenant}/definitions/{definitionId}/vouchers

You will need to POST the following data to the route: 

```
{
	"externalReferenceCode": "YOUR_REFERENCE_HERE",
	"definition": {
    	"voucherType": {"valueAmount": 50}
    }
}
```

It will return the voucher synchronously. {definitionId} is a huge GUID - this is voucher-type Id to issue against, so this will need to be templated in if you want to issue different types of vouchers.  

There is a lot of data on the voucher, mainly state data - e.g. the issuer data at the time etc. However, you'll probably only need to worry about a few fields here: 

```
{
  "chosenType": "value",
  "validityInDays": 1095,
  "maximumRedemptions": 1,
  "startDate": "2015-07-31T13:41:37.089Z",
  "endDate": "2016-07-31T13:41:37.090Z",
  "name": "Gift Card",
  "description": "Gift Card",
  "voucherIdentifier": "GIFT_CARD",
  "active": true,
  "dateIssued": "2015-08-20T09:45:28.132Z",
  "expiryDate": "2018-08-19T09:45:28.132Z",
  "redemptionCount": 0,
  "value": {
    "allowPartial": true,
    "value": 500,
    "available": 500
  },
  "voucherCode": "N7IB3Y3WU8CZ",
  "voucherBatchId": "55d5a1b8825c7d3427298038",
  "invoiceNumber": "Tst98765",
  "issueDate": "2015-08-20T09:45:28.132Z",
  "_id": "55d5a1b8825c7d3427298039",
  "code": 201,
  "success": true,
  "env": "uat"
}
```

The main property is voucherCode, which is the code the customer can use to redeem. 
The next important one is "expiryDate" which is when the voucher is valid until. 
Then there is "name" and "description" which are self explanatory. 
You can also get the value of the voucher from the "value” sub-object. The "allowPartial" flag in here determines if the voucher is in "Gift Card" mode (i.e the customer doesn't have to use the whole voucher at once). 

You should get an HTTP code of 201 (created) if successful, a 4xx if there is something wrong with the data you submit, and 5xx if our servers have problems (hopefully something you will not see). 

## Balance Check

To check the state of a voucher, including the value on it, you can do a GET operation on: 

$ENIGMA_URL/{tenant}/vouchers?voucherCode={voucherCode}

eg https://$ENIGMA_URL/{tenant}/vouchers?voucherCode={voucherCode}

It return the same object as the issue route, with all the extra data. Once again the fields above are the only ones needed for transacting. 

## Redeeming

The path to redeem a voucher is a POST operation:

https://$ENIGMA_URL/{tenant}/{storeId}/vouchers/redeem?voucherCode={voucherCode}
 
The {storeId} here is a store GUID to track where the voucher was redeemed. Voucher code in the query string is the voucherCode from the issue command above.

The POST body contains the amount to redeem and an invoice/ reference number to tie back the amounts: 
```
{
	"invoiceNumber": "YOUR_REF_HERE", 
    "redemptionAmount": 100
}
```






