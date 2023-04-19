---
title: API Reference

language_tabs: # must be one of https://git.io/vQNgJ
  - shell
  # - ruby
  # - python
  # - javascript

toc_footers:
  - <a href='https://app.artsvp.com/join'>Get an API Key</a>

includes:
  - errors

search: true

code_clipboard: true

meta:
  - name: description
    content: Documentation for the ARTSVP API
---

# Introduction

Welcome to the **ARTSVP API**

If you require help getting started or would like to request specific endpoints that suit your needs, please contact us on [support@artsvp.com](mailto:support@artsvp.com)

# Authentication

> To authorize, use this code:

```shell
# With shell, you can just pass the correct header with each request
curl "api_endpoint_here" \
  -H "Authorization: my_api_key"
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


> Make sure to replace `my_api_key` with your API key.

To connect to our API, you must use the __Authorization Header__ to provide your Bearer token. This can be obtained from your dashboard, under _Settings_ -> _Developer_. 

Your API key is used as your bearer token and has complete access to your account so please protect it 🙏🏼

`Authorization: my_api_key`

<aside class="notice">
You must replace <code>my_api_key</code> with your personal API key.
</aside>

# Rate Limiting

We limit API requests to __1,000 requests every 15 minutes__. In the future, we expect to offer "high volume" rate limit plans for businesses that need more throughput.

Each request will return `x-rate-limit headers` so you can keep track of your queries.

# Bookings

Bookings are unique records that belong to an `Event`. They hold important information such as the `name` and `email` of the person making the booking and and other specifics needed for the events such as the booking `size`, `start_time` etc. 

Each booking has a `status` field which denotes where the booking is within the booking process.

## The booking object

> The booking object

```json
{ 
  "id": "bo_jwhfoiwhef9w8f9we79w8f79we87wef",
  "object": "booking",
  "reference": "C922-4EBA-1F8C",
  "status": "is_confirmed",
  "name": "Mike",
  "email": "mike@artsvp.com",
  "size": 5,
  "start_time_utc": "2022-01-01T13:00:00.000Z",
  "start_time_local": "2022-01-01T13:00:00.000+00:00",
  "url": "https://artsvp.com/bookings/jwhfoiwhef9w8f9we79w8f79we87wef",
  "event_id": "836d76",
  "event_name": "Summer Fair",
  "event_reference": "Summer Fair - VIP 1",
  "event_time_zone": "Europe/London",
  "invite_id": "db8e29",
  "tags": [],
  "resource_tags": ["vip", "new_user"],
  "meta_data": {},
  "external_id": "abc123",
  "created_at": "2021-10-21T15:22:35.758Z",
  "updated_at": "2021-10-21T15:33:13.889Z"
}
```

### Attributes

Attribute | Type | Description
--------- | ----------- | -----------
`id` | string | Unique identifier for the object.
`object` | string | String representing the object’s type
`reference` | string | A unique reference for the booking 
`status` | string | The booking status. Prefixed with `is_` (e.g. `is_confirmed`)
`name` | string | Name of the primary guest
`email` | string | Email of the primary guest
`size` | integer | Number of guests attatched to the booking
`start_time_utc` | timestamp | Start time in UTC
`start_time_local` | timestamp | Start time in Event's Time Zone
`url` | string | URL to manage the booking
`event_id` | string | ID of the Event
`event_name` | string | Name of the Event
`event_reference` | string | Internal Reference for the Event
`event_time_zone` | timestamp | Time Zone of the Event
`invite_id` | string | The ID of the booking's Invite (if present)
`tags` | array | Tags assigned to the booking
`resource_tags` | array | Tags assigned to the `Booking Resource`
`meta_data` | hash | Meta Data assigned to the booking
`external_id` | string | External ID assigned to the booking
`created_at` | timestamp | Timestamp when the booking was Created
`updated_at` | timestamp | Timestamp when the booking was Updated



## List all bookings

```shell
curl "https://app.artsvp.com/api/v2/bookings" \
  -H "Authorization: my_api_key"
  -d external_id="abc123"
```

> The above command returns JSON structured like this:

```json
{
  "object": "list",
  "url": "/v2/bookings",
  "data": [
    { 
      "id": "boo_jwhfoiwhef9w8f9we79w8f79we87wef",
      "object": "booking",
      "reference": "C922-4EBA-1F8C",
      "status": "is_confirmed",
      "name": "Mike",
      "email": "mike@artsvp.com",
      "size": 5,
      "start_time_utc": "2022-01-01T13:00:00.000Z",
      "start_time_local": "2022-01-01T13:00:00.000+00:00",
      "url": "https://artsvp.com/bookings/jwhfoiwhef9w8f9we79w8f79we87wef",
      "event_id": "836d76",
      "event_name": "Summer Fair",
      "event_reference": "Summer Fair - VIP 1",
      "event_time_zone": "Europe/London",
      "invite_id": "db8e29",
      "tags": [],
      "resource_tags": ["vip", "new_user"],
      "meta_data": {},
      "external_id": "abc123",
      "created_at": "2021-10-21T15:22:35.758Z",
      "updated_at": "2021-10-21T15:33:13.889Z"
    },
    ...
  ]
```

Return a list of all bookings your Organisation owns. You can optionally scope your request with parameters.

### HTTP Request

`GET https://app.artsvp.com/api/v2/bookings`

### Parameters

Parameter | Required | Description
--------- | ------- | -----------
`external_id` | `false` | External ID associated with the booking
`email` | `false` | Email used to create the booking
`tags` | `false` | Tags associated to the booking


# Invites

Invites exist as a vehicle for a user to create a `booking`. They can be used simply as a unique booking link or be pre-configured with information to define the booking flow or lock the booking to a particular user (name and email). You also have control over the number of times an Invite can be used (number of bookings created). 


<aside>
Matching booking data (name, email, meta_data etc..) will absorbed by the booking once the booking has been created.
</aside>

## The invite object

> The invite object

```json
{ 
  "id": "inv_jwhfoiwhef9w8f9we79w8f79we87wef",
  "object": "invite",
  "code": "372abb",
  "name": "Mike",
  "email": "mike@artsvp.com",
  "resource": "Event",
  "resource_id": "6u9F2a",
  "golden": false,
  "register_interest": false,
  "url": "https://invite.artsvp.com/372abb",
  "used": false,
  "amount_used": "0/1",
  "tags": [
    "single_entry",
    "vip_1"
  ],
  "resource_tags": [
    "new_user"
  ],
  "meta_data": {},
  "external_id": "",
  "created_at": "2021-10-21T15:22:35.758Z",
  "updated_at": "2021-10-21T15:33:13.889Z"
}
```

### Attributes

Attribute | Type | Description
--------- | ----------- | -----------
`id` | string | Unique identifier for the object.
`object` | string | String representing the object’s type
`code` | string | A unique reference for the invite 
`name` | string | Name assigned to the invite
`email` | string | Email assigned to the invite
`resource` | string | Type of the `Resource`
`resource_id` | string | ID of the `Resource`
`golden` | boolean | If the invite is marked as Golden
`register_interest` | boolean | If the invite is marked as Register Interest
`url` | string | URL of the invite
`used` | boolean | If the invite has been used at least once
`amount_used` | string | Number of times the invite has been used
`tags` | array | Tags assigned to the invite 
`resource_tags` | array | Tags assigned to the `Resource`
`meta_data` | hash | Meta Data assigned to the invite
`external_id` | string | External ID assigned to the invite
`created_at` | timestamp | Timestamp when the invte was Created
`updated_at` | timestamp | Timestamp when the invte was Updated


## Create an invite

```shell
curl -X POST "https://app.artsvp.com/api/v2/invites" \
  -H "Authorization: my_api_key"
  -d name="Lucy Green"
  -d email="lucy@example.com"
  -d resource_id="3f10ed"
  -d golen=true
  -d tags=["artsvp", "vip"]
  -d filter="external_id"
```

> The above command returns JSON structured like this:

```json
{ 
  "id": "inv_jwhfoiwhef9w8f9we79w8f79we87wef",
  "object": "invite",
  "code": "468d2w",
  "name": "Lucy Green",
  "email": "lucy@example.com",
  "resource": "Event",
  "resource_id": "3f10ed",
  "golden": true,
  "register_interest": false,
  "url": "https://invite.artsvp.com/468d2w",
  "used": false,
  "amount_used": "0/1",
  "tags": [
    "artsvp",
    "vip"
  ],
  "resource_tags": [],
  "meta_data": {},
  "created_at": "2021-10-21T15:22:35.758Z",
  "updated_at": "2021-10-21T15:33:13.889Z"
}
```

Creates an invite for a specific resource (collections or invites). 

Use a **filter** to prevent duplicate invites from being created. If you attempt to create an invite using a filter, an new invite will only be created if an invite does not already exist with a matching filter. For example, if you filter by email and the email field is `email@example.com`, a new invite will only be created if an invite does not exist with that email.

### HTTP Request

`POST https://app.artsvp.com/api/v2/invites`

### Parameters

Parameter | Required | Description
--------- | ------- | -----------
`name` | `false` | The `name` of the invite
`email` | `false` | The `email` of the invite
`resource_id` | `false` | Type of the `Resource`
`resource_type` | `false` | ID of the `Resource`
`external_id` | `false` | The `external_id` to assign to the invite
`golden` | boolean | If the invite should be Golden
`register_interest` | boolean | If the invite should be Register Interest
`total_count` | `false` | The number of times the invite can be used, if provided 0, the invite becomes limitless. If no value is provided, total_count defaults to 1.
`tags` | `false` | Any tags you want to assign to the invite
`meta_data` | `false` | Meta Data assigned to the invite
`filter` | `false` | Filter the creation of the invite to a variable to avoid duplicate invites. Available filters: [‘name’, ‘email’, ‘user_id’]

## Retrieve an invite

```shell
curl -X GET "https://app.artsvp.com/api/v2/invites/468d2w" \
  -H "Authorization: my_api_key"
  -d code="468d2w"
```

> The above command returns JSON structured like this:

```json
{ 
  "id": "inv_jwhfoiwhef9w8f9we79w8f79we87wef",
  "object": "invite",
  "code": "468d2w",
  "name": "Lucy Green",
  "email": "lucy@example.com",
  "resource": "Event",
  "resource_id": "3f10ed",
  "golden": true,
  "register_interest": false,
  "url": "https://invite.artsvp.com/468d2w",
  "used": false,
  "amount_used": "0/1",
  "tags": [
    "artsvp",
    "vip"
  ],
  "resource_tags": [],
  "meta_data": {},
  "created_at": "2021-10-21T15:22:35.758Z",
  "updated_at": "2021-10-21T15:33:13.889Z",
  "bookings": {
    "id": "boo_123456789",
    "object": "booking"
    ...
  }
}
```

Retrieves the details of an invite that has previously been created. Supply invite code from either an invite creation request or the invite list. This endpoint provides a more detail than the list view by including the associated bookings.

### HTTP Request

`GET https://app.artsvp.com/api/v2/invites/:code`

### Parameters

Parameter | Required | Description
--------- | ------- | -----------
`code` | **`true`** | The code of the invite to retrieve


## Update an invite

```shell
curl -X PATCH "https://app.artsvp.com/api/v2/invites/468d2w" \
  -H "Authorization: my_api_key"
  -d total_count=10
```

> The above command returns JSON structured like this:

```json
{ 
  "id": "inv_jwhfoiwhef9w8f9we79w8f79we87wef",
  "object": "invite",
  "code": "468d2w",
  "name": "Lucy Green",
  "email": "lucy@example.com",
  "resource": "Event",
  "resource_id": "3f10ed",
  "golden": true,
  "register_interest": false,
  "url": "https://invite.artsvp.com/468d2w",
  "used": false,
  "amount_used": "0/10",
  "tags": [
    "artsvp",
    "vip"
  ],
  "resource_tags": [],
  "meta_data": {},
  "created_at": "2021-10-21T15:22:35.758Z",
  "updated_at": "2021-10-21T15:33:13.889Z"
}
```

Updates an invite for certain whitelisted attributes. If the invite has already been used, the associated bookings will also show with the updated invite information.

### HTTP Request

`PATCH https://app.artsvp.com/api/v2/invites/:code`

### Parameters

Parameter | Required | Description
--------- | ------- | -----------
`tags` | `false` | Any tags passed here will overwrite the existing tags
`total_count` | `false` | The number of times the invite can be used, if provided 0, the invite becomes limitless. If no value is provided, total_count defaults to 1.
`golden` | `false` | If set as true, the invite will be created as a golden invite, if set to false, the golden functionality will be removed for the invite
`register_interest` | `false` | If set as true, the invite will mark users as register interest waitlist for an event, if set to false, the functionality will be removed for the invite

## Delete an invite

```shell
curl -X DELETE "https://app.artsvp.com/api/v2/invites/468d2w" \
  -H "Authorization: my_api_key"
```

> The above command returns the following status code:

```text
204 No Content
```

Updates an invite for certain whitelisted attributes. If the invite has already been used, the associated bookings will also show with the updated invite information.

### HTTP Request

`DELETE https://app.artsvp.com/api/v2/invites/:code`

## Batch create invites

```shell
curl -X POST "https://app.artsvp.com/api/v2/invites/batch" \
  -H "Authorization: my_api_key"
  -d name="Lucy Green"
  -d email="lucy@example.com"
  -d resource_tags=["vip"]
```

> The above command returns JSON structured like this:

```json
{
  "object": "batch",
  "url": "/v2/invites/batch",
  "data": [
    { 
      "id": "inv_jwhfoiwhef9w8f9we79w8f79we87wef",
      "object": "invite",
      "code": "372abb",
      "name": "Mike",
      "email": "mike@artsvp.com",
      "resource": "Event",
      "resource_id": "6u9F2a",
      "golden": false,
      "register_interest": false,
      "url": "https://invite.artsvp.com/372abb",
      "used": false,
      "amount_used": "0/1",
      "tags": [
        "single_entry",
        "vip_1"
      ],
      "resource_tags": [
        "vip"
      ],
      "meta_data": {},
      "created_at": "2021-10-21T15:22:35.758Z",
      "updated_at": "2021-10-21T15:33:13.889Z"
    },
    ...
  ],
  "total_events": 1,
  "total_collection": 1,
  "total_resources": 2,
  "total_invites_created": 2
```

Create multiple invites with a single API call

### HTTP Request

`POST https://app.artsvp.com/api/v2/invites/batch`

### Parameters

Parameter | Required | Description
--------- | ------- | -----------
`resource_tags` | **`true`** | Invites will be creates for all resources with matching tags
`name` | `false` | The `name` of the invite
`email` | `false` | The `email` of the invite
`external_id` | `false` | The `external_id` to assign to the invite
`golden` | `false` | If the invite should be Golden
`register_interest` | `false` | If the invite should be Register Interest
`total_count` | `false` | The number of times the invite can be used, if provided 0, the invite becomes limitless. If no value is provided, total_count defaults to 1.
`tags` | `false` | Any tags you want to assign to the invite
`meta_data` | `false` | Meta Data assigned to the invite
`filter` | `false` | Filter the creation of the invite to a variable to avoid duplicate invites. Available filters: [‘name’, ‘email’, ‘user_id’]

<aside class="notice">
The resource is determined by the resource_tags. If `resource_type` or `resource_id` parameters are passed the batch will fail. 
</aside>


## List all invites

```shell
curl "https://app.artsvp.com/api/v2/invites?user_id=123" \
  -H "Authorization: my_api_key"
  -d resource_id="3f10ed"
```

> The above command returns JSON structured like this:

```json
{
  "object": "list",
  "url": "/v2/invites",
  "data": [
    { 
      "id": "inv_jwhfoiwhef9w8f9we79w8f79we87wef",
      "object": "invite",
      "code": "372abb",
      "name": "Mike",
      "email": "mike@artsvp.com",
      "resource": "Event",
      "resource_id": "6u9F2a",
      "golden": false,
      "register_interest": false,
      "url": "https://invite.artsvp.com/372abb",
      "used": false,
      "amount_used": "0/1",
      "tags": [
        "single_entry",
        "vip_1"
      ],
      "resource_tags": [
        "new_user"
      ],
      "meta_data": {},
      "created_at": "2021-10-21T15:22:35.758Z",
      "updated_at": "2021-10-21T15:33:13.889Z"
    },
    ...
  ]
```

Return a list of all bookings your Organisation owns. You can optionally scope your request with parameters.

### HTTP Request

`GET https://app.artsvp.com/api/v2/invites`

### Parameters

Parameter | Required | Description
--------- | ------- | -----------
`resource_id` | `false` | The ID of a resource. Usually matches an Event ID. Click ‘Share’ on an event to view.
`resource_type` | `false` | The type of the resource. Pass either ‘collection’ or ‘event’ to dictate what resource type the `resource_id`. Default is `Event`
`tags` | `false` | Returns all invites with any matching tags
`external_id` | `false` | Only return invites matching this `external_id`
`name` | `false` | Only return invites matching this `name`
`email` | `false` | Only return invites matching this `email`
`limit` | `false` | A limit on the number of objects to be returned, between 1 and 100. Default is 50.
`starting_after` | `false` | A cursor for use in pagination. starting_after is an object ID that defines your place in the list. For instance, if you make a list request and receive 100 objects, ending with `obj_foo`, your subsequent call can include `starting_after=obj_foo` in order to fetch the next page of the list.
`ending_before` | `false` | A cursor for use in pagination. ending_before is an object ID that defines your place in the list. For instance, if you make a list request and receive 100 objects, starting with `obj_bar`, your subsequent call can include `ending_before=obj_bar` in order to fetch the previous page of the list.

