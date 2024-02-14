# Errors

The ARTSVP API uses the following error codes:


Error Code | Meaning
---------- | -------
400 | Bad Request -- The request is invalid.
401 | Unauthorized -- The API key provided is invalid.
403 | Forbidden -- The data requested is for administrators only.
404 | Not Found -- Data could not be found.
429 | Too Many Requests -- Rate limit has been exceeded.
500 | Internal Server Error -- We had a problem with our server. Try again later.
503 | Service Unavailable -- We're temporarily offline for maintenance. Please try again later.
