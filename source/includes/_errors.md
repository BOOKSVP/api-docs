# Errors

The ARTSVP API uses conventional HTTP response codes to indicate the success or failure of a request. Codes in the `2xx` range indicate success, `4xx` codes indicate a client error, and `5xx` codes indicate a server-side issue.

## HTTP status codes

| Code | Name                  | Description                                                                       |
| ---- | --------------------- | --------------------------------------------------------------------------------- |
| 200  | OK                    | Request succeeded                                                                 |
| 201  | Created               | Resource was successfully created (e.g. invite creation)                          |
| 204  | No Content            | Request succeeded with no response body (e.g. delete operations)                  |
| 400  | Bad Request           | The request is missing required parameters                                        |
| 401  | Unauthorized          | Invalid or missing API key                                                        |
| 403  | Forbidden             | The request was rejected (e.g. invalid URL or internal IP)                        |
| 404  | Not Found             | The requested resource does not exist or the endpoint is invalid                  |
| 422  | Unprocessable Entity  | The request was well-formed but contained invalid data (e.g. limit out of range)  |
| 429  | Too Many Requests     | Rate limit exceeded. Wait for the cooldown period before retrying                 |
| 500  | Internal Server Error | Something went wrong on our end. Try again later                                  |
| 503  | Service Unavailable   | We're temporarily offline for maintenance. Please try again later                 |

## Error response format

> Example error responses:

```json
{
  "error": "Access denied due to invalid credentials"
}
```

```json
{
  "error": "Invalid booking provided, please verify booking is valid"
}
```

```json
{
  "error": "Too many requests fired within time limit. Please wait 5 minute(s)"
}
```

```json
{
  "error": "Element request limit exceeded, please request between 1 to 100 elements"
}
```

```json
{
  "error": "Missing parameter required. Please provide name in your request"
}
```

```json
{
  "error": "Invalid format for JSON used with meta_data"
}
```

```json
{
  "errors": ["Name can't be blank", "Email is invalid"]
}
```

Error responses return a JSON object with either:

- `"error"` — a single error message string (most common)
- `"errors"` — an array of validation error messages (for record validation failures)
