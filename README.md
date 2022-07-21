# paycruiser-api-doc
API Documentation for PayCruiser. To signup with PayCruiser, please go to [https://www.paycruiser.com/sign-up](https://www.paycruiser.com/sign-up)

Base URLS:
  1. Sandbox: `https://sandbox-api.paycruiser.com`
  2. Live: `please contact us`

# 1. Login

User provides their cellphone number to receive onetime passcode 

## 1.a Request One Time Passcode
### Request
```
curl --request POST \
  --url https://sandbox-api.paycruiser.com/auth/mobile/ \
  --header 'Content-Type: application/json' \
  --data '{"mobile":"<YOUR_MOBILE_NUMBER>"}'
```

### Response

```
{
  "detail": "We sent you a 6-digit verification code via text. Please use that code to continue"
}
```


## 1.b Token Authentications
User inputs one time passcode to receive long lived API token to store for subsequent requests

### Request
```
curl --request POST \
  --url https://sandbox-api.paycruiser.com/callback/auth/ \
  --header 'Content-Type: application/json' \
  --data '{"token":"123456"}'
```

### Response

```
{
  "token": "<YOUR_TOKEN>"
}
```

# 2. User (Merchant) Information
This endpoint returns user information(which will give you the merchant_id information)

### Request

The header token is the token you received in the Token Authentication section
```
curl --request GET \
  --url https://sandbox-api.paycruiser.com/user/acct-info/ \
  --header 'Authorization: Token <YOUR_TOKEN>' \
  --header 'Content-Type: application/json'
```

### Response

The merchant_id is what you will need in order to make request to get user transactions list. 
The rest of the information is irrelevant for the card reader

```
{
  "id": 1113,
  "first_name": "John",
  "last_name": "Doe ",
  "business_name": "Paycruiser Test",
  "domain_url": null,
  "merchant_id": "PAYCRUISER-TEST",
  "user_ethereum_handle": "",
  "address": "56 WTC",
  "city": "Los Angeles",
  "is_active": true,
  "zip_code": "55667",
  "state": "CA",
  "country": "USA",
  "id_verification_ref": "inq_ZhZjAtgT8f4Q8oSCFLqhRK2D",
  "user_ip_address": "71.195.41.42",
  "government_id_verified": true,
  "business_verified": true,
  "mobile_verified": true,
  "account_admins": [
    "ID: 744, Merchant-ID: ACME-1 ",
    "ID: 806, Merchant-ID: GMT ",
    "ID: 994, Merchant-ID: CONTOSO-PHARMACY "
  ],
  "related_submerchants": [
    "ID: 1187, Merchant-ID: RESIDENCE-AFRICA ",
    "ID: 654, Merchant-ID: T1M"
  ]
}
```

# 3. Make A Transaction

Use this endpoint to submit onetime payments. The merchant account receiving funds is provided in "merchant_account". 

## Test Cards Information
Use these following card numbers to test your sandbox integration. P.S: You cannot run live transactions on sandbox environment.

| Card Brand       | Card Number      | Expiry Date | Amount (in USD) |
|------------------|------------------|-------------|-----------------|
| Visa             | 4111111111111111 | 1229        | 50              |
| MasterCard       | 5500000000000004 | 1229        | 50              |
| American Express | 340000000000009  | 1229        | 50              |
| JCB              | 3566002020140006 | 1229        | 50              |
| Discover         | 6011000000000004 | 1229        | 50              |
| Diners Club      | 36438999960016   | 1229        | 50              |
| China Union Pay  | 6250941006528599 | 1229        | 50              |


## Payload

| Attribute        | Type   | Description                                                                                                                                | Is Required            |
|------------------|--------|--------------------------------------------------------------------------------------------------------------------------------------------|------------------------|
| fullName         | string | name as it appears on the card                                                                                                             | required               |
| email            | string | customer (purchaser) email. Receipts will be sent to this email                                                                            | optional               |
| cardnum          | string | credit card number without spaces or dashes                                                                                                | required               |
| cardtype         | string | credit card type: Visa, Mastercard, Discover, American Express                                                                             | required               |
| cardmonth        | string | card expiration month                                                                                                                      | required               |
| cardyear         | string | card expiration year                                                                                                                       | required               |
| cardcvc          | string | card cvv                                                                                                                                   | required               |
| promocode        | string | Promo code(for discounts). Must be stored/defined in the system.  Please contact support for more details regarding how to use promo codes | optional               |
| product_id       | string | Product ID, if merchant has product on our e-commerce page. If none, please enter 999                                                      | required,  default=999 |
| unit_count       | string | The unit price will be multiplied by the value of unit_count. By default, the value should be 1                                            | required, default=1    |
| unit_price       | string | amount of a unit of transaction. The final amount charged is unit_price * unit_count                                                       | required               |
| zipcode          | string | zip code of the card or customer zip code.                                                                                                 | required               |
| description      | string | Transaction derscription                                                                                                                   | required               |
| memo             | string | Transaction memo. Some clients use this for tracking purposes on Quickbooks                                                                | optional               |
| merchant_account | string | Merchant account to who this transaction will be paid to                                                                                   | required               |
| phone_number     | string | customer phone number to receive transaction receipts to                                                                                   | optional               |
| api_key          | string | merchant's paycruiser API key                                                                                                              | required if not authenticated              |
| tip              | string | dollar value to add to the final amount                                                                                                    | optional               |


### Request for authenticated payments (recommended)
```
curl --request POST \
  --url https://sandbox-api.paycruiser.com/pay/single-payment/ \
  --header 'Authorization: Token <YOUR_TOKEN>
  --data '{
	 "fullName":"Jane Doe",
	 "email":"",
	 "cardnum":"4111111111111111",
	 "cardtype":"visa",
	 "cardmonth":"11",
	 "cardyear":"22",
	 "cardcvc":"123",
	 "promocode":"",
	 "product_id":999,
	 "unit_count":1,
	 "unit_price":"50",
	 "zipcode":"90802",
	 "description":"",
	 "memo":"",
	 "merchant_account":"PAYCRUISER-TEST",
	 "phone_number":"",
	 "tip":""
}'
```

### Request for non authenticated payments (not recommended)
```
curl --request POST \
  --url https://sandbox-api.paycruiser.com/pay/ \
  --data '{
	 "fullName":"Jane Doe",
	 "email":"",
	 "cardnum":"4111111111111111",
	 "cardtype":"visa",
	 "cardmonth":"11",
	 "cardyear":"22",
	 "cardcvc":"123",
	 "promocode":"",
	 "product_id":999,
	 "unit_count":1,
	 "unit_price":"50",
	 "zipcode":"90802",
	 "description":"",
	 "memo":"",
	 "merchant_account":"PAYCRUISER-TEST",
	 "phone_number":"",
	 "tip":""
}'
```

### Response

You should always display payment_status to UI.

```
{
  "data": {
    "fullName": "Ousmane Conde",
    "email": "",
    "cardnum": "4111111111111111",
    "cardtype": "visa",
    "cardmonth": "12",
    "cardyear": "23",
    "cardcvc": "952",
    "promocode": "",
    "currency_code": "usd",
    "product_id": 999,
    "unit_count": 1,
    "unit_price": "1",
    "zipcode": "55116",
    "description": "",
    "memo": "",
    "merchant_account": "PAYCRUISER-SN",
    "phone_number": "++15629992511",
    "tip": ""
  },
  "errors": [],
  "status_code": 250,
  "payment_status": "SUCCESS! Your payment of 1.00 USD was processed",
  "success_message": [
    "SUCCESS! Your payment of 1.00 USD was processed",
    "Your confirmation Number: 171969",
    "1 x 1 USD; Memo: mpos id: 1223; Tip: 0.00 USD"
  ],
  "confirmation_code": "171969",
  "transaction_tag": "4650684582",
  "transaction_id": "ET156323"
}
```

# 4. Get Transactions Report

Return the list of all merchant transactions and their status

## Request
```
curl --request GET \
  --url 'https://sandbox-api.paycruiser.com/merchant/transactions/?merchant_id=PAYCRUISER-TEST' \
  --header 'Authorization: Token <YOUR_TOKEN>'
```

## Response

```html
{
  "count": 437,
  "next": "https://sandbox-api.paycruiser.com/me/transactions/?limit=250&merchant_id=PAYCRUISER-TEST&offset=250",
  "previous": null,
  "results": [
    {
      "id": "91a5a764-d84b-459d-bef6-892245d4cb3b",
      "is_paid_out": false,
      "tag": "6016284342",
      "cardholder_name": "Jane Doe",
      "card_number": "7234 ***",
      "card_type": "MASTERCARD",
      "amount": "$69.46",
      "transaction_type": "Purchase",
      "status": "Approved",
      "auth_no": "09310B",
      "time": "11/28/2020 19:03:09",
      "transaction_datetime": "2020-11-29T03:03:09",
      "ref_num": "PAYCRUISER-TEST",
      "cust_ref_num": "180715",
      "reference_3": null,
      "terminal_name": "PAYCRUISER",
      "processing_fees": "2.306",
      "processing_fees_str": "$ 2.306",
      "payout_amount": "67.154",
      "payout_amount_str": "$ 67.154",
      "payout_status": 2,
      "payout_status_str": "Processed",
      "payout_record": null
    },
    {
      "id": "a8645983-58e3-48fb-8320-ecb7661ad104",
      "is_paid_out": false,
      "tag": "6016040314",
      "cardholder_name": "JOHN DOE",
      "card_number": "7133 ***",
      "card_type": "VISA",
      "amount": "$104.74",
      "transaction_type": "Purchase",
      "status": "Approved",
      "auth_no": "113082",
      "time": "11/28/2020 17:38:08",
      "transaction_datetime": "2020-11-29T01:38:08",
      "ref_num": "PAYCRUISER-TEST",
      "cust_ref_num": "222877",
      "reference_3": null,
      "terminal_name": "PAYCRUISER",
      "processing_fees": "3.223",
      "processing_fees_str": "$ 3.223",
      "payout_amount": "101.517",
      "payout_amount_str": "$ 101.517",
      "payout_status": 2,
      "payout_status_str": "Processed",
      "payout_record": null
    },
   ...
  ]
}
```

# 5. Get A Transaction Detail
Ruturns a single transaction details

## Request

```
curl --request GET \
  --url 'https://sandbox-api.paycruiser.com/merchant/transactions/dd914aea-720c-4ecb-8200-f8aaf46ce103/?merchant_id=<YOUR-MERCHANT-ID>' \
  --header 'Authorization: Token <YOUR_TOKEN>' \
  --header 'Content-Type: application/json'
```

## Response

```
{
  "id": "dd914aea-720c-4ecb-8200-f8aaf46ce103",
  "is_paid_out": false,
  "tag": "5891626177",
  "cardholder_name": "JANE D DOE",
  "card_number": "7234 ***",
  "card_type": "VISA",
  "amount": "$56.23",
  "transaction_type": "Purchase",
  "status": "Approved",
  "auth_no": "134548",
  "time": "10/24/2020 16:44:41",
  "transaction_datetime": "2020-10-24T23:44:41",
  "ref_num": "PAYCRUISER-TEST",
  "cust_ref_num": "190232",
  "reference_3": null,
  "terminal_name": "PAYCRUISER",
  "processing_fees": "1.962",
  "processing_fees_str": "$ 1.962",
  "payout_amount": "54.268",
  "payout_amount_str": "$ 54.268",
  "payout_status": 2,
  "payout_status_str": "Processed",
  "payout_record": null
}
```

# 6. Void Or Refund Transaction
 To process refunds, you need to 
 1. Have the *refund* feature enabled on your account. If this is not the case, please contact support at help@paycruiser.com and request activation
 2. Search the transaction you need to refund. Please note, you can only search by transaction_tag and transaction_id
 
 ## Request
 ```
 curl --request GET \
  --url 'https://sandbox-api.paycruiser.com/merchant/paycruiser-transactions/?search=4650684582' \
  --header 'Authorization: Token <YOUR_TOKEN>'
 ```
 
 ## Response
 
 ```
{
  "count": 1,
  "next": null,
  "previous": null,
  "results": [
    {
      "id": "c5dd75a3-506f-4b77-b4ec-78497b0cbe0d",
      "created_at": "2021-08-27T05:56:13.360412Z",
      "updated_on": "2021-08-27T05:56:13.379153Z",
      "total_amount_in_cents": "100",
      "total_amount_USD": "1.00",
      "tip_amount": "0.00",
      "tipped_user": null,
      "card_type": "visa",
      "card_number": "4111 ****",
      "card_cvv": "9",
      "card_expiry": "12",
      "full_name": "Ousmane Conde",
      "ref_customer": "163609",
      "ref_merchant": "PayCruiser-SN",
      "description": "1 x 1 USD; Memo: mpos id: 1223; Tip: 0.00 USD",
      "memo": "mpos id: 1223",
      "email": "",
      "zipcode": "55116",
      "unit_count": "1",
      "promocode": "NONE",
      "phone_number": "",
      "resp_correlation_id": "134.3004377289767",
      "resp_transaction_status": "approved",
      "resp_validation_status": "success",
      "resp_transaction_type": "purchase",
      "resp_transaction_id": "ET128985",
      "resp_transaction_tag": "4650744783",
      "resp_method": "credit_card",
      "resp_amount": "100",
      "resp_currency": "USD",
      "resp_cvv2": null,
      "resp_card_type": "visa",
      "resp_cardholder_name": "Ousmane Conde",
      "resp_card_number": "1111",
      "resp_exp_date": "1223",
      "resp_bank_resp_code": "100",
      "resp_bank_message": "Approved",
      "resp_gateway_resp_code": "00",
      "resp_gateway_message": "Transaction Normal",
      "resp_error_msg": null
    }
  ]
}
 ```
 
 3. Grab the values for keys(id,resp_transaction_id and resp_transaction_tag) of the transaction returned
 4. Process the refund
 
 ## Request
 
 ```
 curl --request POST \
  --url http://localhost/merchant/paycruiser-transactions/<id>/refund/ \
  --header 'Authorization: Token <YOUR_TOKEN>' \
  --header 'Content-Type: application/json' \
  --data '{
  "merchant_ref": "<Your merchant name or the merchant-name of your sub-merchant you are processing this transaction for>",
  "transaction_id":"ET128985", # This is resp_transaction_id
  "transaction_tag": "4650744783", # this is resp_transaction_tag
  "transaction_type": "void", # "refund" if you would like to process refund instead. You can only void same day transactions
  "method": "credit_card",
  "amount": "100",
  "currency_code": "USD"
}'
 ```
 
 ## Response
 
  ```
  {
  "correlation_id": "134.3004427469539",
  "transaction_status": "approved",
  "validation_status": "success",
  "transaction_type": "refund",
  "transaction_id": "RETURN",
  "transaction_tag": "4650747768",
  "method": "credit_card",
  "amount": "100",
  "currency": "USD",
  "bank_resp_code": "100",
  "bank_message": "Approved",
  "gateway_resp_code": "00",
  "gateway_message": "Transaction Normal",
  "retrieval_ref_no": "210826",
  "tokenized_card_number": "7818177350131111",
  "is_success": 1
  }
  ```

# 7. Log Out

You just need to delete the Header token from the device. Without Header Token, user is basically logged out

# 8. Reccuring Payments

## 8.1 Create Customer
This endpoints links a new customer with a merchant (e.g: You want to enroll a new client in order to store their credit card information and charge them on a reccuring basis. To achieve this, you'd first need to to create the customer record as indicated below)
```
curl --request POST \
  --url https://sandbox-api.paycruiser.com/merchant/merchant-customers/ \
  --header 'Authorization: Token 7a7887e7c98723881757c9wq94d0ae4c0b406c0f' \
  --header 'Content-Type: application/json' \
  --data '{
		"first_name": "Jane",
		"last_name": "Doe",
		"email": "janedoe@paycruiser.com",
		"phone": "‭+15629992511‬",
		"profession": "Student",
		"notes": "",
		"merchant": 1113
}'
```
For more details regarding this endpoint, please visit our [online doc](https://api.paycruiser.com/swagger/#/merchant/merchant_merchant-customers_create)


## 8.2 Create/Save Customer Credit Card (for recurring Payments)
Once you have created the customer, now you can link a credit card information to the customer. Credit card will be encrypted using multiple encryption algorithms and store in an encrypted system as well. Once credit card saved, you will recveive the encrypted card number, which you can only use for this specific merchant on and exclusively PayCruiser.

```
curl --request POST \
  --url https://sandbox-api.paycruiser.com/merchant/customer-credit-cards/ \
  --header 'Authorization: Token 7a7887e7c98723881757c9wq94d0ae4c0b406c0f' \
  --header 'Content-Type: application/json' \
  --data '{
  "card_number": "373953192351004",
  "card_type": "American Express",
  "cardholder_name": "Jane Doe",
  "exp_date": "0724",
  "cvv": "724",
  "zip_code": "55116",
  "card_owner": "901f95df-23d2-4f1d-bf7c-3dd3b7116946",
  "authorized_merchant": 1113
}'
```

For more information regarding this endpoint, please visit our [online documentation](https://api.paycruiser.com/swagger/#/merchant/merchant_customer-credit-cards_create)

## 8.3 Get Customer Information
This endpoint will provide you with your customers information saved (handy to, for instance, find a customer id, etc...)

### Get List of Customers

```
curl --request GET \
  --url https://sandbox-api.paycruiser.com/merchant/merchant-customers/ \
  --header 'Authorization: Token 7a7887e7c98723881757c9wq94d0ae4c0b406c0f' \
  --header 'Content-Type: application/json'
```

### Get a Specific customer

```
curl --request GET \
  --url https://sandbox-api.paycruiser.com/merchant/merchant-customers/71748772-9024-4ab2-8ebf-e1e3f0a96bf1/ \
  --header 'Authorization: Token 7a7887e7c98723881757c9wq94d0ae4c0b406c0f' \
  --header 'Content-Type: application/json'
```

Please note, you have to pass the customer id in the url to retrieve specifc customer info.


## 8.4 Make Payment from saved Credit Card
This endpoint allows to charge customer card on file.

```
curl --request POST \
  --url https://sandbox-api.paycruiser.com/merchant/customer-credit-cards/d3fd41fd-21f3-494a-8729-512815a1cfb9/charge/ \
  --header 'Authorization: Token 7a7887e7c98723881757c9wq94d0ae4c0b406c0f' \
  --header 'Content-Type: application/json' \
  --data '{
	"merchant_id": 1113,
	"transaction_type": "purchase",
	"method": "token",
	"amount": "500", 
	"currency_code": "USD",
	"customer_id": "901f95df-23d2-4f1d-bf7c-3dd3b7116946",
	"memo": "#43452"
}
'
```
Please note, you have to pass the credit card id in the url and the customer id in the url body.
For more information regarding this endpoint, please visit our [online doc](https://api.paycruiser.com/swagger/#/merchant/merchant_customer-credit-cards_charge)



## 8.5 Get Customer Card Information

```
curl --request GET \
  --url https://sandbox-api.paycruiser.com/merchant/customer-credit-cards/103288e7-b95a-4342-bdce-0ddc595501b3/ \
  --header 'Authorization: Token 7a7887e7c98723881757c9wq94d0ae4c0b406c0f' \
  --header 'Content-Type: application/json'
```

For more information, please visit our [online documentation](https://api.paycruiser.com/swagger/#/merchant/merchant_customer-credit-cards_read)
