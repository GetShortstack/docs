# Shortstack Endpoints

Every endpoint you write is a function called `main` that gets run when the endpoint is hit:

`def main(params, state, request):`
  

  ## main
  * `main` is the function that the endpoint executes. Don't
    rename it :) You can add as many helper functions as you want, but `main` is the entry point.

  ## params
  * `params` is a dictionary containing your incoming query or body arguments. `params.get("incoming")` gets your argument named
    incoming, or `None` if it doesn't exist.

    ```
    # Example:
    # /your_endpoint?incoming=shortstack
    params.get("incoming") # returns "shortstack"

    # /your_endpoint
    params.get("incoming") # returns None
    ```

  ## storage
  * `storage` is a dictionary for you to persist data. It's a bootstrapped database inspired by localStorage. It's available across all of your endpoints. Go to [Your Storage](https://app.getshortstack.com/storage) to declare how you want to store data. For example, let's create a new list to store phone numbers:
    ![image](static/docsMedia/step1.png ":size=350")
    _Hover your cursor over storage and click on the '+' icon that appears_
    ![image](static/docsMedia/step2.png ":size=350")
    _Name this list. We'll call it numbers_
    ![image](static/docsMedia/step3.png ":size=350")
    _Note the data type defaults to NULL. Hover your cursor over NULL and click on the edit(pencil) that appears._
    ![image](static/docsMedia/step4.png ":size=350")
    _Replace null with empty brackets for a list and click the purple check on the right. Make sure the click the check next to [ ... ] to save the data type as a list instead of string_

    Yay! Your storage now has a list called numbers ready to use :)
    In an endpoint, the following code would add phone numbers to the list!
    ```
    # /endpoint?number=415555555
    state["numbers"].append(params.get("number"))
    ```
  ## Endpoint Responses

  Every endpoint ends with the following code block:

  ```
    # your endpoint response
    response_body = {}
    status_code = 200
    return response_body, status_code
  ```
  * `response_body` is the json object your endpoint returns. You can put anything here to return it.
  * `status_code` is the HTTP status code to return. 2XX is success, 3XX is redirection, 4XX is client error, and 5XX is server error. [read more on status codes here](https://www.restapitutorial.com/httpstatuscodes.html)
  * `request` is the [full request object](https://kite.com/python/docs/flask.Request). You probably won't need this, but it's there in case you do.

# Shortstack Out-of-the-Box

Shortstack comes with many services out-of-the-box! Rather than waste time making accounts, managing multiple billing accounts, configuring services, etc, you can just code :)
  
  ## SMS
  * `sms(number: string, message: string)` is a simple way to send SMS messages. It comes from a real 10-digit number (not a short code). 2way texting support coming soon!
    ```
    # Example:
    sms("415555555", "hello from shortstack!")

    ```
    Or try it with a query parameter!
    ```
    # /endpoint?number=415555555
    sms(params.get("number"), "hello from shortstack!")
    ```

  ## Upload Files
  * `upload_blob(blob)` is a file uploader. Call this function with the blob and it will return a URL to access it. You can store this URL in state or simply return it from the endpoint.
