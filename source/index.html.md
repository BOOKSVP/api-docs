---
title: API Reference

language_tabs: # must be one of https://github.com/rouge-ruby/rouge/wiki/List-of-supported-languages-and-lexers
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

> Make sure to replace `my_api_key` with your API key.

To connect to our API, you must use the **Authorization Header** to provide your Bearer token. This can be obtained from your dashboard, under _Settings_ -> _Developer_.

Your API key is used as your bearer token and has complete access to your account so please protect it 🙏🏼

`Authorization: my_api_key`

<aside class="notice">
You must replace <code>my_api_key</code> with your personal API key.
</aside>

# Rate Limiting

We limit API requests to **1,000 requests every 15 minutes**. In the future, we expect to offer "high volume" rate limit plans for businesses that need more throughput.

Each request will return `x-rate-limit headers` so you can keep track of your queries.

If the rate limit is exceeded we will return a 429 (Too Many Requests) response status code, please make sure to handle this response code, as we do not queue requests that have been made after the limit has been exceeded.

# Pagination

For endpoints that return large quantities of data, pagination is implemented to reduce request times. When an endpoint implements pagination, a special key value pair in the response will include `has_more` with either `true` or `false`.

If this is returned, then a secondary key value pair will be available labeled `last_element_id`, where `element` will be replaced with whatever is being queried, for example, `last_booking_id` for the bookings endpoint.

This will be used as a cursor for use in pagination, where providing the param `starting_after` with the `last_element_id` in your subsequent call will return the next page of results, and providing the param `ending_before` with the `last_element_id` will return the previous page of results.

# Resources

**Resource** is the collective term for _Event_ and _Collection_ objects. Some functionality, such as creating invites can apply to either _Event_ or _Collection_ objects- so for simplicity, we refer to them as a _Resource_ in the documentation.

# Events

Events typically represent a single event that you are hosting and can be used to create bookings, send invites, and manage your guest list. They have their own dedicated availability / capacity settings and can be configured to suit your needs.

## The event object

> The event object

```json
{
  "id": "eve-c60d5868-c4cf-4b55-83f2-6578a5173767",
  "object": "event",
  "code": "efa400",
  "name": "Summer Party",
  "reference": "Summer Party (VIP 1)",
  "description": "A summer meet and greet for our VIP guests",
  "time_zone": "Europe/London",
  "start_date": "2023-05-18",
  "start_time": "2023-05-18T10:00:00.000+01:00",
  "end_date": "2023-05-18",
  "end_time": "2023-05-18T17:00:00.000+01:00",
  "public_url": "http://artsvp.com/efa400",
  "banner_image": "https://artsvp.s3.eu-west-2.amazonaws.com/uploads/banners/xxxxx.jpeg",
  "resource_tags": ["vip", "art_fair"],
  "created_at": "2023-04-06T15:12:08.671Z",
  "updated_at": "2023-05-15T11:34:48.746Z"
}
```

### Attributes

| Attribute       | Type      | Description                                      |
| --------------- | --------- | ------------------------------------------------ |
| `id`            | string    | Unique identifier for the object.                |
| `object`        | string    | String representing the object’s type            |
| `code`          | string    | A unique reference for the event used in the URL |
| `name`          | string    | Public facing event name                         |
| `reference`     | string    | Internal event reference                         |
| `description`   | string    | Internal event description (Developer use only)  |
| `timezone`      | string    | The time zone the event is taking place in       |
| `start_date`    | string    | The date the event starts                        |
| `start_time`    | string    | The time the event starts                        |
| `end_date`      | string    | The date the event ends                          |
| `end_time`      | string    | The time the event ends                          |
| `public_url`    | string    | The public URL for the event                     |
| `banner_image`  | string    | The banner image for the event                   |
| `resource_tags` | array     | Internal tags assigned to the event              |
| `created_at`    | timestamp | Timestamp when the event was created           |
| `updated_at`    | timestamp | Timestamp when the event was updated           |

## Retrieve an event

```shell
curl -X GET "https://app.artsvp.com/api/v2/events/eve-c60d5868-c4cf-4b55-83f2-6578a5173767" \
  -H "Authorization: my_api_key"
```

> The above command returns JSON structured like this:

```json
{
  "id": "eve-c60d5868-c4cf-4b55-83f2-6578a5173767",
  "object": "event",
  "code": "efa400",
  "name": "Summer Party",
  "reference": "Summer Party (VIP 1)",
  "description": "A summer meet and greet for our VIP guests",
  "time_zone": "Europe/London",
  "start_date": "2023-05-18",
  "start_time": "2023-05-18T10:00:00.000+01:00",
  "end_date": "2023-05-18",
  "end_time": "2023-05-18T17:00:00.000+01:00",
  "public_url": "http://artsvp.com/efa400",
  "banner_image": "https://artsvp.s3.eu-west-2.amazonaws.com/uploads/banners/xxxxx.jpeg",
  "resource_tags": ["vip", "art_fair"],
  "created_at": "2023-04-06T15:12:08.671Z",
  "updated_at": "2023-05-15T11:34:48.746Z"
}
```

Retrieves the details of an event.

### HTTP Request

`GET https://app.artsvp.com/api/v2/events/:id`

### Parameters

| Parameter | Required   | Description                     |
| --------- | ---------- | ------------------------------- |
| `id`      | **`true`** | The id of the event to retrieve |

## List all events

```shell
curl "https://app.artsvp.com/api/v2/events" \
  -H "Authorization: my_api_key"
```

> The above command returns JSON structured like this:

```json
{
  "object": "list",
  "url": "/v2/events",
  "data": [
    {
      "id": "eve-c60d5868-c4cf-4b55-83f2-6578a5173767",
      "object": "event",
      "code": "efa400",
      "name": "Summer Party",
      ...
    },
    {
      "id": "eve-c60d5868-c4cf-4b55-83f2-6578a8263751",
      "object": "event",
      "code": "gh7823",
      "name": "Winter Party",
      ...
    },
    ...
  ]
}
```

Return a list of all events your Organisation owns.

### HTTP Request

`GET https://app.artsvp.com/api/v2/events`

# Collections

Collections are a way to group events together. For example, you may want to group all events for a particular Art Fair together.

## The collection object

> The collection object

```json
{
  "id": "col-ab281a7f-c85e-4ecb-952c-b17f9e181826",
  "object": "collection",
  "code": "433ca2",
  "name": "VIP program",
  "public_url": "http://localhost:3000/433ca2",
  "banner_image": "https://artsvp.s3.eu-west-2.amazonaws.com/uploads/banners/5e52e75e-163f-4754-bad4-7d63d6bc508f.png",
  "resource_tags": [],
  "created_at": "2023-04-21T09:21:51.167Z",
  "updated_at": "2023-04-21T09:22:49.868Z"
}
```

### Attributes

| Attribute       | Type      | Description                                      |
| --------------- | --------- | ------------------------------------------------ |
| `id`            | string    | Unique identifier for the object.                |
| `object`        | string    | String representing the object’s type            |
| `code`          | string    | A unique reference for the collection used in the URL |
| `name`          | string    | Public facing collection name                         |
| `public_url`    | string    | The public URL for the collection                     |
| `banner_image`  | string    | The banner image for the collection                   |
| `resource_tags` | array     | Internal tags assigned to the collection              |
| `created_at`    | timestamp | Timestamp when the collection was created           |
| `updated_at`    | timestamp | Timestamp when the collection was updated           |

## Retrieve a collection

```shell
curl -X GET "https://app.artsvp.com/api/v2/collection/col-ab281a7f-c85e-4ecb-952c-b17f9e181826" \
  -H "Authorization: my_api_key"
```

> The above command returns JSON structured like this:

```json
{
  "id": "col-ab281a7f-c85e-4ecb-952c-b17f9e181826",
  "object": "collection",
  "code": "433ca2",
  "name": "VIP program",
  "public_url": "http://localhost:3000/433ca2",
  "banner_image": "https://artsvp.s3.eu-west-2.amazonaws.com/uploads/banners/5e52e75e-163f-4754-bad4-7d63d6bc508f.png",
  "resource_tags": [],
  "created_at": "2023-04-21T09:21:51.167Z",
  "updated_at": "2023-04-21T09:22:49.868Z"
}
```

Retrieves the details of a collection.

### HTTP Request

`GET https://app.artsvp.com/api/v2/collections/:id`

### Parameters

| Parameter | Required   | Description                          |
| --------- | ---------- | ------------------------------------ |
| `id`      | **`true`** | The id of the collection to retrieve |

## List all collections

```shell
curl "https://app.artsvp.com/api/v2/collections" \
  -H "Authorization: my_api_key"
```

> The above command returns JSON structured like this:

```json
{
  "object": "list",
  "url": "/v2/collections",
  "data": [
    {
        "id": "col-ab281a7f-c85e-4ecb-952c-b17f9e181826",
        "object": "collection",
        "code": "433ca2",
        "name": "VIP program",
        ...
    },
    {
        "id": "col-89kl2ghq-c85e-4ecb-952c-b17f62hy73ds",
        "object": "collection",
        "code": "78hgwq",
        "name": "Front of House",
        ...
    },
    ...
  ]
}
```

Return a list of all collections your Organisation owns.

### HTTP Request

`GET https://app.artsvp.com/api/v2/collections`

# Bookings

Bookings are unique records that belong to an `Event`. They hold important information such as the `name` and `email` of the person making the booking and and other specifics needed for the events such as the booking `size`, `start_time` etc.

Each booking has a `status` field which denotes where the booking is within the booking process.

## The booking object

> The booking object

```json
{
  "id": "boo-46d60ac0-bd1a-4e11-a3e2-0394e872887f",
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
  "event": {
    "id": "eve-c60d5868-c4cf-4b55-83f2-6578a5173767",
    "object": "event",
    "code": "efa400",
    "name": "Summer Party",
    ...
  },
  "tags": [],
  "resource_tags": ["vip", "new_user"],
  "meta_data": {},
  "external_id": "abc123",
  "created_at": "2021-10-21T15:22:35.758Z",
  "updated_at": "2021-10-21T15:33:13.889Z"
}
```

### Attributes

| Attribute          | Type      | Description                                                   |
| ------------------ | --------- | ------------------------------------------------------------- |
| `id`               | string    | Unique identifier for the object.                             |
| `object`           | string    | String representing the object’s type                         |
| `reference`        | string    | A unique reference for the booking                            |
| `status`           | string    | The booking status. Prefixed with `is_` (e.g. `is_confirmed`) |
| `name`             | string    | Name of the primary guest                                     |
| `email`            | string    | Email of the primary guest                                    |
| `size`             | integer   | Number of guests attatched to the booking (includes primary guest)                    |
| `start_time_utc`   | timestamp | Start time in UTC                                             |
| `start_time_local` | timestamp | Start time in Event's Time Zone                               |
| `url`              | string    | URL to manage the booking                                     |
| `invite_id`        | string    | Booking's invite reference (if present)          |
| `event`            | hash      | The event object the booking is made for                   |
| `tags`             | array     | Tags assigned to the booking                                  |
| `resource_tags`    | array     | Tags assigned to the `Booking Resource`                       |
| `meta_data`        | hash      | Meta Data assigned to the booking                             |
| `external_id`      | string    | External ID assigned to the booking                           |
| `created_at`       | timestamp | Timestamp when the booking was created                        |
| `updated_at`       | timestamp | Timestamp when the booking was updated                        |

## Retrieve a booking

```shell
curl -X GET "https://app.artsvp.com/api/v2/bookings/boo-46d60ac0-bd1a-4e11-a3e2-0394e872887f" \
  -H "Authorization: my_api_key"
```

> The above command returns JSON structured like this:

```json
{
  "id": "boo-46d60ac0-bd1a-4e11-a3e2-0394e872887f",
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
  "event": {
    "id": "eve-c60d5868-c4cf-4b55-83f2-6578a5173767",
    "object": "event",
    "code": "efa400",
    "name": "Summer Party",
    ...
  },
  "tags": [],
  "resource_tags": ["vip", "new_user"],
  "meta_data": {},
  "external_id": "abc123",
  "created_at": "2021-10-21T15:22:35.758Z",
  "updated_at": "2021-10-21T15:33:13.889Z"
}
```

Retrieves the details of a booking.

### HTTP Request

`GET https://app.artsvp.com/api/v2/bookings/:id`

### Parameters

| Parameter | Required   | Description                       |
| --------- | ---------- | --------------------------------- |
| `id`      | **`true`** | The ID of the booking to retrieve |

## Update a booking

```shell
curl -X PATCH "https://app.artsvp.com/api/v2/bookings/boo-46d60ac0-bd1a-4e11-a3e2-0394e872887f" \
  -H "Authorization: my_api_key"
  -d external_id=test123
```

> The above command returns JSON structured like this:

```json
{
  "id": "boo-46d60ac0-bd1a-4e11-a3e2-0394e872887f",
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
  "event": {
    "id": "eve-c60d5868-c4cf-4b55-83f2-6578a5173767",
    "object": "event",
    "code": "efa400",
    "name": "Summer Party",
    ...
  },
  "tags": [],
  "resource_tags": ["vip", "new_user"],
  "meta_data": {},
  "external_id": "test123",
  "created_at": "2021-10-21T15:22:35.758Z",
  "updated_at": "2021-10-21T15:33:13.889Z"
}
```

Updates a booking for certain whitelisted attributes.

### HTTP Request

`PATCH https://app.artsvp.com/api/v2/bookings/:id`

### Parameters

| Parameter     | Required | Description                         |
| ------------- | -------- | ----------------------------------- |
| `name`        | `false`  | Name of the primary guest           |
| `email`       | `false`  | Email of the primary guest          |
| `meta_data`   | `false`  | Meta Data assigned to the booking   |
| `external_id` | `false`  | External ID assigned to the booking |

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
      "id": "boo-46d60ac0-bd1a-4e11-a3e2-0394e872887f",
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
      "event": {
        "id": "eve-c60d5868-c4cf-4b55-83f2-6578a5173767",
        "object": "event",
        "code": "efa400",
        "name": "Summer Party",
        ...
      },
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

| Parameter        | Required | Description                                                                                                                                                                                                                                                                                             |
| ---------------- | -------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `external_id`    | `false`  | External ID associated with the booking                                                                                                                                                                                                                                                                 |
| `email`          | `false`  | Email used to create the booking                                                                                                                                                                                                                                                                        |
| `tags`           | `false`  | Tags associated to the booking                                                                                                                                                                                                                                                                          |
| `starting_after` | `false`  | A cursor for use in pagination. starting_after is an object ID that defines your place in the list. For instance, if you make a list request and receive 100 objects, ending with `obj_foo`, your subsequent call can include `starting_after=obj_foo` in order to fetch the next page of the list.     |
| `ending_before`  | `false`  | A cursor for use in pagination. ending_before is an object ID that defines your place in the list. For instance, if you make a list request and receive 100 objects, starting with `obj_bar`, your subsequent call can include `ending_before=obj_bar` in order to fetch the previous page of the list. |

# Invites

Invites exist as a vehicle for a user to create a `booking`. They can be used simply as a unique booking link or be pre-configured with information to define the booking flow or lock the booking to a particular user (name and email). You also have control over the number of times an Invite can be used (number of bookings created).

<aside>
Matching booking data (name, email, meta_data etc..) will be absorbed by the booking once the booking has been created.
</aside>

## The invite object

> The invite object

```json
{
  "id": "inv-b48ed250-8ebb-448e-a7a0-940d9fcf5dc0",
  "object": "invite",
  "code": "372abb",
  "name": "Mike",
  "email": "mike@artsvp.com",
  "golden": false,
  "register_interest": false,
  "url": "https://invite.artsvp.com/372abb",
  "used": false,
  "amount_used": "0/1",
  "resource": {
    "id": "eve-c60d5868-c4cf-4b55-83f2-6578a5173767",
    "object": "event",
    "code": "efa400",
    "name": "Summer Party",
    ...
  },
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

| Attribute           | Type      | Description                                                     |
| ------------------- | --------- | --------------------------------------------------------------- |
| `id`                | string    | Unique identifier for the object.                               |
| `object`            | string    | String representing the object’s type                           |
| `code`              | string    | A unique reference for the invite                               |
| `name`              | string    | Name assigned to the invite                                     |
| `email`             | string    | Email assigned to the invite                                    |
| `golden`            | boolean   | If the invite is marked as Golden                               |
| `register_interest` | boolean   | If the invite is marked as Register Interest                    |
| `url`               | string    | URL of the invite                                               |
| `used`              | boolean   | If the invite has been used at least once                       |
| `amount_used`       | string    | Number of times the invite has been used                        |
| `resource`          | hash      | The assigned `Resource` (`Event` or `Collection`) to the invite |
| `tags`              | array     | Tags assigned to the invite                                     |
| `resource_tags`     | array     | Tags assigned to the `Resource`                                 |
| `meta_data`         | hash      | Meta Data assigned to the invite                                |
| `external_id`       | string    | External ID assigned to the invite                              |
| `created_at`        | timestamp | Timestamp when the invte was Created                            |
| `updated_at`        | timestamp | Timestamp when the invte was Updated                            |

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
  "id": "inv-b48ed250-8ebb-448e-a7a0-940d9fcf5dc0",
  "object": "invite",
  "code": "468d2w",
  "name": "Lucy Green",
  "email": "lucy@example.com",
  "golden": true,
  "register_interest": false,
  "url": "https://invite.artsvp.com/468d2w",
  "used": false,
  "amount_used": "0/1",
  "resource": {
    "id": "eve-c60d5868-c4cf-4b55-83f2-6578a5173767",
    "object": "event",
    "code": "efa400",
    "name": "Summer Party",
    ...
  },
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

Creates an invite for a specific resource (collections or events).

Use a **filter** to prevent duplicate invites from being created. If you attempt to create an invite using a filter, an new invite will only be created if an invite does not already exist with a matching filter. For example, if you filter by email and the email field is `email@example.com`, a new invite will only be created if an invite does not exist with that email.

### HTTP Request

`POST https://app.artsvp.com/api/v2/invites`

### Parameters

| Parameter           | Required | Description                                                                                                                                  |
| ------------------- | -------- | -------------------------------------------------------------------------------------------------------------------------------------------- |
| `name`              | `false`  | The `name` of the invite                                                                                                                     |
| `email`             | `false`  | The `email` of the invite                                                                                                                    |
| `resource_id`       | `false`  | Type of the `Resource`                                                                                                                       |
| `resource_type`     | `false`  | ID of the `Resource`                                                                                                                         |
| `resource`          | `false`  | Instead of defining _resource_id_ & _resource_type_. The ID of the `Resource`                                                                |
| `external_id`       | `false`  | The `external_id` to assign to the invite                                                                                                    |
| `golden`            | boolean  | If the invite should be Golden                                                                                                               |
| `register_interest` | boolean  | If the invite should be Register Interest                                                                                                    |
| `total_count`       | `false`  | The number of times the invite can be used, if provided 0, the invite becomes limitless. If no value is provided, total_count defaults to 1. |
| `tags`              | `false`  | Any tags you want to assign to the invite                                                                                                    |
| `meta_data`         | `false`  | Meta Data assigned to the invite                                                                                                             |
| `filter`            | `false`  | Filter the creation of the invite to a variable to avoid duplicate invites. Available filters: `name`, `email`, `external_id`                |

## Retrieve an invite

```shell
curl -X GET "https://app.artsvp.com/api/v2/invites/inv-b48ed250-8ebb-448e-a7a0-940d9fcf5dc0" \
  -H "Authorization: my_api_key"
```

> The above command returns JSON structured like this:

```json
{
  "id": "inv-b48ed250-8ebb-448e-a7a0-940d9fcf5dc0",
  "object": "invite",
  "code": "468d2w",
  "name": "Lucy Green",
  "email": "lucy@example.com",
  "golden": true,
  "register_interest": false,
  "url": "https://invite.artsvp.com/468d2w",
  "used": false,
  "amount_used": "0/1",
  "resource": {
    "id": "eve-c60d5868-c4cf-4b55-83f2-6578a5173767",
    "object": "event",
    "code": "efa400",
    "name": "Summer Party",
    ...
  },
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

`GET https://app.artsvp.com/api/v2/invites/:id`

### Parameters

| Parameter | Required   | Description                        |
| --------- | ---------- | ---------------------------------- |
| `id`      | **`true`** | The code of the invite to retrieve |

## Update an invite

```shell
curl -X PATCH "https://app.artsvp.com/api/v2/invites/inv-b48ed250-8ebb-448e-a7a0-940d9fcf5dc0" \
  -H "Authorization: my_api_key"
  -d total_count=10
```

> The above command returns JSON structured like this:

```json
{
  "id": "inv-b48ed250-8ebb-448e-a7a0-940d9fcf5dc0",
  "object": "invite",
  "code": "468d2w",
  "name": "Lucy Green",
  "email": "lucy@example.com",
  "golden": true,
  "register_interest": false,
  "url": "https://invite.artsvp.com/468d2w",
  "used": false,
  "amount_used": "0/10",
  "resource": {
    "id": "eve-c60d5868-c4cf-4b55-83f2-6578a5173767",
    "object": "event",
    "code": "efa400",
    "name": "Summer Party",
    ...
  },
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

`PATCH https://app.artsvp.com/api/v2/invites/:id`

### Parameters

| Parameter           | Required | Description                                                                                                                                              |
| ------------------- | -------- | -------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `tags`              | `false`  | Any tags passed here will overwrite the existing tags                                                                                                    |
| `total_count`       | `false`  | The number of times the invite can be used, if provided 0, the invite becomes limitless. If no value is provided, total_count defaults to 1.             |
| `golden`            | `false`  | If set as true, the invite will be created as a golden invite, if set to false, the golden functionality will be removed for the invite                  |
| `register_interest` | `false`  | If set as true, the invite will mark users as register interest waitlist for an event, if set to false, the functionality will be removed for the invite |
| `external_id`       | `false`  | The `external_id` to assign to the invite                                                                                                                |
| `meta_data`         | `false`  | Meta Data assigned to the booking                                                                                                                        |

## Delete an invite

```shell
curl -X DELETE "https://app.artsvp.com/api/v2/invites/inv-b48ed250-8ebb-448e-a7a0-940d9fcf5dc0" \
  -H "Authorization: my_api_key"
```

> The above command returns the following status code:

```text
204 No Content
```

Updates an invite for certain whitelisted attributes. If the invite has already been used, the associated bookings will also show with the updated invite information.

### HTTP Request

`DELETE https://app.artsvp.com/api/v2/invites/:id`

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
      "id": "inv-b48ed250-8ebb-448e-a7a0-940d9fcf5dc0",
      "object": "invite",
      "code": "372abb",
      "name": "Mike",
      "email": "mike@artsvp.com",
      "golden": false,
      "register_interest": false,
      "url": "https://invite.artsvp.com/372abb",
      "used": false,
      "amount_used": "0/1",
      "resource": {
        "id": "eve-c60d5868-c4cf-4b55-83f2-6578a5173767",
        "object": "event",
        "code": "efa400",
        "name": "Summer Party",
        ...
      },
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

| Parameter           | Required   | Description                                                                                                                                  |
| ------------------- | ---------- | -------------------------------------------------------------------------------------------------------------------------------------------- |
| `resource_tags`     | **`true`** | Invites will be creates for all resources with matching tags                                                                                 |
| `name`              | `false`    | The `name` of the invite                                                                                                                     |
| `email`             | `false`    | The `email` of the invite                                                                                                                    |
| `external_id`       | `false`    | The `external_id` to assign to the invite                                                                                                    |
| `golden`            | `false`    | If the invite should be Golden                                                                                                               |
| `register_interest` | `false`    | If the invite should be Register Interest                                                                                                    |
| `total_count`       | `false`    | The number of times the invite can be used, if provided 0, the invite becomes limitless. If no value is provided, total_count defaults to 1. |
| `tags`              | `false`    | Any tags you want to assign to the invite                                                                                                    |
| `meta_data`         | `false`    | Meta Data assigned to the invite                                                                                                             |
| `filter`            | `false`    | Filter the creation of the invite to a variable to avoid duplicate invites. Available filters: `name`, `email`, `external_id`                |

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
      "id": "inv-b48ed250-8ebb-448e-a7a0-940d9fcf5dc0",
      "object": "invite",
      "code": "372abb",
      "name": "Mike",
      "email": "mike@artsvp.com",
      "golden": false,
      "register_interest": false,
      "url": "https://invite.artsvp.com/372abb",
      "used": false,
      "amount_used": "0/1",
      "resource": {
        "id": "eve-c60d5868-c4cf-4b55-83f2-6578a5173767",
        "object": "event",
        "code": "efa400",
        "name": "Summer Party",
        ...
      },
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

Return a list of all invites your Organisation owns. You can optionally scope your request with parameters.

### HTTP Request

`GET https://app.artsvp.com/api/v2/invites`

### Parameters

| Parameter        | Required | Description                                                                                                                                                                                                                                                                                             |
| ---------------- | -------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `resource_id`    | `false`  | The ID of a resource. Usually matches an Event ID. Click ‘Share’ on an event to view.                                                                                                                                                                                                                   |
| `resource_type`  | `false`  | The type of the resource. Pass either ‘collection’ or ‘event’ to dictate what resource type the `resource_id`. Default is `Event`                                                                                                                                                                       |
| `tags`           | `false`  | Returns all invites with any matching tags                                                                                                                                                                                                                                                              |
| `external_id`    | `false`  | Only return invites matching this `external_id`                                                                                                                                                                                                                                                         |
| `name`           | `false`  | Only return invites matching this `name`                                                                                                                                                                                                                                                                |
| `email`          | `false`  | Only return invites matching this `email`                                                                                                                                                                                                                                                               |
| `limit`          | `false`  | A limit on the number of objects to be returned, between 1 and 100. Default is 50.                                                                                                                                                                                                                      |
| `starting_after` | `false`  | A cursor for use in pagination. starting_after is an object ID that defines your place in the list. For instance, if you make a list request and receive 100 objects, ending with `obj_foo`, your subsequent call can include `starting_after=obj_foo` in order to fetch the next page of the list.     |
| `ending_before`  | `false`  | A cursor for use in pagination. ending_before is an object ID that defines your place in the list. For instance, if you make a list request and receive 100 objects, starting with `obj_bar`, your subsequent call can include `ending_before=obj_bar` in order to fetch the previous page of the list. |
