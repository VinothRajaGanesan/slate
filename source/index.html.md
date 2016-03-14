---
title: Bypass API Reference

language_tabs:
  - pseudocode
  - javascript

toc_footers:
  - <a href='https://github.com/bypasslane'>Bypass Github</a>
  - <a href='https://github.com/tripit/slate'>Documentation Powered by Slate</a>

search: true
---

# Introduction

Welcome to the Bypass API!
This documentation covers the basics of creating a mobile POS application.
Specifically we cover the guest checkout sequence for in-seat delivery to a customer in a stadium environment.

Example API usage is provided in JavaScript. You can view code example sin the dark area to the right.

# Design principles and approach

## Protocol and data

> API endpoint examples include

```pseudocode
GET /api/venue/orders

GET /api/venue/location/:location_id/orders

POST /api/venue/location/:location_id/orders

PUT /api/venue/orders/:order_uuid/payments/:payment_uuid
```

The Bypass API is a REST architecture with JSON as the format for data exchange.
Most all resource endpoints implement standard HTTP CRUD (Create / POST, Read / GET, Update / PUT, Delete / DELETE) methods, though some are reserved.


## Security

Security is a primary concern of the Bypass API. All API calls use HTTPS and most use an API session token to authenticate the user.

Additionally, any handling of credit card data is sent to a dedicated, PCI-compliant service (Zuul).

## Reliability in poor network conditions

Network reliability is a constant problem in high traffic sports and entertainment venues.
The Bypass API solves this problem through a distributed architecture and idempotent design for ordering endpoints.
More than an edge-case, it is assumed that network calls will fail frequently and therefore it is critical that any call can be retried without concern for duplicated data or unexpected behavior.
This pattern is enabled via the use of UUIDs for resources created at the edge of the network (e.g., at the POS device application) and then propagated to the server and all other devices.

## Scalability

Bypass achieves scalability via our distributed SOA for resource management.
Distributed resource creation and eventual consistency for data propagation permit the horizontal and vertical scaling of independent services based on the changing needs of the system.

This approach enables two key elements of the API:
1. Consistently fast writes for orders without the need for server-side order math driven by config lookups
2. Background processing of redundant data to offload longer tasks and retain backups of data for recovery scenarios


# The guest checkout sequence

1. Fetch venue
2. Choose service location
3. Lookup menu for service location
4. Create Order locally
5. Authorize payment
6. Send order to Bypass

<!-- # Authentication -->

<!-- > To authorize, use the following example code:

```javascript
var rp = require('request-promise');

var authToken = new Buffer(
  "example_user_name" + ":" + "example_password"
).toString('base64');

var options = {
  url: 'https://auth.bypassmobile.com/auth.json',
  method: 'POST',
  headers: {
    'Accept': 'application/json, text/javascript, */*; q=0.01',
    'Authorization': 'Basic ' + authToken
  },
  json: true
};

rp(options).then(function(session) {
  var token = session.session_token
});
``` -->

<!-- Bypass uses an API session to allow access to the API. You can register a new Kittn API key at our [developer portal](http://example.com/developers).

Kittn expects for the API key to be included in all API requests to the server in a header that looks like the following:

`Authorization: meowmeowmeow`

<aside class="notice">
You must replace <code>meowmeowmeow</code> with your personal API key.
</aside>

# Kittens

## Get All Kittens

```ruby
require 'kittn'

api = Kittn::APIClient.authorize!('meowmeowmeow')
api.kittens.get
```

```python
import kittn

api = kittn.authorize('meowmeowmeow')
api.kittens.get()
```

```shell
curl "http://example.com/api/kittens"
  -H "Authorization: meowmeowmeow"
```

> The above command returns JSON structured like this:

```json
[
  {
    "id": 1,
    "name": "Fluffums",
    "breed": "calico",
    "fluffiness": 6,
    "cuteness": 7
  },
  {
    "id": 2,
    "name": "Max",
    "breed": "unknown",
    "fluffiness": 5,
    "cuteness": 10
  }
]
```

This endpoint retrieves all kittens.

### HTTP Request

`GET http://example.com/api/kittens`

### Query Parameters

Parameter | Default | Description
--------- | ------- | -----------
include_cats | false | If set to true, the result will also include cats.
available | true | If set to false, the result will include kittens that have already been adopted.

<aside class="success">
Remember — a happy kitten is an authenticated kitten!
</aside> -->

<!-- > In the event of a network failure, it is safe to retry a payment:

```javascript
var transaction = {
  uuid: "transactionUUID",
  transaction: {
    uuid: "transactionUUID",
    gateway_account_login: 'example gateway account',
    order_id: '1',
    amount: 100, // <-- $1.00 (amount is in pennies)
    credit_card: {}
}

request.put('https://zuul.bypassmobile.com/sales/transactionUUID', transaction)
  .then(successHandler)
  .catch(function (error) {
    // If we had an error, retry
    return request.put('https://zuul.bypassmobile.com/sales/transactionUUID', transaction)
      .then(successHandler);
  });
``` -->
