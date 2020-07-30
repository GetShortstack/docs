# Getting Started Guide

Use this quickstart guide to jump into Shortstack. If you have any lingering questions, [please e-mail us](nader+docs@getshortstack.com)

# Shortstack Endpoints

Every endpoint you write is a function called `main` that gets run when the endpoint is hit:

`def main(params, state, request):`

## main

- `main` is the function that the endpoint executes. Don't
  rename it :) You can add as many helper functions as you want, but `main` is the entry point.

## Shared Code

You'll notice each endpoint has two tabs. The `main` endpoint is under _*Endpoint Code*_. _Shared Code_ belongs to the project level, and can be accessed by any endpoint under that project.

![image](static/docsMedia/sharedCode1.png ":size=550")

You can access the shared code via the `shared` object in any endpoint. So the following will return pancakes

```python
shared.breakfast # will return "pancakes"
```

## params

- `params` is a [python dictionary](https://docs.python.org/3/tutorial/datastructures.html#dictionaries) containing your incoming query or body arguments. `params.get("incoming")` gets your argument named
  incoming, or `None` if it doesn't exist.

  ```python
  # Example:
  # /your_endpoint?incoming=shortstack
  params.get("incoming") # returns "shortstack"

  # /your_endpoint
  params.get("incoming") # returns None

  # /your_endpoint
  params.get("incoming", "none provided") # returns "none provided" if incoming doesn't exist

  ```

## variables

- `variables` is the Shortstack secret/environment variables manager. You can access it [here](https://app.getshortstack.com/variables)

  1. Add any environment variable or secret on the Variables page. Note: these variables are protected per project.
     ![image](static/docsMedia/variables1.png ":size=550")

  2. You can use any of your variables by the `variables` object in your endpoint code.
     For example:

  ```python
    variables.THIS_IS_MY_KEY
  ```

## storage

- `storage` is a dictionary for you to persist data. It's a bootstrapped database inspired by localStorage. It's available across all of your endpoints. Go to [Your Storage](https://app.getshortstack.com/storage) to initialize or edit objects. For example, let's create a new list to store phone numbers:

  1. Hover your cursor over storage and click on the '+' icon that appears  
     ![image](static/docsMedia/step1.png ":size=550")
  2. Name this list. We'll call it numbers  
     ![image](static/docsMedia/step2.png ":size=550")
  3. Note the data type defaults to NULL. Hover your cursor over NULL and click on the edit(pencil) that appears  
     ![image](static/docsMedia/step3.png ":size=550")
  4. Replace null with empty brackets for a list and click the purple check on the right. Make sure the click the check next to [ ... ] to save the data type as a list instead of string  
     ![image](static/docsMedia/step4.png ":size=550")

  Yay! Your storage now has a list called numbers ready to use :)
  In an endpoint, the following code would add phone numbers to the list!

  ```python
  # /endpoint?number=415555555
  state["numbers"].append(params.get("number"))
  ```

## Endpoint Responses

Every endpoint ends with the following code block:

```python
  # your endpoint response
  response_body = {}
  status_code = 200
  return response_body, status_code
```

- `response_body` is a [python dictionary](https://docs.python.org/3/tutorial/datastructures.html#dictionaries) that your endpoint returns as JSON. You can put anything here to return it.
- `status_code` is the HTTP status code to return. 2XX is success, 3XX is redirection, 4XX is client error, and 5XX is server error. [read more on status codes here](https://www.restapitutorial.com/httpstatuscodes.html)
- `request` is a [flask request object](https://kite.com/python/docs/flask.Request).

# Shortstack Out-of-the-Box

Shortstack comes with many services out-of-the-box! Rather than waste time making accounts, managing multiple bills, configuring services, etc, you can just code :)

## SMS

- `sms(number: string, message: string)` is a simple way to send SMS messages. It comes from a real 10-digit number (not a short code). 2way texting support coming soon!

  ```python
  # Example:
  sms("415555555", "hello from shortstack!")

  ```

  Or try it with a query parameter!

  ```python
  def main(params, state, request):
      # /endpoint?number=415555555
      sms(params.get("number"), "hello from shortstack!")

      # your endpoint response
      response_body = {sent: True} #confirm that you sent the text
      status_code = 200
      return response_body, status_code
  ```

## Upload Files

- `upload_blob(blob)` is a file uploader. Call this function with the blob and it will return a URL to access it. You can store this URL in state or simply return it from the endpoint.

```python
# POST /endpoint

def main(params, state, request):
    # your code here
    file = request.files[“file”] # process the file from the request
    file_bytes = file.read()
    link = upload_blob(file_bytes) # call the Shortstack upload_blob function which returns a url
    # your endpoint response
    response_body = {“link”: link}
    status_code = 200
    return response_body, status_code
```

# [Release Notes](/release.md)
