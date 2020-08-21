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

# Shortstack CLI

If you have a development environment you like, you might prefer to
develop there rather than go configure a new one in the browser. We get that! The Shortstack CLI will allow you to do everything the web app lets you do

- create/delete endpoints
- run them in the remote environment
- view logs
- add packages
- anything\* else you can do in the web app :)

\* almost anything

## Installation

Probably something like

Mac OS:

```zsh
$ brew install stack-cli
```

Windows:

```zsh
$ npm install stack-cli
```

## Getting Started

Now that you have the CLI installed, you'll need to initialize to your stack account.

### Authenticate:

First authenticate yourself

```zsh
stack init
```

### Initialize

Once authenticated, you'll need to initialize

```zsh
stack initialize
```

This will create a local directory at ~/GetShortstack with a folder per projecct. In each project folder, you'll find the endpoints, your shared code, and variables as individual .py files.

### Set Environment

Similar to the project dropdown from the web app, you'll need to specify which project you're working with. This allows you to add packages, variables/secrets, and endpoints; respecting each project's isolated runtime.

```zsh
stack set ProjectName
```

You can view active environment settings with the `status` command:

```zsh
stack status
```

NOTE: We highly recommend setting up Zsh & autocomplete. It'll let you tab through commands like butter

## Creating

### Add or Remove

- `Packages`: You can add any package installable by Pip
  ex: to install numpy

```zsh
stack add package numpy
stack remove package numpy
```

- `Endpoints`: You can create new endpoints in the active project

```zsh
stack add endpoint MyEndpointName
stack remove endpoint MyEndpointName
```

- `Variables`: You can add variables/secrets from [the app](https://app.getshortstack.com/variables) or right from the command line

```zsh
stack add variable NewVar
stack remove variable NewVar
```

Then follow the prompt for the variable value. Note: while you can update the value at any time, we never reveal the value for your security.

### Create New Project

You can create a new project with

```zsh
stack new project MyNewProject
```

Note: this does not set it as the active project. You'll need to use `stack set` for that:

```zsh
stack set project MyNewProject
```

## Running

### List

You can use the `list` command to list available projects, packages, and endpoints. See below

View available projects:

```zsh
stack list project
```

View packages added to the environment. You can `remove` them or `add` new ones.

```zsh
stack list package
```

View endpoints and their associated URLs. You can execute them with `run`.

```zsh
stack list endpoint
```

### Run

You can execute any endpoint right from the terminal.

```zsh
stack run MyEndpoint [GET, POST, PUT, DELETE] --args --body *.json
```

The third argument is the HTTP request type.
Add query args with --args, or -a
Add any json file in the request body with --body or -b

To make a GET request:

```zsh
stack run MyEndpoint GET --a  message=Hello
```

Makes a GET call to `/MyEndpoint?message=Hello`

To make a POST request:

```zsh
stack run MyEndpoint POST --b  body.json
```

Makes a POST call to `/MyEndpoint` with the body.json

### Logs

After running an endpoint, Shortstack automatically collects logs and standard out! View them by running the `logs` command:
To view all the logs of a project, run:

```zsh
stack logs
```

You can also view the logs of an individual endpoint

```zsh
stack logs endpoint MyEndpoint
```

## Syncronizing

Now that you've made changes locally, it's time to save them so they're syncronized with Shortstack & the web app.

### Diff

To view a difference of what's syncronized with remote, run `diff`:

```zsh
stack diff
```

### Override

Use `override` to sync the changes.

To override your local changes with those saved to your Shortstack account, override your local:

```zsh
stack override local
```

To override your remote changes with those saved to your local environment, override your remote:

```zsh
stack override remote
```

Note: override only syncronizes your active project. You can set the active project with `set`

# [Release Notes](/release.md)
