---
title: API Reference

language_tabs: # must be one of https://github.com/rouge-ruby/rouge/wiki/List-of-supported-languages-and-lexers
  - shell
  - javascript
  - python

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

Welcome to the **ARTSVP API** — the interface for programmatically managing events, collections, invites, and bookings on the ARTSVP platform.

The API follows RESTful conventions: it uses standard HTTP methods, accepts and returns JSON, and uses predictable resource-oriented URLs. All object IDs are prefixed with their type (e.g. `eve-`, `col-`, `inv-`, `boo-`, `gue-`, `tik-`, `extra-`).

**Base URL:** `https://app.artsvp.com/api/v3`

If you need help getting started or would like to request specific endpoints, please contact us at [support@artsvp.com](mailto:support@artsvp.com).

# Authentication

> To authorize, use this code:

```shell
curl "https://app.artsvp.com/api/v3/events" \
  -H "Authorization: my_api_key"
```

```javascript
const response = await fetch("https://app.artsvp.com/api/v3/events", {
  headers: {
    "Authorization": "my_api_key"
  }
});
const data = await response.json();
```

```python
import requests

response = requests.get(
    "https://app.artsvp.com/api/v3/events",
    headers={"Authorization": "my_api_key"}
)
data = response.json()
```

> Make sure to replace `my_api_key` with your API key.

All API requests must include an `Authorization` header containing your API key. You can obtain your key from the ARTSVP dashboard under **Settings → Developer**.

Your API key has full access to your organisation's data — treat it as a secret and never expose it in client-side code.

`Authorization: my_api_key`

<aside class="notice">
You must replace <code>my_api_key</code> with your personal API key.
</aside>

# Versioning

The ARTSVP API is currently on **version 3 (V3)**. This version introduced additional endpoints, improved security via rate limiting, and pagination support. The documentation below is exclusively for V3.

Version 2 (V2) remains available but is considered legacy and will be deprecated in a future release. We recommend migrating to V3 for all new integrations.

# Rate Limiting

We limit API requests to **1,000 requests per 15-minute window**. If you exceed this limit, a **5-minute cooldown** period is enforced during which all requests will receive a `429 Too Many Requests` response.

Each response includes rate limit headers so you can monitor your usage:

| Header                    | Description                                         |
| ------------------------- | --------------------------------------------------- |
| `X-Rate-Limit`            | Maximum requests allowed per window (1000)          |
| `X-Rate-Limit-Remaining`  | Requests remaining in the current window            |
| `X-Rate-Limit-Period`     | Window duration in seconds (900)                    |

<aside class="warning">
Requests made during the cooldown period are not queued — they are rejected outright. Make sure your integration handles 429 responses gracefully.
</aside>

# Pagination

For endpoints that return lists (bookings, invites), pagination is implemented to keep responses fast. A paginated response includes:

| Field              | Description                                                                     |
| ------------------ | ------------------------------------------------------------------------------- |
| `has_more`         | `true` if more results exist beyond the current page                            |
| `last_<element>_id`| The ID of the last item returned (e.g. `last_booking_id`, `last_invite_id`)     |

Use cursor-based pagination with the following parameters:

| Parameter        | Description                                                        |
| ---------------- | ------------------------------------------------------------------ |
| `starting_after` | Fetch the next page of results after this object ID                |
| `ending_before`  | Fetch the previous page of results before this object ID           |
| `limit`          | Number of results per page (1–100, default: 50)                    |

# Status

## Check API status

```shell
curl "https://app.artsvp.com/api/v3/status"
```

```javascript
const response = await fetch("https://app.artsvp.com/api/v3/status");
const data = await response.json();
```

```python
import requests

response = requests.get("https://app.artsvp.com/api/v3/status")
data = response.json()
```

> Returns:

```json
{
  "status": "ok"
}
```

A simple health-check endpoint. No authentication required.

### HTTP Request

`GET https://app.artsvp.com/api/v3/status`

# Resources

**Resource** is the collective term for _Event_ and _Collection_ objects. Some functionality — such as creating invites — can apply to either an _Event_ or _Collection_, so we refer to them as a _Resource_ where applicable.

# Events

Events typically represent a single event that you are hosting. They have their own dedicated availability and capacity settings and can be configured to suit your needs. Events can contain tickets and extras (products) that are available for booking.

## The event object

> The event object

```json
{
  "id": "eve-b891bf58-2534-425c-bfa4-f601a7c7d5c1",
  "object": "event",
  "code": "d43a64",
  "name": "Swiss Cheese",
  "reference": "",
  "description": "",
  "time_zone": "America/New_York",
  "start_date": "2025-08-31",
  "start_time": "2025-08-31T10:00:00.000-04:00",
  "end_date": "2025-08-31",
  "end_time": "2025-08-31T13:00:00.000-04:00",
  "tickets": [
    {
      "id": "tik-4bd84e3c-5fed-48bd-947d-6ef3061600bd",
      "name": "Number of people",
      "price_in_cents": 100
    }
  ],
  "extras": [
    {
      "id": "extra-4bd84e3c-5fed-48bd-947d-6ef3061600bd",
      "name": "Cool Extra",
      "price_in_cents": 1000
    }
  ],
  "location_name": "477 Broadway",
  "location_address": "477 St. New York, NY",
  "public_url": "https://app.artsvp.com/d43a64",
  "banner_image": null,
  "resource_tags": [],
  "created_at": "2025-04-11T11:51:22.248Z",
  "updated_at": "2025-07-15T09:32:54.465Z"
}
```

### Attributes

| Attribute          | Type      | Description                                                     |
| ------------------ | --------- | --------------------------------------------------------------- |
| `id`               | string    | Unique identifier for the event (prefixed with `eve-`)          |
| `object`           | string    | Always `"event"`                                                |
| `code`             | string    | Short unique code used in the public event URL                  |
| `name`             | string    | Public-facing event name                                        |
| `reference`        | string    | Internal reference for the event (not shown publicly)           |
| `description`      | string    | Internal event description (developer use only)                 |
| `time_zone`        | string    | IANA time zone the event takes place in (e.g. `Europe/London`)  |
| `start_date`       | string    | Event start date (`YYYY-MM-DD`)                                 |
| `start_time`       | string    | Event start time in the event's time zone (ISO 8601)            |
| `end_date`         | string    | Event end date (`YYYY-MM-DD`)                                   |
| `end_time`         | string    | Event end time in the event's time zone (ISO 8601)              |
| `tickets`          | array     | Ticket products available for the event (see Product object)    |
| `extras`           | array     | Extra products available for the event (see Product object)     |
| `location_name`    | string    | Name of the event venue                                         |
| `location_address` | string    | Address of the event venue                                      |
| `public_url`       | string    | Public URL for the event booking page                           |
| `banner_image`     | string    | URL of the event banner image (or `null`)                       |
| `resource_tags`    | array     | Internal tags assigned to the event                             |
| `created_at`       | timestamp | When the event was created (UTC)                                |
| `updated_at`       | timestamp | When the event was last updated (UTC)                           |

### Product object

Each ticket or extra within an event has the following shape:

| Attribute        | Type    | Description                                               |
| ---------------- | ------- | --------------------------------------------------------- |
| `id`             | string  | Unique product ID (prefixed with `tik-` or `extra-`)      |
| `name`           | string  | Product name                                              |
| `price_in_cents` | integer | Price in the smallest currency unit (e.g. pence or cents) |

## Retrieve an event

```shell
curl "https://app.artsvp.com/api/v3/events/eve-c60d5868-c4cf-4b55-83f2-6578a5173767" \
  -H "Authorization: my_api_key"
```

```javascript
const response = await fetch(
  "https://app.artsvp.com/api/v3/events/eve-c60d5868-c4cf-4b55-83f2-6578a5173767",
  { headers: { "Authorization": "my_api_key" } }
);
const event = await response.json();
```

```python
import requests

response = requests.get(
    "https://app.artsvp.com/api/v3/events/eve-c60d5868-c4cf-4b55-83f2-6578a5173767",
    headers={"Authorization": "my_api_key"}
)
event = response.json()
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
  "tickets": [
    {
      "id": "tik-4bd84e3c-5fed-48bd-947d-6ef3061600bd",
      "name": "Number of people",
      "price_in_cents": 100
    }
  ],
  "extras": [
    {
      "id": "extra-4bd84e3c-5fed-48bd-947d-6ef3061600bd",
      "name": "Cool Extra",
      "price_in_cents": 1000
    }
  ],
  "location_name": "Gallery ABC",
  "location_address": "123 Artists Way",
  "public_url": "https://app.artsvp.com/efa400",
  "banner_image": "https://artsvp.s3.eu-west-2.amazonaws.com/uploads/banners/xxxxx.jpeg",
  "resource_tags": ["vip", "art_fair"],
  "created_at": "2023-04-06T15:12:08.671Z",
  "updated_at": "2023-05-15T11:34:48.746Z"
}
```

Retrieves the details of an event, including tickets and extras associated with that event.

### HTTP Request

`GET https://app.artsvp.com/api/v3/events/:id`

### Parameters

| Parameter | Required   | Description                     |
| --------- | ---------- | ------------------------------- |
| `id`      | **`true`** | The public ID of the event (prefixed with `eve-`) |

## List all events

```shell
curl "https://app.artsvp.com/api/v3/events" \
  -H "Authorization: my_api_key"
```

```javascript
const response = await fetch("https://app.artsvp.com/api/v3/events", {
  headers: { "Authorization": "my_api_key" }
});
const events = await response.json();
```

```python
import requests

response = requests.get(
    "https://app.artsvp.com/api/v3/events",
    headers={"Authorization": "my_api_key"}
)
events = response.json()
```

> The above command returns JSON structured like this:

```json
{
  "object": "list",
  "url": "/v3/events",
  "data": [
    {
      "id": "eve-c60d5868-c4cf-4b55-83f2-6578a5173767",
      "object": "event",
      "code": "efa400",
      "name": "Summer Party",
      "..."
    },
    {
      "id": "eve-c60d5868-c4cf-4b55-83f2-6578a8263751",
      "object": "event",
      "code": "gh7823",
      "name": "Winter Party",
      "..."
    }
  ]
}
```

Returns a list of all events your organisation owns.

### HTTP Request

`GET https://app.artsvp.com/api/v3/events`

# Collections

Collections are a way to group events together. For example, you may group all events for a particular Art Fair into a single collection.

## The collection object

> The collection object

```json
{
  "id": "col-ab281a7f-c85e-4ecb-952c-b17f9e181826",
  "object": "collection",
  "code": "433ca2",
  "name": "VIP program",
  "public_url": "https://app.artsvp.com/433ca2",
  "banner_image": "https://artsvp.s3.eu-west-2.amazonaws.com/uploads/banners/5e52e75e-163f-4754-bad4-7d63d6bc508f.png",
  "resource_tags": [],
  "created_at": "2023-04-21T09:21:51.167Z",
  "updated_at": "2023-04-21T09:22:49.868Z"
}
```

### Attributes

| Attribute       | Type      | Description                                                 |
| --------------- | --------- | ----------------------------------------------------------- |
| `id`            | string    | Unique identifier for the collection (prefixed with `col-`) |
| `object`        | string    | Always `"collection"`                                       |
| `code`          | string    | Short unique code used in the public collection URL         |
| `name`          | string    | Public-facing collection name                               |
| `public_url`    | string    | Public URL for the collection                               |
| `banner_image`  | string    | URL of the collection banner image (or `null`)              |
| `resource_tags` | array     | Internal tags assigned to the collection                    |
| `created_at`    | timestamp | When the collection was created (UTC)                       |
| `updated_at`    | timestamp | When the collection was last updated (UTC)                  |

## Retrieve a collection

```shell
curl "https://app.artsvp.com/api/v3/collections/col-ab281a7f-c85e-4ecb-952c-b17f9e181826" \
  -H "Authorization: my_api_key"
```

```javascript
const response = await fetch(
  "https://app.artsvp.com/api/v3/collections/col-ab281a7f-c85e-4ecb-952c-b17f9e181826",
  { headers: { "Authorization": "my_api_key" } }
);
const collection = await response.json();
```

```python
import requests

response = requests.get(
    "https://app.artsvp.com/api/v3/collections/col-ab281a7f-c85e-4ecb-952c-b17f9e181826",
    headers={"Authorization": "my_api_key"}
)
collection = response.json()
```

> The above command returns JSON structured like this:

```json
{
  "id": "col-ab281a7f-c85e-4ecb-952c-b17f9e181826",
  "object": "collection",
  "code": "433ca2",
  "name": "VIP program",
  "public_url": "https://app.artsvp.com/433ca2",
  "banner_image": "https://artsvp.s3.eu-west-2.amazonaws.com/uploads/banners/5e52e75e-163f-4754-bad4-7d63d6bc508f.png",
  "resource_tags": [],
  "created_at": "2023-04-21T09:21:51.167Z",
  "updated_at": "2023-04-21T09:22:49.868Z"
}
```

Retrieves the details of a collection.

### HTTP Request

`GET https://app.artsvp.com/api/v3/collections/:id`

### Parameters

| Parameter | Required   | Description                                                     |
| --------- | ---------- | --------------------------------------------------------------- |
| `id`      | **`true`** | The public ID of the collection (prefixed with `col-`)          |

## List all collections

```shell
curl "https://app.artsvp.com/api/v3/collections" \
  -H "Authorization: my_api_key"
```

```javascript
const response = await fetch("https://app.artsvp.com/api/v3/collections", {
  headers: { "Authorization": "my_api_key" }
});
const collections = await response.json();
```

```python
import requests

response = requests.get(
    "https://app.artsvp.com/api/v3/collections",
    headers={"Authorization": "my_api_key"}
)
collections = response.json()
```

> The above command returns JSON structured like this:

```json
{
  "object": "list",
  "url": "/v3/collections",
  "data": [
    {
      "id": "col-ab281a7f-c85e-4ecb-952c-b17f9e181826",
      "object": "collection",
      "code": "433ca2",
      "name": "VIP program",
      "..."
    },
    {
      "id": "col-89kl2ghq-c85e-4ecb-952c-b17f62hy73ds",
      "object": "collection",
      "code": "78hgwq",
      "name": "Front of House",
      "..."
    }
  ]
}
```

Returns a list of all collections your organisation owns.

### HTTP Request

`GET https://app.artsvp.com/api/v3/collections`

# Bookings

Bookings are records that belong to an `Event`. They hold information about the person making the booking (`name`, `email`), the booking `size`, `start_time`, associated tickets/extras, and guests.

Each booking has a `status` field which represents the booking's current state in the lifecycle.

### Booking statuses

| Status           | Description                          |
| ---------------- | ------------------------------------ |
| `is_confirmed`   | Booking is confirmed                 |
| `is_pending`     | Booking is awaiting confirmation     |
| `is_waitlist`    | Booking is on the waitlist           |
| `is_cancelled`   | Booking has been cancelled           |
| `is_rejected`    | Booking has been rejected            |
| `is_expired`     | Booking has expired                  |
| `is_interested`  | Guest has registered interest        |

## The booking object

> The booking object

```json
{
  "id": "boo-46d60ac0-bd1a-4e11-a3e2-0394e872887f",
  "object": "booking",
  "reference": "C922-4EBA-1F8C",
  "status": "is_confirmed",
  "name": "Guest",
  "email": "guest@artsvp.com",
  "size": 3,
  "start_time_utc": "2022-01-01T13:00:00.000Z",
  "start_time_local": "2022-01-01T13:00:00.000+00:00",
  "url": "https://app.artsvp.com/bookings/cqg26ckbqaxf7wywpbcqp8xbucffjbqt",
  "invite_id": "inv-b48ed250-8ebb-448e-a7a0-940d9fcf5dc0",
  "event": {
    "id": "eve-b891bf58-2534-425c-bfa4-f601a7c7d5c1",
    "object": "event",
    "code": "d43a64",
    "name": "Swiss Cheese",
    "reference": "",
    "description": "",
    "time_zone": "America/New_York",
    "start_date": "2025-08-31",
    "start_time": "2025-08-31T10:00:00.000-04:00",
    "end_date": "2025-08-31",
    "end_time": "2025-08-31T13:00:00.000-04:00",
    "tickets": [
      {
        "id": "tik-4bd84e3c-5fed-48bd-947d-6ef3061600bd",
        "name": "Senior Day",
        "price_in_cents": 1000
      }
    ],
    "location_name": "477 Broadway",
    "location_address": "477 St. New York, NY",
    "public_url": "https://app.artsvp.com/d43a64",
    "banner_image": null,
    "resource_tags": [],
    "created_at": "2025-04-11T11:51:22.248Z",
    "updated_at": "2025-07-15T09:32:54.465Z"
  },
  "guests": [
    {
      "id": "gue-888165ed-7af2-4ee0-9d8a-ac68dadb6534",
      "object": "guest",
      "name": "Guest",
      "email": "guest@artsvp.com",
      "lead": true,
      "wallet_pass_url": "https://app.artsvp.com/pass?ref=gue-888165ed-7af2-4ee0-9d8a-ac68dadb6534&type=event_ticket"
    },
    {
      "id": "gue-e4f7f5f5-bbb8-4a9f-b742-71427f962b84",
      "object": "guest",
      "name": "Guest 2",
      "email": null,
      "lead": false,
      "wallet_pass_url": "https://app.artsvp.com/pass?ref=gue-e4f7f5f5-bbb8-4a9f-b742-71427f962b84&type=event_ticket"
    }
  ],
  "tickets": [
    {
      "price_in_cents": {
        "cents": 1000,
        "currency_iso": "GBP"
      },
      "quantity": 3
    }
  ],
  "tags": [],
  "resource_tags": ["vip", "new_user"],
  "event_resource_tags": [],
  "agree_to_mailing_list": false,
  "meta_data": {
    "custom_key": "custom_value"
  },
  "external_id": "abc123",
  "created_at": "2021-10-21T15:22:35.758Z",
  "updated_at": "2021-10-21T15:33:13.889Z"
}
```

### Attributes

| Attribute               | Type      | Description                                                                    |
| ----------------------- | --------- | ------------------------------------------------------------------------------ |
| `id`                    | string    | Unique identifier for the booking (prefixed with `boo-`)                       |
| `object`                | string    | Always `"booking"`                                                             |
| `reference`             | string    | Auto-generated unique booking reference                                        |
| `status`                | string    | The booking status (e.g. `is_confirmed`, `is_pending`)                         |
| `name`                  | string    | Name of the primary guest                                                      |
| `email`                 | string    | Email of the primary guest                                                     |
| `size`                  | integer   | Total number of guests on the booking (includes the primary guest)             |
| `start_time_utc`        | timestamp | Booking start time in UTC                                                      |
| `start_time_local`      | timestamp | Booking start time in the event's local time zone                              |
| `url`                   | string    | URL to manage the booking                                                      |
| `invite_id`             | string    | Public ID of the associated invite (only present if booking was made via invite)|
| `event`                 | object    | The full event object the booking belongs to                                   |
| `guests`                | array     | Guest objects included on the booking (see Guest object)                       |
| `tickets`               | array     | Ticket/extra line items on the booking (see Booking product object)            |
| `tags`                  | array     | Tags assigned directly to the booking                                          |
| `resource_tags`         | array     | Tags from the invite's resource (only present if booking was made via invite)  |
| `event_resource_tags`   | array     | Tags assigned to the event this booking belongs to                             |
| `agree_to_mailing_list` | boolean   | Whether the primary guest opted into the mailing list                          |
| `meta_data`             | object    | Custom key-value metadata assigned to the booking                              |
| `external_id`           | string    | External ID for integrating with other systems                                 |
| `created_at`            | timestamp | When the booking was created (UTC)                                             |
| `updated_at`            | timestamp | When the booking was last updated (UTC)                                        |

### Guest object

Each guest within a booking has the following shape:

| Attribute         | Type    | Description                                                |
| ----------------- | ------- | ---------------------------------------------------------- |
| `id`              | string  | Unique guest ID (prefixed with `gue-`)                     |
| `object`          | string  | Always `"guest"`                                           |
| `name`            | string  | Guest name                                                 |
| `email`           | string  | Guest email (may be `null` for non-lead guests)            |
| `lead`            | boolean | `true` if this is the primary/lead guest                   |
| `wallet_pass_url` | string  | URL to download a digital wallet pass for this guest       |

### Booking product object

Each ticket/extra line item on a booking:

| Attribute        | Type    | Description                                                                 |
| ---------------- | ------- | --------------------------------------------------------------------------- |
| `price_in_cents` | object  | Price with `cents` (integer) and `currency_iso` (string, e.g. `"GBP"`)     |
| `quantity`       | integer | Number of this product included in the booking                              |

## Retrieve a booking

```shell
curl "https://app.artsvp.com/api/v3/bookings/boo-46d60ac0-bd1a-4e11-a3e2-0394e872887f" \
  -H "Authorization: my_api_key"
```

```javascript
const response = await fetch(
  "https://app.artsvp.com/api/v3/bookings/boo-46d60ac0-bd1a-4e11-a3e2-0394e872887f",
  { headers: { "Authorization": "my_api_key" } }
);
const booking = await response.json();
```

```python
import requests

response = requests.get(
    "https://app.artsvp.com/api/v3/bookings/boo-46d60ac0-bd1a-4e11-a3e2-0394e872887f",
    headers={"Authorization": "my_api_key"}
)
booking = response.json()
```

> The above command returns the full booking object as described above.

Retrieves the details of a booking, including its event, guests, tickets, and metadata.

### HTTP Request

`GET https://app.artsvp.com/api/v3/bookings/:id`

### Parameters

| Parameter  | Required    | Description                                                |
| ---------- | ----------- | ---------------------------------------------------------- |
| `id`       | **`true`**  | The public ID of the booking (prefixed with `boo-`)        |

## Create a booking

```shell
curl -X POST "https://app.artsvp.com/api/v3/bookings" \
  -H "Authorization: my_api_key" \
  -H "Content-Type: application/json" \
  -d '{
    "name": "Guest",
    "email": "guest@artsvp.com",
    "size": 3,
    "start_time": "2025-08-02 13:00",
    "event_id": "eve-869a5361-4a24-43f8-9891-982f3495902d",
    "tickets": [
      {
        "ticket_id": "tik-8d417129-36e0-44e2-ae60-d9eff5c92ae6",
        "quantity": 3
      }
    ],
    "extras": [
      {
        "extra_id": "extra-e3830baf-80ac-4e16-a3f4-6f9e7f46f495",
        "quantity": 1
      }
    ],
    "meta_data": {
      "custom_key": "custom_value"
    }
  }'
```

```javascript
const response = await fetch("https://app.artsvp.com/api/v3/bookings", {
  method: "POST",
  headers: {
    "Authorization": "my_api_key",
    "Content-Type": "application/json"
  },
  body: JSON.stringify({
    name: "Guest",
    email: "guest@artsvp.com",
    size: 3,
    start_time: "2025-08-02 13:00",
    event_id: "eve-869a5361-4a24-43f8-9891-982f3495902d",
    tickets: [
      { ticket_id: "tik-8d417129-36e0-44e2-ae60-d9eff5c92ae6", quantity: 3 }
    ],
    meta_data: { custom_key: "custom_value" }
  })
});
const booking = await response.json();
```

```python
import requests

response = requests.post(
    "https://app.artsvp.com/api/v3/bookings",
    headers={
        "Authorization": "my_api_key",
        "Content-Type": "application/json"
    },
    json={
        "name": "Guest",
        "email": "guest@artsvp.com",
        "size": 3,
        "start_time": "2025-08-02 13:00",
        "event_id": "eve-869a5361-4a24-43f8-9891-982f3495902d",
        "tickets": [
            {"ticket_id": "tik-8d417129-36e0-44e2-ae60-d9eff5c92ae6", "quantity": 3}
        ],
        "meta_data": {"custom_key": "custom_value"}
    }
)
booking = response.json()
```

> The above command returns the full booking object on success.

Creates a booking for a specific event. Bookings can include tickets, extras, and custom metadata.

If the booking includes paid products, the booking will be created with `is_pending` status and a payment record will be generated. Free bookings are created with `is_confirmed` status.

The booking `size` is automatically calculated from the sum of ticket quantities. The `end_time` is computed based on the event's validity period.

### HTTP Request

`POST https://app.artsvp.com/api/v3/bookings`

### Parameters

| Parameter        | Required   | Description                                                                                                                                                |
| ---------------- | ---------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `name`           | **`true`** | Name of the primary guest                                                                                                                                  |
| `email`          | `false`    | Email of the primary guest                                                                                                                                 |
| `start_time`     | **`true`** | Start time of the booking in `YYYY-MM-DD HH:MM` format (e.g. `"2025-08-02 13:00"`)                                                                        |
| `event_id`       | **`true`** | Public ID of the event (prefixed with `eve-`)                                                                                                              |
| `size`           | **`true`** | Number of guests. Will be recalculated from ticket quantities if tickets are provided                                                                      |
| `tickets`        | `false`    | Array of ticket objects with `ticket_id` (string, prefixed with `tik-`) and `quantity` (integer). Find ticket IDs from the event object                    |
| `extras`         | `false`    | Array of extra objects with `extra_id` (string, prefixed with `extra-`) and `quantity` (integer). Find extra IDs from the event object                     |
| `meta_data`      | `false`    | JSON object of custom key-value pairs. Maximum size: 1024 bytes. QR codes should be a comma-separated string and must match the booking size               |
| `claim_gift_aid` | `false`    | Set to `"1"` to claim gift aid — adds `"gift_aid": true` to `meta_data`                                                                                   |
| `donation_cents` | `false`    | Donation amount in the smallest currency unit. Added as a line item on the booking payment                                                                 |

## Update a booking

```shell
curl -X PATCH "https://app.artsvp.com/api/v3/bookings/boo-46d60ac0-bd1a-4e11-a3e2-0394e872887f" \
  -H "Authorization: my_api_key" \
  -H "Content-Type: application/json" \
  -d '{
    "name": "Updated Name",
    "external_id": "test123",
    "meta_data": "{\"custom_key\": \"new_value\"}"
  }'
```

```javascript
const response = await fetch(
  "https://app.artsvp.com/api/v3/bookings/boo-46d60ac0-bd1a-4e11-a3e2-0394e872887f",
  {
    method: "PATCH",
    headers: {
      "Authorization": "my_api_key",
      "Content-Type": "application/json"
    },
    body: JSON.stringify({
      name: "Updated Name",
      external_id: "test123"
    })
  }
);
const booking = await response.json();
```

```python
import requests

response = requests.patch(
    "https://app.artsvp.com/api/v3/bookings/boo-46d60ac0-bd1a-4e11-a3e2-0394e872887f",
    headers={
        "Authorization": "my_api_key",
        "Content-Type": "application/json"
    },
    json={
        "name": "Updated Name",
        "external_id": "test123"
    }
)
booking = response.json()
```

> The above command returns the full updated booking object.

Updates a booking's whitelisted attributes. Only the fields you include in the request will be changed; omitted fields retain their current values.

### HTTP Request

`PATCH https://app.artsvp.com/api/v3/bookings/:id`

### Parameters

| Parameter     | Required | Description                                                                 |
| ------------- | -------- | --------------------------------------------------------------------------- |
| `name`        | `false`  | Name of the primary guest                                                   |
| `email`       | `false`  | Email of the primary guest                                                  |
| `meta_data`   | `false`  | JSON string of metadata (max 1024 bytes). Merges with existing metadata     |
| `external_id` | `false`  | External ID assigned to the booking                                         |

## List all bookings

```shell
curl "https://app.artsvp.com/api/v3/bookings?status=is_confirmed&limit=25" \
  -H "Authorization: my_api_key"
```

```javascript
const params = new URLSearchParams({
  status: "is_confirmed",
  limit: "25"
});

const response = await fetch(
  `https://app.artsvp.com/api/v3/bookings?${params}`,
  { headers: { "Authorization": "my_api_key" } }
);
const bookings = await response.json();
```

```python
import requests

response = requests.get(
    "https://app.artsvp.com/api/v3/bookings",
    headers={"Authorization": "my_api_key"},
    params={"status": "is_confirmed", "limit": 25}
)
bookings = response.json()
```

> The above command returns JSON structured like this:

```json
{
  "object": "list",
  "url": "/v3/bookings",
  "has_more": true,
  "last_booking_id": "boo-833995e0-f3a0-4112-be05-8a240d33aeda",
  "data": [
    {
      "id": "boo-493b1bc8-e130-4ff3-96bb-944c85f6b83a",
      "object": "booking",
      "reference": "4707-46C9-4032",
      "status": "is_confirmed",
      "name": "Dan",
      "email": "dan@example.com",
      "size": 3,
      "start_time_utc": "2025-08-02T13:00:00.000Z",
      "start_time_local": "2025-08-02T14:00:00.000+01:00",
      "url": "https://app.artsvp.com/bookings/mcxualjmad19fzyiqgn8gvath3rsdubs",
      "event": {
        "id": "eve-4f26a311-c4e1-460f-b306-43d2af072e2f",
        "object": "event",
        "..."
      },
      "guests": [],
      "tickets": [],
      "tags": [],
      "event_resource_tags": [],
      "agree_to_mailing_list": false,
      "meta_data": {},
      "external_id": null,
      "created_at": "2025-08-01T15:45:03.288Z",
      "updated_at": "2025-08-01T15:45:03.288Z"
    }
  ]
}
```

> Filter by event IDs:

```shell
curl "https://app.artsvp.com/api/v3/bookings?event_ids[]=eve-73bb9047-18e1-4290-b2fa-43ee1abc10af&event_ids[]=eve-0701ddd0-2e2b-446a-b6f1-1c9dcfa71092" \
  -H "Authorization: my_api_key"
```

> Filter by metadata (URL-encoded JSON):

```shell
curl "https://app.artsvp.com/api/v3/bookings?meta_data=%7B%22art%22%3A%22fair%22%7D" \
  -H "Authorization: my_api_key"
```

Returns a paginated list of all bookings your organisation owns. Use query parameters to filter results.

### HTTP Request

`GET https://app.artsvp.com/api/v3/bookings`

### Parameters

| Parameter             | Required | Description                                                                                                                                                      |
| --------------------- | -------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `status`              | `false`  | Filter by booking status: `is_pending`, `is_waitlist`, `is_confirmed`, `is_cancelled`, `is_rejected`, `is_expired`, `is_interested`                              |
| `event_ids`           | `false`  | Array of event public IDs to filter bookings (e.g. `event_ids[]=eve-xxx&event_ids[]=eve-yyy`)                                                                    |
| `external_id`         | `false`  | Filter by external ID                                                                                                                                            |
| `email`               | `false`  | Filter by guest email                                                                                                                                            |
| `reference`           | `false`  | Filter by booking reference                                                                                                                                      |
| `tags`                | `false`  | Filter by booking tags (returns bookings with any matching tag)                                                                                                  |
| `event_resource_tags` | `false`  | Filter by event resource tags (returns bookings whose event has any matching tag)                                                                                |
| `meta_data`           | `false`  | URL-encoded JSON object of key-value pairs to match against booking metadata (max 1024 bytes)                                                                    |
| `starting_after`      | `false`  | Cursor for pagination — fetch the next page after this booking ID                                                                                                |
| `ending_before`       | `false`  | Cursor for pagination — fetch the previous page before this booking ID                                                                                           |
| `limit`               | `false`  | Number of results per page (1–100, default: 50)                                                                                                                  |

# Invites

Invites are the mechanism for granting access to events or collections. They can serve as simple unique invite links or be pre-configured with guest information (`name`, `email`) that gets carried over to the booking. You can control how many times an invite can be used via `max_bookings`.

<aside class="notice">
When a booking is created via an invite, matching data (name, email, meta_data, etc.) is automatically transferred to the booking.
</aside>

## The invite object

> The invite object

```json
{
  "id": "inv-b48ed250-8ebb-448e-a7a0-940d9fcf5dc0",
  "object": "invite",
  "code": "372abb",
  "name": "Guest",
  "email": "guest@artsvp.com",
  "status": "active",
  "guest_count_override": null,
  "golden": false,
  "register_interest": false,
  "url": "https://invite.artsvp.com/372abb",
  "used": false,
  "resource": {
    "id": "eve-c60d5868-c4cf-4b55-83f2-6578a5173767",
    "object": "event",
    "code": "efa400",
    "name": "Summer Party",
    "..."
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

| Attribute              | Type      | Description                                                               |
| ---------------------- | --------- | ------------------------------------------------------------------------- |
| `id`                   | string    | Unique identifier for the invite (prefixed with `inv-`)                   |
| `object`               | string    | Always `"invite"`                                                         |
| `code`                 | string    | Short unique code for the invite URL                                      |
| `name`                 | string    | Name assigned to the invite (carried to booking)                          |
| `email`                | string    | Email assigned to the invite (carried to booking)                         |
| `status`               | string    | Invite status (e.g. `active`, `declined`)                                 |
| `guest_count_override` | integer   | Override for the number of guests allowed (or `null`)                     |
| `golden`               | boolean   | Whether the invite is a "Golden" invite (bypasses capacity limits)        |
| `register_interest`    | boolean   | Whether the invite marks the user as having registered interest           |
| `url`                  | string    | Full invite URL                                                           |
| `used`                 | boolean   | Whether the invite has been used at least once                            |
| `resource`             | object    | The assigned Resource (Event or Collection object)                        |
| `tags`                 | array     | Tags assigned to the invite                                               |
| `resource_tags`        | array     | Tags from the associated resource                                         |
| `meta_data`            | object    | Custom key-value metadata assigned to the invite                          |
| `external_id`          | string    | External ID for integrating with other systems                            |
| `created_at`           | timestamp | When the invite was created (UTC)                                         |
| `updated_at`           | timestamp | When the invite was last updated (UTC)                                    |

## Create an invite

```shell
curl -X POST "https://app.artsvp.com/api/v3/invites" \
  -H "Authorization: my_api_key" \
  -H "Content-Type: application/json" \
  -d '{
    "name": "Lucy Green",
    "email": "lucy@example.com",
    "resource": "eve-c60d5868-c4cf-4b55-83f2-6578a5173767",
    "golden": true,
    "tags": ["artsvp", "vip"],
    "filter": "email"
  }'
```

```javascript
const response = await fetch("https://app.artsvp.com/api/v3/invites", {
  method: "POST",
  headers: {
    "Authorization": "my_api_key",
    "Content-Type": "application/json"
  },
  body: JSON.stringify({
    name: "Lucy Green",
    email: "lucy@example.com",
    resource: "eve-c60d5868-c4cf-4b55-83f2-6578a5173767",
    golden: true,
    tags: ["artsvp", "vip"],
    filter: "email"
  })
});
const invite = await response.json();
```

```python
import requests

response = requests.post(
    "https://app.artsvp.com/api/v3/invites",
    headers={
        "Authorization": "my_api_key",
        "Content-Type": "application/json"
    },
    json={
        "name": "Lucy Green",
        "email": "lucy@example.com",
        "resource": "eve-c60d5868-c4cf-4b55-83f2-6578a5173767",
        "golden": True,
        "tags": ["artsvp", "vip"],
        "filter": "email"
    }
)
invite = response.json()
```

> The above command returns the invite object with HTTP status `201 Created`:

```json
{
  "id": "inv-b48ed250-8ebb-448e-a7a0-940d9fcf5dc0",
  "object": "invite",
  "code": "468d2w",
  "name": "Lucy Green",
  "email": "lucy@example.com",
  "status": "active",
  "guest_count_override": null,
  "golden": true,
  "register_interest": false,
  "url": "https://invite.artsvp.com/468d2w",
  "used": false,
  "resource": {
    "id": "eve-c60d5868-c4cf-4b55-83f2-6578a5173767",
    "object": "event",
    "code": "efa400",
    "name": "Summer Party",
    "..."
  },
  "tags": ["artsvp", "vip"],
  "resource_tags": [],
  "meta_data": {},
  "external_id": null,
  "created_at": "2021-10-21T15:22:35.758Z",
  "updated_at": "2021-10-21T15:33:13.889Z"
}
```

Creates an invite for a specific resource (event or collection).

Use the **filter** parameter to prevent duplicate invites. When a filter is provided, the API checks if an invite already exists on the same resource with a matching value for the filter field. If a match is found, the existing invite is returned instead of creating a new one.

### HTTP Request

`POST https://app.artsvp.com/api/v3/invites`

### Specifying the resource

You can identify the target resource in two ways:

1. **Using `resource`** — Pass the full public ID of the resource (e.g. `eve-c60d5868-...` or `col-ab281a7f-...`). The type is automatically detected from the prefix.
2. **Using `resource_id` + `resource_type`** — Pass the short code (e.g. `3f10ed`) and optionally the type (`event` or `collection`). Defaults to `event` if `resource_type` is omitted.

### Parameters

| Parameter              | Required   | Description                                                                                                                                  |
| ---------------------- | ---------- | -------------------------------------------------------------------------------------------------------------------------------------------- |
| `resource`             | see above  | Full public ID of the resource (e.g. `eve-xxx` or `col-xxx`)                                                                                |
| `resource_id`          | see above  | Short code of the resource                                                                                                                   |
| `resource_type`        | `false`    | Type of resource: `event` (default) or `collection`. Only needed with `resource_id`                                                          |
| `name`                 | `false`    | Name to assign to the invite                                                                                                                 |
| `email`                | `false`    | Email to assign to the invite                                                                                                                |
| `external_id`          | `false`    | External ID to assign to the invite                                                                                                          |
| `golden`               | `false`    | Set to `true` to make this a Golden invite (bypasses capacity limits)                                                                        |
| `register_interest`    | `false`    | Set to `true` to mark the invite as "register interest"                                                                                      |
| `max_bookings`         | `false`    | Maximum number of bookings this invite can generate. `0` = unlimited. Default: `1`                                                           |
| `guest_count_override` | `false`    | Override the default guest count allowed per booking                                                                                         |
| `tags`                 | `false`    | Array of tags to assign to the invite (stored lowercase)                                                                                     |
| `meta_data`            | `false`    | JSON string of custom key-value metadata (max 1024 bytes)                                                                                    |
| `filter`               | `false`    | Prevent duplicates by checking for an existing invite matching this field. Options: `name`, `email`, `external_id`                            |

<aside class="notice">
Either <code>resource</code> or <code>resource_id</code> must be provided. If neither is given, a 400 error is returned.
</aside>

## Retrieve an invite

```shell
curl "https://app.artsvp.com/api/v3/invites/inv-b48ed250-8ebb-448e-a7a0-940d9fcf5dc0" \
  -H "Authorization: my_api_key"
```

```javascript
const response = await fetch(
  "https://app.artsvp.com/api/v3/invites/inv-b48ed250-8ebb-448e-a7a0-940d9fcf5dc0",
  { headers: { "Authorization": "my_api_key" } }
);
const invite = await response.json();
```

```python
import requests

response = requests.get(
    "https://app.artsvp.com/api/v3/invites/inv-b48ed250-8ebb-448e-a7a0-940d9fcf5dc0",
    headers={"Authorization": "my_api_key"}
)
invite = response.json()
```

> The above command returns the invite object. If the invite has been used, associated bookings are also included:

```json
{
  "id": "inv-b48ed250-8ebb-448e-a7a0-940d9fcf5dc0",
  "object": "invite",
  "code": "468d2w",
  "name": "Lucy Green",
  "email": "lucy@example.com",
  "status": "active",
  "guest_count_override": null,
  "golden": true,
  "register_interest": false,
  "url": "https://invite.artsvp.com/468d2w",
  "used": true,
  "resource": {
    "id": "eve-c60d5868-c4cf-4b55-83f2-6578a5173767",
    "object": "event",
    "code": "efa400",
    "name": "Summer Party",
    "..."
  },
  "tags": ["artsvp", "vip"],
  "resource_tags": [],
  "meta_data": {},
  "external_id": null,
  "created_at": "2021-10-21T15:22:35.758Z",
  "updated_at": "2021-10-21T15:33:13.889Z",
  "bookings": [
    {
      "id": "boo-46d60ac0-bd1a-4e11-a3e2-0394e872887f",
      "object": "booking",
      "reference": "C922-4EBA-1F8C",
      "status": "is_confirmed",
      "..."
    }
  ]
}
```

Retrieves the details of an invite. This endpoint provides more detail than the list view by including associated bookings (if the invite has been used).

### HTTP Request

`GET https://app.artsvp.com/api/v3/invites/:id`

### Parameters

| Parameter | Required   | Description                                             |
| --------- | ---------- | ------------------------------------------------------- |
| `id`      | **`true`** | The public ID of the invite (prefixed with `inv-`)      |

## Update an invite

```shell
curl -X PATCH "https://app.artsvp.com/api/v3/invites/inv-b48ed250-8ebb-448e-a7a0-940d9fcf5dc0" \
  -H "Authorization: my_api_key" \
  -H "Content-Type: application/json" \
  -d '{
    "max_bookings": 10,
    "name": "Lucy Green-Smith"
  }'
```

```javascript
const response = await fetch(
  "https://app.artsvp.com/api/v3/invites/inv-b48ed250-8ebb-448e-a7a0-940d9fcf5dc0",
  {
    method: "PATCH",
    headers: {
      "Authorization": "my_api_key",
      "Content-Type": "application/json"
    },
    body: JSON.stringify({
      max_bookings: 10,
      name: "Lucy Green-Smith"
    })
  }
);
const invite = await response.json();
```

```python
import requests

response = requests.patch(
    "https://app.artsvp.com/api/v3/invites/inv-b48ed250-8ebb-448e-a7a0-940d9fcf5dc0",
    headers={
        "Authorization": "my_api_key",
        "Content-Type": "application/json"
    },
    json={
        "max_bookings": 10,
        "name": "Lucy Green-Smith"
    }
)
invite = response.json()
```

> The above command returns the updated invite object.

Updates an invite's whitelisted attributes. Only the fields you include in the request will be changed.

### HTTP Request

`PATCH https://app.artsvp.com/api/v3/invites/:id`

### Parameters

| Parameter              | Required | Description                                                                                                     |
| ---------------------- | -------- | --------------------------------------------------------------------------------------------------------------- |
| `name`                 | `false`  | Name of the invite                                                                                              |
| `email`                | `false`  | Email of the invite                                                                                             |
| `tags`                 | `false`  | Array of tags — **overwrites** existing tags entirely (stored lowercase)                                        |
| `max_bookings`         | `false`  | Maximum bookings allowed. `0` = unlimited                                                                       |
| `guest_count_override` | `false`  | Override guest count per booking. Pass `null` to clear                                                          |
| `golden`               | `false`  | Set to `true` or `false` to enable/disable Golden invite status                                                 |
| `register_interest`    | `false`  | Set to `true` or `false` to enable/disable register interest                                                    |
| `external_id`          | `false`  | External ID to assign to the invite                                                                             |
| `meta_data`            | `false`  | JSON string of metadata (max 1024 bytes). Replaces existing metadata                                            |

## Delete an invite

```shell
curl -X DELETE "https://app.artsvp.com/api/v3/invites/inv-b48ed250-8ebb-448e-a7a0-940d9fcf5dc0" \
  -H "Authorization: my_api_key"
```

```javascript
const response = await fetch(
  "https://app.artsvp.com/api/v3/invites/inv-b48ed250-8ebb-448e-a7a0-940d9fcf5dc0",
  {
    method: "DELETE",
    headers: { "Authorization": "my_api_key" }
  }
);
// Response: 204 No Content
```

```python
import requests

response = requests.delete(
    "https://app.artsvp.com/api/v3/invites/inv-b48ed250-8ebb-448e-a7a0-940d9fcf5dc0",
    headers={"Authorization": "my_api_key"}
)
# response.status_code == 204
```

> Returns HTTP status `204 No Content` on success.

Permanently deletes an invite.

### HTTP Request

`DELETE https://app.artsvp.com/api/v3/invites/:id`

## Batch create invites

```shell
curl -X POST "https://app.artsvp.com/api/v3/invites/batch" \
  -H "Authorization: my_api_key" \
  -H "Content-Type: application/json" \
  -d '{
    "name": "Lucy Green",
    "email": "lucy@example.com",
    "resource_tags": ["vip"],
    "filter": "email"
  }'
```

```javascript
const response = await fetch("https://app.artsvp.com/api/v3/invites/batch", {
  method: "POST",
  headers: {
    "Authorization": "my_api_key",
    "Content-Type": "application/json"
  },
  body: JSON.stringify({
    name: "Lucy Green",
    email: "lucy@example.com",
    resource_tags: ["vip"],
    filter: "email"
  })
});
const result = await response.json();
```

```python
import requests

response = requests.post(
    "https://app.artsvp.com/api/v3/invites/batch",
    headers={
        "Authorization": "my_api_key",
        "Content-Type": "application/json"
    },
    json={
        "name": "Lucy Green",
        "email": "lucy@example.com",
        "resource_tags": ["vip"],
        "filter": "email"
    }
)
result = response.json()
```

> The above command returns JSON structured like this:

```json
{
  "object": "batch",
  "url": "/v3/invites/batch",
  "data": [
    {
      "id": "inv-b48ed250-8ebb-448e-a7a0-940d9fcf5dc0",
      "object": "invite",
      "code": "372abb",
      "name": "Lucy Green",
      "email": "lucy@example.com",
      "..."
    }
  ],
  "total_events": 1,
  "total_collections": 1,
  "total_resources": 2,
  "total_invites_created": 2
}
```

Creates invites for **all resources** (events and collections) that match the given `resource_tags`. This is useful for creating the same invite across many events at once.

The request body **must** be sent as JSON (`Content-Type: application/json`).

### HTTP Request

`POST https://app.artsvp.com/api/v3/invites/batch`

### Parameters

| Parameter           | Required   | Description                                                                                                                                  |
| ------------------- | ---------- | -------------------------------------------------------------------------------------------------------------------------------------------- |
| `resource_tags`     | **`true`** | Array of tags — invites are created for all resources matching any of these tags                                                              |
| `name`              | `false`    | Name to assign to all created invites                                                                                                        |
| `email`             | `false`    | Email to assign to all created invites                                                                                                       |
| `external_id`       | `false`    | External ID to assign to all created invites                                                                                                 |
| `golden`            | `false`    | Whether the invites should be Golden                                                                                                         |
| `register_interest` | `false`    | Whether the invites should have register interest enabled                                                                                    |
| `max_bookings`      | `false`    | Maximum bookings per invite. `0` = unlimited. Default: `1`                                                                                   |
| `tags`              | `false`    | Tags to assign to all created invites (stored lowercase)                                                                                     |
| `meta_data`         | `false`    | JSON string of metadata to assign to all created invites (max 1024 bytes)                                                                    |
| `filter`            | `false`    | Prevent duplicates per resource. Options: `name`, `email`, `external_id`                                                                     |

<aside class="warning">
Do <strong>not</strong> pass <code>resource_type</code> or <code>resource_id</code> in a batch request — these will cause the request to fail. The resource is determined solely by <code>resource_tags</code>.
</aside>

## List all invites

```shell
curl "https://app.artsvp.com/api/v3/invites?resource_id=3f10ed&limit=25" \
  -H "Authorization: my_api_key"
```

```javascript
const params = new URLSearchParams({
  resource_id: "3f10ed",
  limit: "25"
});

const response = await fetch(
  `https://app.artsvp.com/api/v3/invites?${params}`,
  { headers: { "Authorization": "my_api_key" } }
);
const invites = await response.json();
```

```python
import requests

response = requests.get(
    "https://app.artsvp.com/api/v3/invites",
    headers={"Authorization": "my_api_key"},
    params={"resource_id": "3f10ed", "limit": 25}
)
invites = response.json()
```

> The above command returns JSON structured like this:

```json
{
  "object": "list",
  "url": "/v3/invites",
  "has_more": true,
  "last_invite_id": "inv-833995e0-f3a0-4112-be05-8a240d33aeda",
  "data": [
    {
      "id": "inv-b48ed250-8ebb-448e-a7a0-940d9fcf5dc0",
      "object": "invite",
      "code": "372abb",
      "name": "Guest",
      "email": "guest@artsvp.com",
      "status": "active",
      "guest_count_override": null,
      "golden": false,
      "register_interest": false,
      "url": "https://invite.artsvp.com/372abb",
      "used": false,
      "resource": {
        "id": "eve-c60d5868-c4cf-4b55-83f2-6578a5173767",
        "object": "event",
        "code": "efa400",
        "name": "Summer Party",
        "..."
      },
      "tags": ["single_entry", "vip_1"],
      "resource_tags": ["new_user"],
      "meta_data": {},
      "external_id": "",
      "created_at": "2021-10-21T15:22:35.758Z",
      "updated_at": "2021-10-21T15:33:13.889Z"
    }
  ]
}
```

> Filter by status:

```shell
curl "https://app.artsvp.com/api/v3/invites?status=active" \
  -H "Authorization: my_api_key"
```

> Filter by metadata (URL-encoded JSON):

```shell
curl "https://app.artsvp.com/api/v3/invites?meta_data=%7B%22art%22%3A%22fair%22%7D" \
  -H "Authorization: my_api_key"
```

Returns a paginated list of all invites your organisation owns. Results can be scoped to a specific resource or filtered by various parameters.

### HTTP Request

`GET https://app.artsvp.com/api/v3/invites`

### Parameters

| Parameter        | Required | Description                                                                                                            |
| ---------------- | -------- | ---------------------------------------------------------------------------------------------------------------------- |
| `resource`       | `false`  | Full public ID of a resource to scope results (e.g. `eve-xxx` or `col-xxx`)                                            |
| `resource_id`    | `false`  | Short code of a resource to scope results                                                                              |
| `resource_type`  | `false`  | Type of resource: `event` (default) or `collection`. Only needed with `resource_id`                                    |
| `status`         | `false`  | Filter by invite status: `active` or `declined`                                                                        |
| `external_id`    | `false`  | Filter by external ID                                                                                                  |
| `name`           | `false`  | Filter by invite name (exact match)                                                                                    |
| `email`          | `false`  | Filter by invite email (exact match)                                                                                   |
| `code`           | `false`  | Filter by invite code (exact match)                                                                                    |
| `tags`           | `false`  | Filter by invite tags (returns invites with any matching tag, lowercase)                                               |
| `resource_tags`  | `false`  | Filter by resource tags (returns invites whose resource has any matching tag)                                           |
| `meta_data`      | `false`  | URL-encoded JSON object of key-value pairs to match against invite metadata (max 1024 bytes)                           |
| `starting_after` | `false`  | Cursor for pagination — fetch the next page after this invite ID                                                       |
| `ending_before`  | `false`  | Cursor for pagination — fetch the previous page before this invite ID                                                  |
| `limit`          | `false`  | Number of results per page (1–100, default: 50)                                                                        |

# Webhooks

Webhook endpoints can be configured to receive real-time notifications for key events. Configure your webhooks on the [organisation developer page](https://app.artsvp.com/settings/developer).

## The webhook endpoint

The webhook endpoint configuration includes:

- **Destination URL** — where webhook events are sent
- **Signing secret** — for verifying webhook authenticity
- **API version** — determines the payload format
- **Status** — `Active` or `Inactive`
- **Event types** — which notifications to receive

## Response requirements & retry policy

Your endpoint **must return a `200` response** for each webhook delivery. Any other response is logged as a failed attempt.

Failed deliveries are retried up to **9 additional times** (10 total attempts) with exponentially increasing intervals. After **10 consecutive failed notifications** (100 failed attempts total), the endpoint is automatically set to **Error** status.

| Endpoint Status | Behavior                                                                                 |
| --------------- | ---------------------------------------------------------------------------------------- |
| **Active**      | Receives all subscribed events                                                           |
| **Error**       | Stops sending but continues recording events. Contact support@artsvp.com to reactivate   |
| **Inactive**    | No events are sent or recorded                                                           |

## The outbound webhook event object

> The outbound webhook event object

```json
{
  "id": "owe-cdcaf11d-0086-43ea-9eb0-39d3c434f508",
  "object": "webhook_event",
  "event_type": "booking.confirmed",
  "api_version": "v3",
  "organisation": "demo",
  "data": {
    "id": "boo-46d60ac0-bd1a-4e11-a3e2-0394e872887f",
    "object": "booking",
    "status": "is_confirmed",
    "..."
  }
}
```

Each webhook event is delivered as a JSON payload with the following structure:

| Field          | Type   | Description                                                      |
| -------------- | ------ | ---------------------------------------------------------------- |
| `id`           | string | Unique webhook event ID (prefixed with `owe-`)                   |
| `object`       | string | Always `"webhook_event"`                                         |
| `event_type`   | string | The type of event that triggered the webhook (see below)         |
| `api_version`  | string | API version used for the payload format (e.g. `"v3"`)            |
| `organisation` | string | Organisation subdomain                                           |
| `data`         | object | The resource object in its documented format                     |

### Supported event types

| Event Type          | Description                                    |
| ------------------- | ---------------------------------------------- |
| `booking.created`   | A new booking was created                      |
| `booking.confirmed` | A booking was confirmed                        |
| `booking.updated`   | A booking was updated                          |
| `booking.cancelled` | A booking was cancelled                        |
| `event.created`     | A new event was created                        |
| `event.updated`     | An event was updated                           |
| `event.cancelled`   | An event was cancelled                         |
| `event.published`   | An event was published                         |

Where applicable, the `data` field includes a `previous_attributes` object showing what changed.
