# pay-sdk-rest
Payment Gateway REST API

## INTRODUCTION

The document provides the interface information to Integrate with Etisalat Payment Gateway to perform Ecommerce payment using REST (JSON).


##	Transaction Flow

Please find the below transaction flow for Ecommerce transaction.

1.	The registration call should be triggered to payment gateway in which the ReturnPath property with Transaction details like amount , currency , OrderID ect should be mentioned. 
2.	Redirect to Payment Portal URL, returned in Register call with TransactionID as hidden field.
```
<form action=<Payment Portal URL> method="post"> 
<input type='Hidden' name='TransactionID' value=<TransactionID value>/> 
<input type="submit" value="Submit">
```
3.	Payer will proceed with 3D Secure Authentication process.
4.	After 3D Authentication, control will returned back to Merchant’s ReturnPath property URL received in registration call.
5.	Merchant sends Finalization call to complete transaction.


###	Registration

####	Registration Call
Required to provide the below properties while calling Registration. 
Property	Usage	Comments
Request Properties
Customer	Customer ID. Please refer to section 5.1 for Customer details.	MANDATORY
Store	Store. Please refer to section 5.1 for Store details.	OPTIONAL
Terminal	Terminal. Please refer to section 5.1 for Terminal details.	OPTIONAL
Channel	Payment Channel. Please refer to section 5.1 for Channel details.	MANDATORY
Amount	Total amount to be charged.	MANDATORY
Currency	Currency in which above mentioned amount is to be charged.	MANDATORY
OrderID	Merchant can use this property to map id for this transaction w.r.t. his system (can also be auto generated, please refer to section 5.1 for format).	OPTIONAL
OrderName	Short description for order.	OPTIONAL
OrderInfo	Details of order.	OPTIONAL
TransactionHint	It is used to specify which payment instruments should be available to the buyer. Merchant can specify which features should be supported by payment instrument i.e. Auto Capture/Reversal or Manual Capture/Reversal. By default it is set Auto Capture. Kindly refer section 5 for more details about transactionHint.	OPTIONAL
ReturnPath	Buyer will be redirected back to this URL once the authentication of card is completed.	MANDATORY
Response Properties
TransactionID	EPG TransactionID	OPTIONAL
PaymentPortal	EPG Payment Page in which Payer will enter the CreditCard	OPTIONAL
ResponseCode	This field returns the exact response code. Success is always
indicated with 0	MANDATORY
ResponseDescription	User-friendly description of the error in ResponseCode.
Note: This field can only be used to display the error description and
should not be used to check if transaction was successful, to check if
transaction was successful please use ResponseCode field.	MANDATORY
UniqueID	Unique reference ID for this call	MANDATORY

####	Sample Request
```
{
 "Registration" : {
   "Currency": "AED",  
   "ReturnPath": "https://partner.ctdev.comtrust.ae/banktestnbad/Authorization.aspx?capture=true",
   "TransactionHint": "CPT:Y;VCC:Y;",
   "OrderID": "7210055701315195",
   "Store": "Mobile",
   "Terminal": "Recharge",
   "Channel": "Web",
   "Amount": "2.00",
   "Customer": "Demo Merchant",
   "OrderName": "Paybill"
	}
}
```
####	Sample Response
```
{  
   "Transaction":{  
      "PaymentPortal":"https://demo-ipg.comtrust.ae/PaymentEx/Paymentpartner/Payment?lang=en&layout=C0STCBVLEI",
      "PaymentPage":"https://demo-ipg.comtrust.ae/PaymentEx/Paymentpartner/Payment?lang=en&layout=C0STCBVLEI",
      "ResponseCode":"0",
      "ResponseClass":"0",
      "ResponseDescription":"Request Processed Successfully",
      "ResponseClassDescription":"Success",
      "TransactionID":"847718745846",
      "Balance":{  
         "Value":"0"
      },
      "Amount":{  
         "Value":"0"
      },
      "Fees":{  
         "Value":"0"
      },
      "Payer":null,
      "UniqueID":"a25ea7da-a212-406a-967b-94953191aad7"
   }
}
```


###	Finalization

####	Finalization Call
Required to provide the below properties while calling Finalization to complete payment.
Property	Usage	Comments
Request Properties
Customer	Please refer to section 5.1 for BusinessChannel details	MANDATORY
TransactionID	TransactionID returned in Registration response.	OPTIONAL
	Response Properties	
ResponseCode	This field returns the exact response code. Success is always
indicated with 0
	
ResponseDescription	User-friendly description of the error in ResponseCode.
Note: This field can only be used to display the error description and
should not be used to check if transaction was successful, to check if
transaction was successful please use ResponseCode field.	
UniqueID	Unique reference ID for this call	
OrderID	It’s returned only in case of successful transaction and if it had been set during Registration call for same	
ApprovalCode	ApprovalCode, as sent by the issuer bank. Merchant should save this code for future reference and communication with issuer bank related to a particular transaction.	
Amount	Amount charged for the transaction	
Balance	Balance amount for the transaction that has not yet been captured	
CardNumber	Masked credit card number in following format: xxxxxx********xxxx
It will return first 6 and last 4 digits of credit card used for payment.	
CardToken	Tokenized value for the card used, it’s not original credit card number but will always be same for any particular credit card number	
Account	Name of account in payment gateway configurations that transaction happened with (to avoid any confusions, use this field for any references or logging only if advised by Merchant Integration Support)	

####	Sample Request
```
{
  "Finalization": {
    "TransactionID": "755580466468",
    "Customer": "Demo Merchant"
  }
}
```
####	Sample Response
```
{  
   "Transaction":{  
      "ResponseCode":"0",
      "ResponseClass":"0",
      "ResponseDescription":"Request processed successfully",
      "ResponseClassDescription":"Success",
      "Language":"en",
      "ApprovalCode":"421218",
      "Account":"NBAD mCommerce",
      "Balance":{  
         "Value":"0"
      },
      "OrderID":"123456789",
      "Amount":{  
         "Value":"1.120"
      },
      "Fees":{  
         "Value":"0"
      },
      "CardNumber":"411111******1111",
      "Payer":{  
         "Information":"411111******1111"
      },
      "CardToken":"4111110042761111",
      "CardBrand":"Visa",
      "UniqueID":"798ddc2f-f8e4-41e1-a5e3-b065448d75e3"
   }
}
```



##	Properties


### Point of Sale Properties 
 
Property 	Description 
Customer 
 	This property maps to Customer ID as mentioned in Work Order, for testing on staging Demo Merchant should be used as Customer ID. 
Channel 
       
 	Payment Channel to be used for the transaction Acceptable Values: 
•	Web (default) 
•	Terminal 
•	POS 
•	Recurring 
•	Phone 
•	Mail 
•	USSD 
•	MEC
Store 
 
  	The name of the store (this property is optional unless the merchant requested support for more than one store or the default store has not been provisioned in Payment Gateway, Merchant Integration Support team advises the merchant on its usage upon request) 
Terminal 
 	The name of the terminal (this property is optional unless the merchant requested support for more than one terminal or the default terminal has 
 
	not been provisioned in Payment Gateway, Merchant Integration Supportteam advises the merchant on its usage upon request) 
 
### Transaction Properties 
 
Property 	Description 
Language 
 
 
 
 
  
  	This property can be used with any request and it specifies which language is used for error message descriptions. In order to display messages correctly, the proper language code page has to be installed on the server. Currently defined languages: 
-	EN – English 
-	AR – Arabic 
-	QQ – Technical descriptions (detailed description suited for 
troubleshooting and testing, but not recommended to be used as an end user messages) 
Amount 
 	It represents the transaction amount (in standard format with dot as a separator e.g. 12.34) 
Currency 
 
 
 	Transaction’s currency as ISO currency code (e.g. 840 for US Dollar, 874 for UAE Dirham) or ISO currency name (e.g. USD for US Dollar, AED for UAE Dirham). Please refer to following link for complete list: http://www.currency-iso.org/iso_index/iso_tables/iso_tables_a1.htm 
TransactionHint 
  
 
  
 
 
 
 
 
 
 
 
 
   	This property is used to specify which payment instruments should be available to the buyer. Merchant can specify which features should be supported by payment instrument i.e. later reversal, capture, partial reversal, partial capture etc. Additionally a merchant can request the final step to perform either authorization and capture or authorization alone e.g. 
CPT:N –only authorization (Manual Capture) 
CPT:Y –authorization and capture (Auto Capture) (Default behavior) 
Merchant can also use this property to select one of scenarios which has been configured on Payment Gateway – SCN:<scenario letter> e.g. SCN:X – ‘X’ is the Scenario ID as configured and communicated by EPG team for a particular type of transaction scenario 
For controlling whenever and for which brands Payment Portal should require payer to enter Verification Code (i.e. CVV2, CVC2, CID). VCC tag will control verification codes for all brands, while CVV, CVC, CID will control it for specific brand only e.g. 
VCC:Y –ask verification code for all brands (Default behavior) 
VCC:N – don’t ask verification code for any brand 
  	 	 
 
 
 
 
 
 
 
 
  
  
 
 
  	o GMT / Local time 
By default all dates and sequences are using GMT (UTC) time, in order to use local time instead, the suffix “g” can be added to any command, this suffix should be placed before the number in the sequence (or before closing “}” if a sequence does not contain any numbers). 
Examples (assuming it’s 1st of December 2004, 14:12pm): {Y}{m}{d}{Od5} – generates: 2004120100001, 2004120100002, 2004120100003 etc. and then in the next day 2004120200001, 2004120200002, 2004120100003 etc. {Yg}{mg}{dg}{Od5g} – same as above but operating on local UAE time · Box{b}{H}{Oy7}–generates: BoxDec140000001, BoxDec140000002 etc., at 15pm it resets to BoxDec150000001, BoxDec150000002 etc. 
OrderName 
  	Short description of the order. The OrderName has to be shorter than 25 characters. It can contain only printable Unicode characters and it cannot neither start nor end nor have to adjacent white characters. 
OrderInfo 
 
 
  	Long description of the goods which are being purchased (this will be displayed on Common Payment Page as a tooltip). The OrderInfo has to be shorter than 256 characters. It can contain only printable and end of line Unicode characters and it cannot neither start nor end nor have to adjacent white characters. 
TransactionID 
 	Transaction reference number. This is returned by EPG itself in response of WEB SERVICE Registration call 
RecurrenceID	Transaction reference number for registered credit card to initiate recurrence transaction.
MerchantAccount	Name of account in payment gateway configurations that transaction happened with (to avoid any confusions, use this field for any references or logging only if advised by Merchant Integration Support)	
### Buyer Properties 
 
Property 	Description 
AccountNumber 	Credit or Debit Card number
ExpiryDate 
 	Specify ExpiryDate as yyyy-mm (given format is recommended, but yy-mm mm/yyyy, mm/yyare also accepted); 
VerifyCode 
 
 
  	This property refers to CVV2/CVC/etc. The format of the value has to match format used by given card brand (3 digits for Visa/MC/JCB and Diners and 4 digits for AMEX and Simulator). Some brands accept to additional symbols: ‘N/A’ to indicate that VerifyCode is unavailable and ‘ILG’ which specifies that the value printed on the card is illegible. 
CardTrackData 
 
 
  	This property is used in case of card present scenario where payer swaps the card into a machine like KIOSK. KIOSK reads the Card Track  Data and sends it to EPG in CardTrackData field. 
Note: In case CardTrackData is being sent to EPG then there is no need to send any other property from Buyer Related Properties. 
  	 	 
  
  	 
 
### Response Properties 
 
These response properties can be retrieved in response to a particular call. For code sample/syntax refer to Section 5.3.8.2 Late Bound Properties. 
Property 	Description 
ResponseCode 
 
 
 
  
  
 
  	This field returns the exact response code. Success is always indicated with 0, and any code using WEB SERVICE component should verify this status. 
Note: The exact meaning of this value may be different depend on the buyer’s card issuer, merchant’s account bank or the step at which authentication failed, which means that we are unable to provide a list of all possible descriptions, in order to receive user- friendly description of the event please use ResponseDescription field. 
Please refer to FAQ Document for Troubleshooting guide on EPG errors. 
ResponseDescription 
 
  	User-friendly description of the error in ResponseCode. 
Note: This field can only be used to display the error description and should not be used to check if transaction was successful, to check if transaction was successful please use ResponseCode field. 
ResponseClass 
 
 
  	This field serves a similar purpose as ResponseCode, but instead of giving a very detailed error it specifies at which stage or part of the system request failed (for example it may point out that issuer declined request or acquire did not accept it or Payment Gateway rejected it, without going into detail) 
ResponseClassDescription 	This is a user-friendly description of ResponseClass 
TransactionID 
 	Unique ID for in progress transaction (Returned in response for every call) 
RecurrenceID	Recurrence ID used for subsequent recurrence transaction.
PaymentPortal
  	Link to Common Payment Page where payer will be asked to input Credit Card information for secure transaction (Returned only in response for Registration call) 
ApprovalCode 
 	This is the response coming from respective bank for Authorization (Returned only in response forAuthorization & Finalizationcalls) 
OrderID 
 
  	OrderID as provided by Customer or if Customer chose automated OrderID generation in Registration or Authorization call then the generated value (Returned only in response forAuthorization & Finalization calls) 
Balance 
 	This is the response coming from respective bank for Authorization (Returned only in response for Capture & Reversal calls) 
 AccountExpiry	Expiry date of Credit Card. This value will be send only while registering a Credit Card for Recurring Payments. It will be send in DateTime format	
Details/Type	Below are the list of Operation name.
Name                           Value
Registration	              V
PaymentPage Query	V
Payment Data Update	V
Authentication Update	V
CC Fraud	               F
CC Authorization	A
CC Reversal	               R
CC Capture	               S
CB Authorization	 A
CB Capture	                S
CB Reversal	                R	
Details/Status	Below are the list of Status which can be associated for any operation mentioned above.
Name               Value
Pending	p
Accepted	A
Not Accepted	F
Reverting	r
Reverted	R
Rejected	U
Waiting	              w
Canceled	C
Timeout	T
Invalid	               I	
Payer/CardBrand or AccountBrand	Below are the list of Card Brand and its symbols
Name           Symbol
Visa                   V
MasterCard     M
AMEX                A
CentralBank
UNB Account
ADCB Account	
 
##	Configuration And Test Accounts

###	Server Configuration.
Server	URL	Port
Production	https://ipg.comtrust.ae
2443
Sandbox	https://demo-ipg.comtrust.ae
2443

Connectivity with EPG should be done using TLSv1.2 , Also note that Store and Terminal  must be configurable for all above call, based on production set up We may or may not provide store and terminal values.
Please contact Merchant Integration Support Merchant-Integration@etisalat.ae to provision and share below configuration.
a)	Customer
b)	Store
c)	Terminal

###	Test Credit Cards for Sandbox environment
Visa Card	Master Card	Error Code	Error Description
4111111111111111	5555555555554444	0	Success
4012888888881881	 	51	Not Sufficient Fund
 	5105105105105100	5	Do not honor
Other Valid Visa Cards	Other Valid Master Cards	91	Issuer Switch inoperative

###	Client Certificate for Sandbox environment

Download Demo Merchant Certificate using below link. Password is “Comtrust”
https://demo-ipg.comtrust.ae/merchant/Downloads/Certificates.zip

