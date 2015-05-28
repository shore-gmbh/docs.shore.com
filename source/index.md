# Customers

The Customer Store Service is used for serving any kind of customer related information.


## GET /:oid/customers

> Example Request

```language-curl
curl https://api.shore.com/v1/4eadc0f0-45d1-4a8c-91b4-ca5d82910135/customers \
  -d dude="awesome-stuff"
  --data dude="awesome stuff"
  -u authentication_key
```

> Example Response

```language-javascript
{
  "meta": {
    "current_page": 1,
    "total_pages": 3,
    "per_page": 25
  },

  "customers": [
  ]
}
```

Retrieves all customers for a given organization id. The result is paginated.

### Request URL
`https://api.shore.com/v1/:oid/customers`

### Request Params
<table class="attributes">
  <tr>
    <td>oid<div>String, <span class="req">Required</span></div></td>
    <td>The organization id</td>
  </tr>
</table>

### Reponse Code
`200`


## GET /:oid/customers/:id

> Example Request

```shell
$ curl https://api.shore.com/v1/4eadc0f0-45d1-4a8c-91b4-ca5d82910135/customers/522f78b8-6f39-4dce-be8e-ce0fc0f816e6
```

> Example Response

```json
{
  "id": "522f78b8-6f39-4dce-be8e-ce0fc0f816e6",

  "created_at": "2015-03-26T18:33:10.545+01:00",
  "updated_at": "2015-03-26T18:33:10.545+01:00",

  "locale": "de-DE",

  "identity": {
    "gender": "male",
    "first": "John",
    "last": "Doe"
  },

  "groups": [
    {
      "name": "category",
      "value": "VIP"
    }
  ],

  "addresses": [
    {
      "line1": "Rosenheimer Str 145e",
      "line2": "1st floor",
      "city": "Munich",
      "state": "Bavaria",
      "zip": "81671",
      "country": "Germany"
    }, {
      "...": "..."
    }
  ],

  "dates": [
    {
      "name": "birthday",  // birthday, anniversary, other
      "value": "2015-03-26"
    }, {
      "...": "..."
    }
  ],

  "emails": [
    {
      "primary": true,
      "name": "home", // home, work, other
      "value": "john.doe@example.com"
    }, {
      "...": "..."
    }
  ],

  "phones": [
    {
      "primary": true,
      "name": "mobile", // mobile, work, home, work-fax, home-fax, other
      "value": "123456789"
    }, {
      "...": "..."
    }
  ],

  "urls": [
    {
      "name": "website", // website, other
      "value": "http://www.example.com"
    }, {
      "...": "..."
    }
  ],

  "attributes": [
    {
      "name": "description",
      "value": "Some custom definde attribute"
    }, {
      "...": "..."
    }
  ]
}
```

Retrieves the details of an existing customer.

### Request URL
`https://api.shore.com/v1/:oid/customers/:id`

### Request Params
<table class="attributes">
  <tr>
    <td>oid<div>String, <span class="req">Required</span></div></td>
    <td>The organization id</td>
  </tr>
  <tr>
    <td>id<div>String, <span class="req">Required</span></div></td>
    <td>The customer id</td>
  </tr>
</table>

### Response Code
`200`

## POST /:oid/customers

> Example Request

```shell
$ curl https://api.shore.com/v1/4eadc0f0-45d1-4a8c-91b4-ca5d82910135/customers \
  -X POST
  -d '{
    "identity": {
      "first": "John",
      "last": "Doe"
    }
  }'
```

> Example Response

```json
{
  "id": "522f78b8-6f39-4dce-be8e-ce0fc0f816e6",

  "created_at": "2015-03-26T18:33:10.545+01:00",
  "updated_at": "2015-03-26T18:33:10.545+01:00",

  "identity": {
    "first": "John",
    "last": "Doe"
  }
}
```

Creates a new customer record.

### Request URL
`https://api.shore.com/v1/:oid/customers`

### Request Params
<table class="attributes">
  <tr>
    <td>oid<div>String, <span class="req">Required</span></div></td>
    <td>The organization id</td>
  </tr>
</table>

### Response Code
`201`


## PUT /:oid/customers/:id

> Example Request

```shell
$ curl https://api.shore.com/v1/4eadc0f0-45d1-4a8c-91b4-ca5d82910135/customers/522f78b8-6f39-4dce-be8e-ce0fc0f816e6
  -X PUT
  -d '{
    "identity": {
      "first": "Jane"
    }
  }'
```

> Example Response

```json
{
  "id": "522f78b8-6f39-4dce-be8e-ce0fc0f816e6",

  "created_at": "2015-03-26T18:33:10.545+01:00",
  "updated_at": "2015-03-26T18:33:10.545+01:00",

  "identity": {
    "first": "Jane",
    "last": "Doe"
  }
}
```

Updates the customer by setting the values of the passed parameters. Any parameters not provided will be left unchanged.

### Request URL
`https://api.shore.com/v1/:oid/customers/:id`

### Request Params
<table class="attributes">
  <tr>
    <td>oid<div>String, <span class="req">Required</span></div></td>
    <td>The organization id</td>
  </tr>
  <tr>
    <td>id<div>String, <span class="req">Required</span></div></td>
    <td>The customer id</td>
  </tr>
</table>

### Response Code
`200`


## DELETE /:oid/customers/:id

> Example Request

```bash
$ curl https://api.shore.com/v1/4eadc0f0-45d1-4a8c-91b4-ca5d82910135/customers/522f78b8-6f39-4dce-be8e-ce0fc0f816e6 \
  -X DELETE
```

> Example Response

```json
{
  "id": "522f78b8-6f39-4dce-be8e-ce0fc0f816e6",
  "deleted_at": "2015-03-26T18:33:10.545+01:00"
}
```

Permanently deletes a customer. It cannot be undone. Upon deletion, the response returns the `deleted_at` date.

### Request URL
`https://api.shore.com/v1/:oid/customers`

### Request Params
<table class="attributes">
  <tr>
    <td>oid<div>String, <span class="req">Required</span></div></td>
    <td>The organization id</td>
  </tr>
</table>

### Response Code
`201`


