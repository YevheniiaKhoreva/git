# Place market order to buy Ethereum

Place a market order to buy a certain amount of Ethereum via the Coinbase API.

**URL** : `/v1/coinbase/eth/buy`

**Method** : `POST`

**Auth required** : YES

**Permissions required** : None

**Required Headers** :

```json
{
    "Authorization": "base64_signed_auth",
    "Content-Type": "application/x-www-form-urlencoded"
}
```

**Data constraints**

Send the following data, 'size' is required.

```json
{
    "size": "[string representing an amount of Ethereum to buy, e.g. '0.1']",
    "productId": "[string representing the product of which to purchase, must be 'ETH-EUR' or 'ETH-USD'. Defaults to 'ETH-EUR']"
}
```

**Data example**

```json
{
    "size": "0.1",
    "productId": "ETH-USD"
}
```

## Success Response

**Condition** : Ef allt er í lagi þá mun tilheyrandi upphæð Ethereum hafa verið keypt.

**Code** : `200 OK`

**Content example**

```json
{
    "status": "success",
    "data": {
        "id": "58ae13c0-3379-4475-89f5-9568a8808b76",
        "size": "0.00100000",
        "product_id": "ETH-EUR",
        "side": "buy",
        "funds": "35.1593857800000000",
        "type": "market",
        "post_only": false,
        "created_at": "2019-08-31T22:28:07.706645Z",
        "done_at": "2019-08-31T22:28:07.711Z",
        "done_reason": "filled",
        "fill_fees": "0.0219245750000000",
        "filled_size": "0.00100000",
        "executed_value": "8.7698300000000000",
        "status": "done",
        "settled": true
    }
}
```

## Error Responses

**Condition** : If order size is not valid.

**Code** : `400 BAD REQUEST`

**Content example**

```json
{
    "status": "error",
    "data": {
        "id": "ORDER-SIZE-INVALID",
        "reason": "Order size must be a string representation of a number."
    }
}
```

### Or

**Condition** : If there is a random error

**Code** : `500 INTERNAL SERVER ERROR`

**Content example**

```json
{
    "status": "error",
    "data": {
        "id": "INTERNAL-SERVER-ERROR",
        "reason": "Internal server error"
    }
}
```
