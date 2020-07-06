# Shortstack Docs

There are two parts to the docs. Objects you have access to and out-of-the-box functionality

<!-- > An awesome project. -->

## Shortstack Objects

Every endpoint you write has the same signature:

<!-- `def main(params, state, request):` -->

```
def main(params, state, request):
```

- `main` is the function that the endpoint executes. Don't
  rename it :) You can add as many helper functions as you want, but `main` is the entry point.
- `params` contains your incoming query or body arguments.
  `params.get("incoming")` gets your argument named
  incoming, or `None` if it doesn't exist.
- `state` is a dictionary for you to persist data in between
  calls. Think of it like a bootstrapped database.
- `request` is the [full request object](https://kite.com/python/docs/flask.Request)

Every endpoint ends with

```
  # your endpoint response
  response_body = {}
  status_code = 200
  return response_body, status_code
```

- `response_body` is the json object your endpoint returns
- `status_code` is the [HTTP status code](https://www.restapitutorial.com/httpstatuscodes.html) to return.

## Shortstack Out-of-the-Box

Shortstack comes with many services out-of-the-box! Rather than waste time making accounts, managing multiple billing accounts, configuring services, etc, you can just code :)

- `sms(number: string, message: string)` is a simple way to send SMS messages. It comes from a real 10-digit number (not a short code). 2way texting support coming soon!

- `upload_blob(blob)` is a file uploader. Call this function with the blob and it will return a URL to access it. You can store this URL in state or simply return it from the endpoint.
