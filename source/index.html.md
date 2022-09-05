---
title: API Reference

language_tabs: # must be one of https://git.io/vQNgJ
  - shell
  # - ruby
  # - python
  # - javascript

toc_footers:
  - <a href='https://app.artsvp.co/join'>Get an API Key</a>

includes:
  - errors

search: true

code_clipboard: true

meta:
  - name: description
    content: Documentation for the BOOKSVP API
---

# Introduction

Welcome to the BOOKSVP API! 

If you require help getting started or would like to request specific endpoints that suit your needs, please contact us on [support@artsvp.com](mailto:support@artsvp.com)

# Authentication

> To authorize, use this code:

```shell
# With shell, you can just pass the correct header with each request
curl "api_endpoint_here" \
  -H "Authorization: svp_api_key"
```

<!-- 
```ruby
xxx
```

```python
xxx
``` 

```javascript
xxx
``` 
-->


> Make sure to replace `svp_api_key` with your API key.

To connect to our API, you must use the __Authorization Header__ to provide your Bearer token. This can be obtained from your dashboard, under _Settings_ -> _Developer_. 

Your API key is used as your bearer token and has complete access to your account so please protect it 🙏🏼

`Authorization: svp_api_key`

<aside class="notice">
You must replace <code>svp_api_key</code> with your personal API key.
</aside>

# Rate Limiting

We limit API requests to __500 requests per 15 minutes__. In the future, we expect to offer "high volume" rate limit plans for businesses that need more throughput.

Each request will return `x-rate-limit headers` so you can keep track of your queries.

# Bookings

## List Bookings

```shell
curl "https://app.artsvp.co/api/v1/bookings?user_id=123" \
  -H "Authorization: svp_api_key"
```

> The above command returns JSON structured like this:

```json
{
  "object": "list",
  "url": "/v1/bookings",
  "data": [
    {
      "user_id": "abc123",
      "reference": "06gh7se2",
      "event_id": "3g7k0w",
      "event_name": "Example Event",
      "event_reference": "Example Event",
      "event_time_zone": "America/New York",
      "name": "Mike Holford",
      "email": "mike@artsvp.com",
      "size": 2,
      "meta_data": {},
      "start_time_utc": "xxx",
      "start_time_local": "xxx",
      "url": "xxx",
      "confirmed": "true",
      "cancelled": "false",
      "pending": "false",
      "created_at": "false",
      "updated_at": "false",
    },
    ...
  ]
```

Return a list of all bookings this API key owns, scoped by passed parameters.

### HTTP Request

`GET https://app.artsvp.co/api/v1/bookings`

### Query Parameters

Parameter | Required | Description
--------- | ------- | -----------
user_id | false | Unique ID of the user making the booking. 
email | false | Email used to create the booking
tags | false | Tags associated to the booking


<aside class="success">
Remember — a happy kitten is an authenticated kitten!
</aside>

## Get a Specific Kitten

```ruby
require 'kittn'

api = Kittn::APIClient.authorize!('svp_api_key')
api.kittens.get(2)
```

```python
import kittn

api = kittn.authorize('svp_api_key')
api.kittens.get(2)
```

```shell
curl "http://example.com/api/kittens/2" \
  -H "Authorization: svp_api_key"
```

```javascript
const kittn = require('kittn');

let api = kittn.authorize('svp_api_key');
let max = api.kittens.get(2);
```

> The above command returns JSON structured like this:

```json
{
  "id": 2,
  "name": "Max",
  "breed": "unknown",
  "fluffiness": 5,
  "cuteness": 10
}
```

This endpoint retrieves a specific kitten.

<aside class="warning">Inside HTML code blocks like this one, you can't use Markdown, so use <code>&lt;code&gt;</code> blocks to denote code.</aside>

### HTTP Request

`GET http://example.com/kittens/<ID>`

### URL Parameters

Parameter | Description
--------- | -----------
ID | The ID of the kitten to retrieve

## Delete a Specific Kitten

```ruby
require 'kittn'

api = Kittn::APIClient.authorize!('svp_api_key')
api.kittens.delete(2)
```

```python
import kittn

api = kittn.authorize('svp_api_key')
api.kittens.delete(2)
```

```shell
curl "http://example.com/api/kittens/2" \
  -X DELETE \
  -H "Authorization: svp_api_key"
```

```javascript
const kittn = require('kittn');

let api = kittn.authorize('svp_api_key');
let max = api.kittens.delete(2);
```

> The above command returns JSON structured like this:

```json
{
  "id": 2,
  "deleted" : ":("
}
```

This endpoint deletes a specific kitten.

### HTTP Request

`DELETE http://example.com/kittens/<ID>`

### URL Parameters

Parameter | Description
--------- | -----------
ID | The ID of the kitten to delete

