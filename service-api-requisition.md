# service-api-requisition api-listing 

This markdown file contains all api document Order-wise how does flow works of service-api-requisition

	baseProcurementUrl:
		http://localhost:7050/api

# API Reference
The service-api-requisition is organized around REST. Our API has predictable resource-oriented URLs, accepts form-encoded request bodies, returns JSON-encoded responses, and uses standard HTTP response codes, authentication, and verbs.

## Errors

Service Api Requisition uses conventional HTTP response codes to indicate the success or failure of an API request. In general: Codes in the ```2xx``` range indicate success. Codes in the ```4xx``` range indicate an error that failed given the information provided (e.g., a required parameter was omitted, a charge failed, etc.). Codes in the ```5xx``` range indicate an error with Stripe's servers (these are rare).

Some ```4xx``` errors that could be handled programmatically (e.g., a card is declined) include an error code that briefly explains the error reported.

 # HTTPS STATUS CODE SUMMRY

Code   | Summary
------------- | -------------
200 - OK  | Everything worked as expected.
400 - Bad Request  | The request was unacceptable, often due to missing a required parameter.
401 - Unauthorized | No valid API key provided.
402 - Request Failed | The parameters were valid but the request failed.
403 - Forbidden | The API key doesn't have permissions to perform the request.
404 - Not Found | The requested resource doesn't exist.
409 - Conflict | The request conflicts with another request (perhaps due to using the same idempotent key)
429 - Too Many Requests | Too many requests hit the API too quickly. We recommend an exponential backoff of your requests.
500, 502, 503, 504 - Server Errors | Something went wrong on Stripe's end. (These are rare.)
800 - NEGATIVE_ID_NOT_ALLOWED | Negative id not allowed 
801 - ID_NOT_FOUND | Id not found
803 - DATA_NOT_FOUND | Data not found
804 - JSON_MAPPING_EXCEPTION | JSON mapping exception
805 - JSON_PROCESSING_EXCEPTION | JSON processing exception
806 - JSON_EXCEPTION | JSON exception 
807 - IO_EXCEPTION | IO exception
808 - PARSE_EXCEPTION | Parse exception
8020 - NO_APPROVAL_RIGHTS | Current role cannot approve

## Attributes

**type** *string*

The type of error returned. One of api_error, card_error, idempotency_error, or invalid_request_error

**code** *string*

For some errors that could be handled programmatically, a short string indicating the error code reported.

**message** *string*

A human-readable message providing more details about the error. For card errors, these messages can be shown to your users.

**param** *string*

If the error is parameter-specific, the parameter related to the error. For example, you can use this to display a message near the correct form field.

 # Error Type and Description

Error Type   | Description
------------- | -------------
api_error |	API errors cover any other type of problem (e.g., a temporary problem with Stripe's servers), and are extremely uncommon.
card_error |	Card errors are the most common type of error you should expect to handle. They result when the user enters a card that can't be charged for some reason.
idempotency_error |	Idempotency errors occur when an Idempotency-Key | is re-used on a request that does not match the first request's API endpoint and parameters.
invalid_request_error |	Invalid request errors arise when your request has invalid parameters.

## **Requisitions-Entity**
    Requisitions{
    	/*requisitionNo : Unique number for each requistion*/
    	requisitionNo String
    	/*createdOn : request date*/
    	createdOn Instant
    	/*createdBy : requested by*/
    	createdBy String
    	/*updatedOn : request update date*/
    	updatedOn Instant
    	/*updatedOn : request update by*/
    	updatedBy String
    	status String
    	/**
    	*progressStage : ReadyToBuy, POGenerated, SendToVendors, 
    	*QuotationReceived,QuotationApproved,InvoiceGenerated,
    	*PaymentCompleted
    	**/
    	progressStage String
    	financialYear Integer
    	/*type : purchase*/
    	type String
    	totalPrice Integer 
    	notes String maxlength(5000)
    	dueDate LocalDate
 	}

## **Requisitions**
    End Points
        POST /requisitions
        PUT  /requisitions
        POST /requisitions/approve
        GET  /requisitions
        GET  /requisitions/:id
        DELETE /requisitions/:id
		POST /requisitions/sendToVendor

**/requisitions**

Api to create requisition.
 
	Method: POST
	  RequestParam:
		  request* MultipartFile requisitionFile
    RequestParam:
		  request* MultipartFile requisitionLineItemFile
    RequestMapping:
      request* String obj
	Response:
		Success	Requisition added successfully response

*CURL*
```
curl --location --request POST 'http://localhost:7050/api/requisitions' \
--form 'requisitionLineItemFile=@"/C:/Users/admin/Downloads/excelsheet1.xlsx"' \
--form 'obj="{
    \"departmentId\":5,
    \"currencyId\":5,
    \"requisitionNo\" : 1,
    \"progressStage\" : \"test\",
    \"financialYear\": 2021,
     \"dueDate\":\"2009-09-30\",
    \"status\":\"Draft\",
    \"totalPrice\": 2000,
    \"notes\":\"ddatatata\"
}"'
```
**/requisitions**

Api to update requisition.
 
	Method: PUT
	  RequestParam:
		  request* MultipartFile requisitionFile
    RequestParam:
		  request* MultipartFile requisitionLineItemFile
    RequestMapping:
      request* String obj
	Response:
		Success	Requisition update successfully response

*CURL*
```
curl --location --request PUT 'http://localhost:7050/api/requisitions' \
--form 'obj="{
     \"id\":\"1251\",
    \"status\":\"njh\",
     \"roleName\":\"General_Director\",
    \"totalPrice\": 3000,
    \"notes\":\"ddatatataaaaaaaaaaa\",
   \"requisitionLineItemLists\":[{
   \"id\":6,
    \"orderQuantity\":2,
    \"ratePerItem\":15000,
    \"itemDescription\":\"laptop\",
    \"price\":12000,
    \"dueDate\":\"2021-07-21\"
        }]
}"' \
--form 'requisitionLineItemFile=@"/C:/Users/admin/Downloads/excelsheet1.xlsx"'
```
**/requisitions**

Api to search requisition.
 
	Method: GET
	  RequestParam:
		  request* Map<String, String> requestObj
	Response:
		Success	Requisition search completed. Total records 

*CURL*
```
curl --location --request GET 'http://localhost:7050/api/requisitions?status=DRAFT'
```
**/requisitions/{id}**

Api to deactivated requisition.
 
	Method: DELETE
	  PathVariable:
		  request* Long id
	Response:
		Success	Requsitions deactive successfully response

*CURL*
```
curl --location --request DELETE 'http://localhost:7050/api/requisitions/2101'
```
**/requisitions/{id}**

Api to get requisition by id.
 
	Method: GET
	  PathVariable:
		  request* Long id
	Response:
		Success	Requsitions get records successfully response

*CURL*
```
curl --location --request GET 'http://localhost:7050/api/requisitions/2101'
```
**/requisitions/sendToVendor**

Api to requisitions send to vendor.
 
	Method: POST
	  RequestBody:
		  request* List<ObjectNode> list
	Response:
		Success	Assigning requisitions to vendors  
    successfully response

*CURL*
```
curl --location --request POST 'http://localhost:7050/api/requisitions/sendToVendor' \
--header 'Content-Type: application/json' \
--data-raw '[
{
    "stages":"mohit",
    "notes":"hello",
    "status":"ACTIVE",
    "vendorId"  :"1",
    "requisitionId":"2101"

}
]'
```
**/requisitions/approve**

Api to requisition approve.
 
	Method: POST
	  RequestBody:
		  request* List<ObjectNode> list
	Response:
		Success	Request to approve a requsition  
    successfully response

*CURL*
```
curl --location --request POST 'http://localhost:7050/api/requisitions/approve' \
--header 'Content-Type: application/json' \
--data-raw '{
    "requisitionId":"2101",
    "role":"ROLE_ADMIN"
}'
```
## **RequisitionLineItem-Entity**
    RequisitionLineItem{
    	createdOn Instant
    	createdBy String
    	updatedOn Instant
    	updatedBy String
    	status String
    	progressStage String
    	itemDescription String
    	orderQuantity Integer
    	price Integer
    	priority String
    	notes String maxlength(5000)
    	dueDate LocalDate
   	    ratePerItem Integer
 	}
## **RequisitionLineItem**
    End Points
        POST /requisitionLineItem
        PUT  /requisitionLineItem
        GET  /requisitionLineItem
        GET  /requisitionLineItem/:id
        DELETE /requisitionLineItem/:id

**/requisitionLineItem**

Api to create requisition line item.
 
	Method: POST
	  RequestParam:
		  request* MultipartFile requisitionFile
    RequestParam:
      request* String obj
	Response:
		Success	Requisition line item added successfully response

*CURL*
```
curl --location --request POST 'http://localhost:7050/api/requisitionLineItem' \
--header 'Content-Type: application/json' \
--header 'Accept: application/json' \
--header 'Authorization: Basic PEJhc2ljIEF1dGggVXNlcm5hbWU+OjxCYXNpYyBBdXRoIFBhc3N3b3JkPg==' \
--data-raw '{
  "requisitionId": 89168209,
  "status": "magna esse",
  "progressStage": "sint exercitation nulla",
  "itemDescription": "eu ut",
  "orderQuantity": 39604945,
  "price": 42579461,
  "priority": "irure dolore deserunt",
  "notes": "amet velit ullamco id",
  "dueDate": "1954-01-26",
  "ratePerItem": 77773456,
  "type": "non Duis",
  "requisitionLineItemFile": [
    {
      "id": 24319778
    },
    {
      "id": 19882842
    }
  ]
}'
```
**/requisitionLineItem**

Api to update requisition line item.
 
	Method: PUT
	  RequestParam:
		  request* MultipartFile requisitionFile
    RequestParam:
      request* String obj
	Response:
		Success	Requisition line item update successfully response

*CURL*
```
curl --location --request POST 'http://localhost:7050/api/requisitionLineItem' \
--header 'Content-Type: application/json' \
--header 'Accept: application/json' \
--header 'Authorization: Basic PEJhc2ljIEF1dGggVXNlcm5hbWU+OjxCYXNpYyBBdXRoIFBhc3N3b3JkPg==' \
--data-raw '{
  "id": 89014587,
  "requisitionId": 89168209,
  "status": "ACTIVE",
  "progressStage": "sint exercitation nulla",
  "itemDescription": "eu ut",
  "orderQuantity": 39604945,
  "price": 42579461,
  "priority": "irure dolore deserunt",
  "notes": "amet velit ullamco id",
  "dueDate": "1954-01-26",
  "ratePerItem": 77773456,
  "type": "non Duis",
  "requisitionLineItemFile": [
    {
      "id": 24319778
    },
    {
      "id": 19882842
    }
  ]
}'
```
**/requisitionLineItem**

Api to search requisition line item.
 
	Method: GET
	  RequestParam:
		  request* Map<String, String> requestObj
	Response:
		Success	Requisition line item search completed. Total records 

*CURL*
```
curl --location --request GET 'http://localhost:7050/api/requisitionLineItem?status=ACTIVE'
```
**/requisitionLineItem/{id}**

Api to deactivated requisition line item.
 
	Method: DELETE
	  PathVariable:
		  request* Long id
	Response:
		Success	Requsitions line item deactive successfully response

*CURL*
```
curl --location --request DELETE 'http://localhost:7050/api/requisitionLineItem/89014587'
```
**/requisitionLineItem/{id}**

Api to get requisition line item by id.
 
	Method: GET
	  PathVariable:
		  request* Long id
	Response:
		Success	Requsitions line item get records successfully response

*CURL*
```
curl --location --request GET 'http://localhost:7050/api/requisitionLineItem/89014587
```
## **Committee-Entity**
    Committee{
    	name String
    	type String
    	status String
    	createdOn Instant
    	createdBy String
    	updatedOn Instant
    	updatedBy String
    	notes String maxlength(5000)
 	}
## **Committee**
    End Points
        POST /committee
        PUT  /committee
        GET  /committee
        GET  /committee/:id
        DELETE /committee/:id

**/committee**

Api to create committee.
 
	Method: POST
	  RequestBody:
		  request* ObjectNode obj
	Response:
		Success	Committee added successfully response

*CURL*
```
curl --location --request POST 'http://localhost:7050/api/committee' \
--header 'Content-Type: application/json' \
--data-raw '{
    "name":"raaj",
    "type":"cps",
    "notes":"Aws",
    "status":"ACTIVE"
}'
```
**/committee**

Api to update committee.
 
	Method: PUT
	  RequestBody:
		  request* ObjectNode obj
	Response:
		Success	Committee update successfully response

*CURL*
```
curl --location --request PUT 'http://localhost:7050/api/committee' \
--header 'Content-Type: application/json' \
--data-raw '{
    "name":"raaj sharmaaaa",
    "type":"cpsaaaa",
    "notes":"CPGaaaaa",
    "id"  :"1702"
}'
```
**/committee**

Api to search committee.
 
	Method: GET
	  RequestParam:
		  request* Map<String, String> requestObj
	Response:
		Success	Committee search completed. Total records 

*CURL*
```
curl --location --request GET 'http://localhost:7050/api/committee?id=2002'
```
**/committee/{id}**

Api to deactivated committee.
 
	Method: DELETE
	  PathVariable:
		  request* Long id
	Response:
		Success	Committee deactive successfully response

*CURL*
```
curl --location --request DELETE 'http://localhost:7050/api/committee/2002'
```
**/committee/{id}**

Api to get committee by id.
 
	Method: GET
	  PathVariable:
		  request* Long id
	Response:
		Success	Committee get records successfully response

*CURL*
```
curl --location --request GET 'http://localhost:7050/api/committee/2002'
```
## **CommitteeMembers-Entity**
    CommitteeMembers{
    	name String
    	company String
    	department String
    	phoneNumber String
    	email String
    	status String
    	designation String
    	createdOn Instant
    	createdBy String
    	updatedOn Instant
    	updatedBy String
 	}

## **CommitteeMembers**
    End Points
        POST /committeeMembers
        PUT  /committeeMembers
        GET  /committeeMembers
        GET  /committeeMembers/:id
        DELETE /committeeMembers/:id

**/committeeMembers**

Api to create committee members.
 
	Method: POST
	  RequestParam:
      request* MultipartFile file
    RequestParam:
      request* String obj
	Response:
		Success	committeeMembers added successfully response

*CURL*
```
curl --location --request POST 'http://localhost:7050/api/committeeMembers' \
--form 'obj="{
    \"committeeId\":2002,
    \"name\":\"raaj\",
    \"company\":\"abc\",
    \"department\":\"cfd\",
    \"designation\":\"dfdsfsd\",
    \"phoneNumber\":5465132,
    \"email\":\"dgd@gma.con\",
    \"user\":\"dsgfd\",
\"status\":\"active\"
}"'
```
**/committeeMembers**

Api to update committee members.
 
	Method: PUT
	  RequestParam:
      request* MultipartFile file
    RequestParam:
      request* String obj
	Response:
		Success	committeeMembers update successfully response

*CURL*
```
curl --location --request PUT 'http://localhost:7050/api/committeeMembers' \
--form 'obj="{    
      \"id\":2201,
      \"name\":\"raaaaaj\",
    \"company\":\"abcaaaaaaa\",
    \"department\":\"cfdaaaaa\",
    \"designation\":\"dfdsfsdaaaaaa\",
    \"phoneNumber\":12345678910,
    \"email\":\"dgd@gma.aaaaacon\",
    \"user\":\"dsgaaaaaaaafd\",
\"status\":\"active\"
}"' \
--form 'file=@"/C:/Users/admin/Pictures/bbokstrap3.jpg"'
```
**/committeeMembers**

Api to search committee members.
 
	Method: GET
	  RequestParam:
		  request* Map<String, String> requestObj
	Response:
		Success	committeeMembers search completed. Total records 

*CURL*
```
curl --location --request GET 'http://localhost:7050/api/committeeMembers?name=raaj' \
--data-raw ''
```
**/committeeMembers/{id}**

Api to deactivated committee members.
 
	Method: DELETE
	  PathVariable:
		  request* Long id
	Response:
		Success	committeeMembers deactive successfully response

*CURL*
```
curl --location --request DELETE 'http://localhost:7050/api/committee/2201'
```
**/committeeMembers/{id}**

Api to get committee members by id.
 
	Method: GET
	  PathVariable:
		  request* Long id
	Response:
		Success	committeeMembers get records successfully response

*CURL*
```
curl --location --request GET 'http://localhost:7050/api/committeeMembers/2201'
```
## **Currency-Entity**
    Currency{
		code String
		countryName String
		countryCode String
		status String
 	}

## **Currency**
    End Points
        POST /currency
        PUT  /currency
        GET  /currency
        GET  /currency/:id
        DELETE /currency/:id

**/currency**

Api to create currency.
 
	Method: POST
	  RequestBody:
		  request* ObjectNode obj
	Response:
		Success	Currency added successfully response

*CURL*
```
curl --location --request POST 'http://localhost:7050/api/currency' \
--header 'Content-Type: application/json' \
--data-raw '{
    "code":"10111123",
    "countryName":"india",
    "countryCode":"1020201",
    "symbolFilePath":"symbolFilePath"

}'
```
**/currency**

Api to update currency.
 
	Method: PUT
	  RequestBody:
		  request* ObjectNode obj
	Response:
		Success	Currency update successfully response

*CURL*
```
curl --location --request PUT 'http://localhost:7050/api/currency' \
--header 'Content-Type: application/json' \
--data-raw '{
    "code":"10111123",
    "countryName":"indiaaaaaaa",
    "countryCode":"1020201",
    "symbolFilePath":"symbolFilePath",
    "id":"2301"

}'
```
**/currency**

Api to search currency.
 
	Method: GET
	  RequestParam:
		  request* Map<String, String> requestObj
	Response:
		Success	Currency search completed. Total records 

*CURL*
```
curl --location --request GET 'http://localhost:7050/api/currency?id=2301' \
--data-raw ''
```
**/currency/{id}**

Api to deactivated currency.
 
	Method: DELETE
	  PathVariable:
		  request* Long id
	Response:
		Success	Currency deactive successfully response

*CURL*
```
curl --location --request DELETE 'http://localhost:7050/api/currency/2301' \
--data-raw ''
```
**/currency/{id}**

Api to get currency by id.
 
	Method: GET
	  PathVariable:
		  request* Long id
	Response:
		Success	Currency get records successfully response

*CURL*
```
curl --location --request GET 'http://localhost:7050/api/currency/2301' \
--data-raw ''
```
## **Department-Entity**
    Department{
		name String
	    status String
 	}

## **department**
    End Points
        POST /department
        PUT  /department
        GET  /department
        GET  /department/:id
        DELETE /department/:id

**/department**

Api to create department.
 
	Method: POST
	  RequestBody:
		  request* ObjectNode obj
	Response:
		Success	Department added successfully response

*CURL*
```
curl --location --request POST 'http://localhost:7050/api/department' \
--header 'Content-Type: application/json' \
--data-raw '{
 "name":"HR"
}'
```
**/department**

Api to update department.
 
	Method: PUT
	  RequestBody:
		  request* ObjectNode obj
	Response:
		Success	Department update successfully response

*CURL*
```
curl --location --request PUT 'http://localhost:7050/api/department' \
--header 'Content-Type: application/json' \
--data-raw '{
 "name":"HR department",
  "id":"2351"
}'
```
**/department**

Api to search department.
 
	Method: GET
	  RequestParam:
		  request* Map<String, String> requestObj
	Response:
		Success	Department search completed. Total records 

*CURL*
```
[
    {
        "id": 2351,
        "name": "HR department",
        "status": ACTIVE
    }
]
```
**/department/{id}**

Api to deactivated department.
 
	Method: DELETE
	  PathVariable:
		  request* Long id
	Response:
		Success	Department deactive successfully response

*CURL*
```
curl --location --request DELETE 'http://localhost:7050/api/department/2351'
```
**/department/{id}**

Api to get department by id.
 
	Method: GET
	  PathVariable:
		  request* Long id
	Response:
		Success	Department get records successfully response

*CURL*
```
curl --location --request GET 'http://localhost:7050/api/department/2351'
```
## **Invoice-Entity**
    invoice{
		invoiceNo String
		amount Integer
		modeOfPayment String
		txnRefNo String
		chequeOrDdNo String
		issuerBank String
		status String
		createdOn Instant
		createdBy String
		updatedOn Instant
		updatedBy String
		dueDate LocalDate
		notes String maxlength(5000)
 	}

## **Invoice**
    End Points
        POST /invoice
        PUT  /invoice
        GET  /invoice
        GET  /invoice/:id
        DELETE /invoice/:id

**/invoice**

Api to create invoice.
 
	Method: POST
	  RequestBody:
		  request* ObjectNode obj
	Response:
		Success	Invoice added successfully response

*CURL*
```
curl --location --request POST 'http://localhost:7050/api/invoice' \
--header 'Content-Type: application/json' \
--data-raw ' {
    "documentId":"1",
    "quotationId":9,
    "invoiceNo":"120000",
    "amount":"yahoo.com",
    "modeOfPayment":"modeOfPayment",
    "txnRefNo":"12345",
    "issuerBank":"issuerBank",
    "chequeOrDdNo":"chequeOrDdNo",
    "notes":"notes"
 }'
```
**/invoice**

Api to update invoice.
 
	Method: PUT
	  RequestBody:
		  request* ObjectNode obj
	Response:
		Success	Invoice update successfully response

*CURL*
```
curl --location --request PUT 'http://localhost:7050/api/invoice' \
--header 'Content-Type: application/json' \
--data-raw ' {
     "id":"2401",
    "documentId":"1",
    "quotationId":9,
    "invoiceNo":"1",
    "amount":"1000",
    "modeOfPayment":"modeOfPayment",
    "txnRefNo":"txnRefNo",
    "issuerBank":"issuerBank",
    "chequeOrDdNo":"chequeOrDdNo",
    "notes":"notes"
 }'
```
**/invoice**

Api to search invoice.
 
	Method: GET
	  RequestParam:
		  request* Map<String, String> requestObj
	Response:
		Success	Invoice search completed. Total records 

*CURL*
```
curl --location --request GET 'http://localhost:7050/api/invoice?invoiceNo=1'
```
**/invoice/{id}**

Api to deactivated invoice.
 
	Method: DELETE
	  PathVariable:
		  request* Long id
	Response:
		Success	Invoice deactive successfully response

*CURL*
```
curl --location --request DELETE 'http://localhost:7050/api/invoice/2401'
```
**/invoice/{id}**

Api to get invoice by id.
 
	Method: GET
	  PathVariable:
		  request* Long id
	Response:
		Success	Invoice get records successfully response

*CURL*
```
curl --location --request GET 'http://localhost:7050/api/invoice/2401'
```
## **PurchaseOrder-Entity**
    purchaseOrder{
		poNo String
    	status String
    	createdOn Instant
    	createdBy String
    	updatedOn Instant
    	updatedBy String
   	    dueDate LocalDate
    	termsAndConditions String maxlength(5000)
    	notes String maxlength(5000)
 	}

## **PurchaseOrder**
    End Points
        POST /purchaseOrder
        PUT  /purchaseOrder
        GET  /purchaseOrder
        GET  /purchaseOrder/:id
        DELETE /purchaseOrder/:id
		POST /purchaseOrder/approve

**/purchaseOrder**

Api to create purchase order.
 
	Method: POST
	  RequestBody:
		  request* ObjectNode obj
	Response:
		Success	purchaseOrder added successfully response

*CURL*
```
curl --location --request POST 'http://localhost:7050/api/purchaseOrder' \
--header 'Content-Type: application/json' \
--data-raw '{
    "poNo":"93515233780",
    "termsAndConditions":"aaaa",
    "notes":"Aws cloued",
    "requisitionId":1
}'
```
**/purchaseOrder**

Api to update purchase order.
 
	Method: PUT
	  RequestBody:
		  request* ObjectNode obj
	Response:
		Success	purchaseOrder update successfully response

*CURL*
```
curl --location --request PUT 'http://localhost:7050/api/purchaseOrder' \
--header 'Content-Type: application/json' \
--data-raw '{
    "id":2501,
    "poNo":"125465463450",
    "termsAndConditions":"come on",
    "notes":"Aws cloued"
}'
```
**/purchaseOrder**

Api to search purchase order.
 
	Method: GET
	  RequestParam:
		  request* Map<String, String> requestObj
	Response:
		Success	purchaseOrder search completed. Total records 

*CURL*
```
curl --location --request GET 'http://localhost:7050/api/purchaseOrder?poNo=125465463450'
```
**/purchaseOrder/{id}**

Api to deactivated purchase order.
 
	Method: DELETE
	  PathVariable:
		  request* Long id
	Response:
		Success	purchaseOrder deactive successfully response

*CURL*
```
curl --location --request DELETE 'http://localhost:7050/api/purchaseOrder/2501'
```
**/purchaseOrder/{id}**

Api to get purchase order by id.
 
	Method: GET
	  PathVariable:
		  request* Long id
	Response:
		Success	purchaseOrder get records successfully response

*CURL*
```
curl --location --request GET 'http://localhost:7050/api/purchaseOrder/2501'
```
**/purchaseOrder/approve**

Api to approve purchase order.
 
	Method: POST
	  RequestBody:
		  request* ObjectNode obj
	Response:
		Success	Request to approve a purchase order  
    successfully response

*CURL*
```
curl --location --request POST 'http://localhost:7050/api/purchaseOrder/approve' \
--header 'Content-Type: application/json' \
--data-raw '{
    "purchaseOrderId":2501,
    "roleName":"CSS AI"
}'
```
## **Quotation-Entity**
    Quotation{
		quotationNo String
    	status String
    	createdOn Instant
    	createdBy String
    	updatedOn Instant
    	updatedBy String
    	dueDate LocalDate
    	notes String maxlength(5000)
 	}

## **Quotation**
    End Points
        POST /quotation
        PUT  /quotation
        GET  /quotation
        GET  /quotation/:id
        DELETE /quotation/:id

**/quotation**

Api to create quotation.
 
	Method: POST
	  RequestBody:
		  request* ObjectNode obj
	Response:
		Success	Quotation added successfully response

*CURL*
```
curl --location --request POST 'http://localhost:7050/api/quotation' \
--header 'Content-Type: application/json' \
--data-raw '{
"documentId": "1608",
"vendorId":"2551",
"purchaseOrderId":"1",
"quotationNo":"12",
"notes":"yah",
"status":"i am here"
}'
```
**/quotation**

Api to update quotation.
 
	Method: PUT
	  RequestBody:
		  request* ObjectNode obj
	Response:
		Success	Quotation update successfully response

*CURL*
```
curl --location --request POST 'http://localhost:7050/api/quotation' \
--header 'Content-Type: application/json' \
--data-raw '{
"id":"2601",
"documentId": "1608",
"vendorId":"2551",
"purchaseOrderId":"1",
"quotationNo":"12",
"notes":"notes",
"status":"Active"
}'
```
**/quotation**

Api to search quotation.
 
	Method: GET
	  RequestParam:
		  request* Map<String, String> requestObj
	Response:
		Success	Quotation search completed. Total records 

*CURL*
```
curl --location --request GET 'http://localhost:7050/api/quotation?status=Active'
```
**/quotation/{id}**

Api to get quotation by id.
 
	Method: GET
	  PathVariable:
		  request* Long id
	Response:
		Success	Quotation get records successfully response

*CURL*
```
curl --location --request GET 'http://localhost:7050/api/quotation/2601'
```
## **Vendor-Entity**
    Vendor{
		firstName String
		middleName String
		lastName String
		phoneNumber String
		email String
		address String
		zipCode String
		status String
		createdOn Instant
		createdBy String
		updatedOn Instant
		updatedBy String
 	}

## **Vendor**
    End Points
        POST /vendor
        PUT  /vendor
        GET  /vendor
        GET  /vendor/:id
        DELETE /vendor/:id

**/vendor**

Api to create vendor.
 
	Method: POST
	  RequestBody:
		  request* ObjectNode obj
	Response:
		Success	Vendor added successfully response

*CURL*
```
curl --location --request POST 'http://localhost:7050/api/vendor' \
--header 'Content-Type: application/json' \
--data-raw '{
    "firstName":"vishnu ",
    "middleName":"kumar",
    "lastName": "sharma",
    "phoneNumber":"9067658909",
    "email":"email@mail.com",
    "address":"address",
    "zipCode":"123421",
      "status":"ACTIVE"
}'
```
**/vendor**

Api to update vendor.
 
	Method: PUT
	  RequestBody:
		  request* ObjectNode obj
	Response:
		Success	Vendor update successfully response

*CURL*
```
curl --location --request PUT 'http://localhost:7050/api/vendor' \
--header 'Content-Type: application/json' \
--data-raw '{
	"id":"2551",
    "firstName":"firstName",
    "middleName":"middleName",
    "lastName": "lastName",
    "phoneNumber":"9067658909",
    "email":"email@mail.com",
    "address":"address",
    "zipCode":"123421",
    "status":"ACTIVE"
}'
```
**/vendor**

Api to search vendor.
 
	Method: GET
	  RequestParam:
		  request* Map<String, String> requestObj
	Response:
		Success	Vendor search completed. Total records 

*CURL*
```
curl --location --request GET 'http://localhost:7050/api/vendor?status=ACTIVE'
```
**/vendor/{id}**

Api to deactivated vendor.
 
	Method: DELETE
	  PathVariable:
		  request* Long id
	Response:
		Success	Vendor deactive successfully response

*CURL*
```
curl --location --request DELETE 'http://localhost:7050/api/vendor/2551'
```
**/vendor/{id}**

Api to get vendor by id.
 
	Method: GET
	  PathVariable:
		  request* Long id
	Response:
		Success	Vendor get records successfully response

*CURL*
```
curl --location --request GET 'http://localhost:7050/api/vendor/2551'
```
## **Buyer-Entity**
    buyer{
		firstName String
		middleName String
		lastName String
		phoneNumber String
		email String
		address String
		zipCode String
		status String
		createdOn Instant
		createdBy String
		updatedOn Instant
		updatedBy String
 	}

## **Buyer**
    End Points
        POST /buyer
        PUT  /buyer
        GET  /buyer
        GET  /buyer/:id
        DELETE /buyer/:id

**/buyer**

Api to create buyer.
 
	Method: POST
	  RequestParam:
		  request* MultipartFile file
    RequestMapping:
      request* String obj
	Response:
		Success	Buyer added successfully response

*CURL*
```
curl --location --request POST 'http://localhost:7050/api/buyer' \
--form 'obj="{
    \"firstName\":\"mohit\",
    \"middleName\":\"kumar\",
    \"lastName\": \"sharma\",
    \"phoneNumber\":\"9067658909\",
    \"email\":\"email@mail.com\",
    \"address\":\"address\",
    \"zipCode\":\"123421\",
      \"status\":\"ACTIVE\"
}"' \
--form 'file=@"/D:/synectics-xhz0tw-vcm3ft.webp"'
```
**/buyer**

Api to update buyer.
 
	Method: PUT
	  RequestParam:
		  request* MultipartFile file
      request* String obj
	Response:
		Success	Buyer update successfully response

*CURL*
```
curl --location --request PUT 'http://localhost:7050/api/buyer' \
--form 'obj="{
	\"id\":\"2701\",
    \"firstName\":\"mohit\",
    \"middleName\":\"kumar\",
    \"lastName\": \"soni\",
    \"phoneNumber\":\"9067658909\",
    \"email\":\"email@mail.com\",
    \"address\":\"address\",
    \"zipCode\":\"123421\",
      \"status\":\"ACTIVE\"
}"' \
--form 'file=@"/D:/synectics-xhz0tw-vcm3ft.webp"'
```
**/buyer**

Api to search buyer.
 
	Method: GET
	  RequestParam:
		  request* Map<String, String> requestObj
	Response:
		Success	Buyer search completed. Total records 

*CURL*
```
curl --location --request GET 'http://localhost:7050/api/buyer?id=2701'
```
**/buyer/{id}**

Api to deactivated buyer.
 
	Method: DELETE
	  PathVariable:
		  request* Long id
	Response:
		Success	Buyer deactive successfully response

*CURL*
```
curl --location --request DELETE 'http://localhost:7050/api/buyer/2701'
```
**/buyer/{id}**

Api to get buyer by id.
 
	Method: GET
	  PathVariable:
		  request* Long id
	Response:
		Success	Buyer get records successfully response

*CURL*
```
curl --location --request GET 'http://localhost:7050/api/buyer/2701'
```
## **Roles-Entity**
    Roles{
		name String
    	description String
    	status String
   		createdOn Instant
    	createdBy String
    	updatedOn Instant
    	updatedBy String
    	securityserviceId Long
 	}

## **Roles**
    End Points
        POST /roles
        PUT  /roles
        GET  /roles
        GET  /roles/:id
        DELETE /roles/:id

**/roles**

Api to create roles.
 
	RequestBody:
		  request* ObjectNode obj 
	Response:
		Success	Roles added successfully response

*CURL*
```
curl --location --request POST 'http://localhost:7050/api/roles' \
--header 'Content-Type: application/json' \
--data-raw '{
    "name":"General_Director",
    "status":"ACTIVE",
    "description":"Test 2 role",
    "isGroup":"true"
   
}'
```
**/roles**

Api to update roles.
 
	Method: PUT
	  RequestBody:
		  request* ObjectNode obj
	Response:
		Success	Roles update successfully response

*CURL*

```
curl --location --request PUT 'http://localhost:7050/api/roles' \
--header 'Content-Type: application/json' \
--data-raw '{
	"id":"3001",
    "name":"General_Director",
    "status":"ACTIVE",
    "description":"Test 2 role",
    "isGroup":"true"
   
}'
```
**/roles**

Api to search roles.
 
	Method: GET
	  RequestParam:
		  request* Map<String, String> requestObj
	Response:
		Success	Roles search completed. Total records 

*CURL*
```
curl --location --request GET 'http://localhost:7050/api/roles' \
--data-raw ''
```
**/roles/{id}**

Api to deactivated roles.
 
	Method: DELETE
	  PathVariable:
		  request* Long id
	Response:
		Success	Roles deactive successfully response

*CURL*
```
curl --location --request DELETE 'http://localhost:7050/api/roles/3001' \
--data-raw ''
```
**/roles/{id}**

Api to get roles by id.
 
	Method: GET
	  PathVariable:
		  request* Long id
	Response:
		Success	Roles get records successfully response

*CURL*
```
curl --location --request GET 'http://localhost:7050/api/roles/3001' \
--data-raw ''
```
## **Rules-Entity**
    Rules{
		name String
		description String maxlength(5000)
		status String
		rule String maxlength(10000)
		createdOn Instant
		createdBy String
		updatedOn Instant
		updatedBy String
 	}

## **Rules**
    End Points
        POST /rules
        PUT  /rules
        GET  /rules
        GET  /rules/:id
        DELETE /rules/:id

**/rules**

Api to create rules.
 
	Method: POST
	  RequestBody:
		  request* ObjectNode obj
	Response:
		Success	Rules added successfully response

*CURL*
```
curl --location --request POST 'http://localhost:7050/api/rules' \
--header 'Content-Type: application/json' \
--data-raw '{
   "roleId":"1001",
    "name":"REQUISITION_TYPE",
    "status":"ACTIVE",
    "description":"requisition type rule",
    "rule":{"nonstandard":{"min":"200","max":"4000"},"standard":{"min":"6000"}}

}
'
```
**/rules**

Api to update rules.
 
	Method: PUT
	  RequestBody:
		  request* ObjectNode obj
	Response:
		Success	Rules update successfully response

*CURL*
```
curl --location --request POST 'http://localhost:7050/api/rules' \
--header 'Content-Type: application/json' \
--data-raw '{
   "roleId":"1001",
    "name":"REQUISITION_TYPE",
    "status":"ACTIVE",
    "description":"requisition type rule",
    "rule":{"nonstandard":{"min":"200","max":"4000"},"standard":{"min":"6000"}}

}
'
```
**/rules**

Api to search rules.
 
	Method: GET
	  RequestParam:
		  request* Map<String, String> requestObj
	Response:
		Success	Rules search completed. Total records 

*CURL*
```
curl --location --request GET 'http://localhost:7050/api/rules' \
--data-raw ''
```
**/rules/{id}**

Api to deactivated rules.
 
	Method: DELETE
	  PathVariable:
		  request* Long id
	Response:
		Success	Rules deactive successfully response

*CURL*
```
curl --location --request DELETE 'http://localhost:7050/api/rules/1104'
```
**/rules/{id}**

Api to get rules by id.
 
	Method: GET
	  PathVariable:
		  request* Long id
	Response:
		Success	Rules get records successfully response

*CURL*
```
curl --location --request GET 'http://localhost:7050/api/rules/1104'
```
## **ApprovalRules-Entity**
    ApprovalRules{
		role String
    	minLimit Integer
    	maxLimit Integer
    	createdOn Instant
    	createdBy String
    	updatedOn Instant
    	updatedBy String
    	status String 
 	}

## **ApprovalRules**
    End Points
        POST /approvalRules
        PUT  /approvalRules
        GET  /approvalRules
        GET  /approvalRules/:id
        DELETE /approvalRules/:id

**/approvalRules**

Api to create approval rules.
 
	Method: POST
	  RequestBody:
		  request* ObjectNode obj
	Response:
		Success	approvalRules added successfully response

*CURL*
```
curl --location --request POST 'http://localhost:7050/api/approvalRules' \
--header 'Content-Type: application/json' \
--data-raw '{
    "role":"GENERAL_DIRECTOR_GRP",
    "minLimit":0,
    "maxLimit":50000

}'
```
**/approvalRules**

Api to update approvalRules.
 
	Method: PUT
	  RequestBody:
		  request* ObjectNode obj
	Response:
		Success	approvalRules update successfully response

*CURL*
```
curl --location --request POST 'http://localhost:7050/api/approvalRules' \
--header 'Content-Type: application/json' \
--data-raw '{
	"id":"5001",
    "role":"GENERAL_DIRECTOR_GRP",
    "minLimit":0,
    "maxLimit":20000

}'
```
**/approvalRules**

Api to search approvalRules.
 
	Method: GET
	  RequestParam:
		  request* Map<String, String> requestObj
	Response:
		Success	approvalRules search completed. Total records 

*CURL*
```
curl --location --request GET 'http://localhost:7050/api/approvalRules' \
--data-raw ''
```
**/approvalRules/{id}**

Api to deactivated approvalRules.
 
	Method: DELETE
	  PathVariable:
		  request* Long id
	Response:
		Success	approvalRules deactive successfully response

*CURL*
```
curl --location --request DELETE 'http://localhost:7050/api/approvalRules/5001'
```
**/approvalRules/{id}**

Api to get approvalRules by id.
 
	Method: GET
	  PathVariable:
		  request* Long id
	Response:
		Success	approvalRules get records successfully response

*CURL*
```
curl --location --request GET 'http://localhost:7050/api/approvalRules/5001'
```

### Who do I talk to? ###
	Please mail us on
	info@syenctiks.com
Footer
© 2022 GitHub, Inc.
Footer navigation
Terms
Privacy
Security
Status
Docs
Contact GitHub
Pricing
API
Training
Blog
About






