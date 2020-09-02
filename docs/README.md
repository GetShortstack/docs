# Shortstack

Shortstack is a development environment that allows you to rapidly build and scale APIs. Shortstack aims to optimize for developer time without the tradeoff of vendor lockin.

**Highlighted Features**:

- **Production ready by default**: Scale without extra configuration or additional knowledge
- **Deployless**: Deployments just happen instantly when you save.
- **Configureless**: We handle the environments and infrastructure. Whether you're coding from the CLI and your local environment or the web app, the environment is ready to go.
- **Ergonomic request/response validation**: Use python3 type hints to define the shape of your request args and body.
- **Openapi by default**: Openapi specification is automatically generated for you as you write and annotate your endpoints
- **Secrets management**: Store your applications securely without extra effort
- **Package management**: Use the power of python's community with 3rd party packages found on pypi
- **Shortstore:** A super simple nosql database to prototype your applications -- backed by Dynamodb.

**Coming Soon**:

Our roadmap is constantly evolving with feedback from our users. Here are some of the things we are thinking about.

- **Eject:** Eject your project at any point in your project to host it yourself and get under the hood. Access all the configuration details and more without worrying about them at the start of your project.
- **Project Environments:** Create multiple different isolated environments to develop or experiment on your APIs ex. (staging, prod, dev)
- **Application monitoring, alerting:** get crash alerts and performance monitoring out of the box or hook up (Sentry, APM, etc).
- **Robust Logging and Querying** Search and query for relevant logs to quickly debug your application

Use this quickstart guide to jump into Shortstack. If you have any lingering questions, [please e-mail us](nader+docs@getshortstack.com)

**Project Status**

[✓] Alpha: We are testing Shortstack with a small set of users.

[✓] Public Alpha: Join our waitlist and we will get you access as soon as we can. We hope you build something great! The platform still has kinks please help us iron them out.

[ ] Public Beta: Anyone can create an account. API becomes stable.

[ ] Public launch: Stable for enterprise use cases.

# User Guide

## Core Concepts

Shortstack lets you create projects. In each project, you'll find:

- Endpoints: Code that responds to http requests.
- Shared Code: Code that can be shared by any endpoint in a project
- Packages: Installable 3rd party packages from PyPi that can be used in endpoints or shared code
- Variables: A secure way to use and manage secrets, keys, passwords and is also a good way to manage environment variables or global constants in your code.
- Storage: A simple key value database. It can be thought of as a persistent dictionary accross requests as its api closely mirrors python's native dict type. It's a great way to build a proof of concept without needing to hook up a database.

## Quickstart

This requires a shorstack account. [Create one here](https://app.getshortstack.com/signup)!

Lets create a hello world project.

1. Navigate to the [endpoints page](https://app.getshortstack.com/endpoints).
2. Create a new endpoint by pressing the plus (+) sign in the side bar.
3. Enter a name for the endpoint we can call it `hello_world`.
4. Write the following python code in the editor.

```python
def get():
  return {"message": "hello world"}
```

5. Press the save endpoint button to deploy your code.
6. Run the endpoint by copying the url at the top of the page and entering it in a new tab or by pressing the play button at the bottom of the page.
   Hint: Your url will look like `https://123456.getshortstack.com/api/_execute/89247f45-d47f-777-b8c9-91a20287faa6`
7. You should see `{"message": "hello world"}` as the response :)

## Defining Endpoints

Hint: Shortstack is built on top of [FastApi](https://fastapi.tiangolo.com)/[Starlette](https://www.starlette.io) and supports much of its API. To dig deeper in request/response, validation and documenation it might be helpful to checkout their docs.

### Handling http methods

- To handle different http methods for a given url, create new functions named with the http request type you would like to handle. The following methods are currently supported.
  - get
  - post
  - put
  - delete
  - options
  - head
  - patch
  - trace

Example:

```python
def get():
  return {"message":"GET"}

def post():
  return {"message": "POST"}
```

### Custom endpoint paths

To change an endpoint's url suffix to something more readable and useful, click on the `edit metadata` tab at the bottom of the editor and modify the url path. Once satisfied press save.

Hint: Endpoints are immediately accessible by its url after it is saved.

### Sending data to your endpoints

There are currently 4 supported ways to send data to your endpoints.

- Path Parameters
- Query Arguments
- Request Body
- Request Files

#### Path params

Path params are parameters encoded as part of the url enclosed with `/`.

Ex:

- `/resource/1`
- `/resource/2/do_something`

To extract the value (ex. 1, 2) for our function to use, we need to modify the endpoint path. Click on the `edit metadata` tab at the bottom of the editor and modify the url path to `/resource/{resource_id}`.

Next add `resource_id` as an argument to your function definition to use it.

```python
def get(resource_id: int):
  return {"resource_id": resource_id}  # {"resource_id": 1}
```

Hint: The type hint of `int` used will typecast the parameter or throw a http 422 error with a message on what was invalid.

#### Query Params

Query params are parameters found at the end of url after `?` They are key value pairs joined with `=` character and separated by `&`.
Ex:

- `/resource?id=1`
- `/resource?type=2&filter=brita`

Simply add the query param names as arguments in the function to use in the endpoint.

```python
def get(type: int):
  return {"type": type}  # {"type": 2}
```

To optionally accept a query parameter define the variable as a python keyword argument.

```python
def get(type: int, filter=None):
  return {"type": type, "filter": filter}  # {"type": 2, "filter": "brita"}
```

#### Request Body

The request body is data sent from a client to your backend. To access and validate a json request body we use [Pydantic](https://pydantic-docs.helpmanual.io) models. The pydantic classes allow us to define what our data should be in plain python3 type hints.

Define your body with a pydantic model then use the class as a type hint for function argument to use the body in the endpoint.

```python
from typing import Optional
from pydantic import BaseModel

# The class for the  body to be matched
class ItemInBody(BaseModel):
  name: str
  email: str
  phone: Optional[str] = None

# add the class as a parameter
def post(item: ItemInBody):
  return item  # {"name": "Alec", "email": "email@mail.com", "phone": "123-456-7890"}
```

To test this we can use a tool like [Postman](https://www.postman.com) or you can use Shorstack's `endpoint runner` which you will find at the bottom of the editor. Select the `json body` editor and add a test request body and make sure the http method selected is `POST`. You can use this as a test request body:

```json
{ "name": "Alec", "email": "email@mail.com", "phone": "123-456-7890" }
```

Press the play button and you should see the `ItemInBody` type sent back as a json response.

Hint: You can nest pydantic models.

Hint: It is best practice to not use the http `GET` method to send response body's. It is undefined behavior in the specifications.

#### Request Files

To receive files sent as form data you can declare a keyword argument with default value of `File(...)`.

```python
from fastapi import File
import file

def post(file_contents: bytes = File(...))

  # We're using our built in file uploader
  # see below for documentation on it
  link = file.upload(file_contents)

  return {"file_url": link}
```

You can specify file type as `UploadFile` to acess file meta data and handle the file more efficiently. See all of `UploadFile`'s [attributes here](https://fastapi.tiangolo.com/tutorial/request-files/#uploadfile).

```python
from fastapi import File, UploadFile
import file

await def post(uploaded_file: UploadFile = File(...))

  file_contents = await uploaded_file.read()

  link = file.upload(file_contents)

  return {"file_url": link, "file_name": uploaded_file.filename}
```

To handle multiple uploaded files, type hint the parameter as a `List`.

```python
from typing import List
from fastapi import File, UploadFile
import file

await def post(uploaded_files: List[UploadFile] = File(...))

  return {"file_names": [f.filename for f in uploaded_files]}
```

### Responses

Http responses are used to send data back to the client. Responses include a body, headers, cookies, and a status code.

As you have seen you can create a response by returning from an endpoint handler. By default the response status code is `200`. The object returned will be used as the body.

To return a custom response you can return a Starlette response object. Read more about [Starlette response objects here](https://www.starlette.io/responses/).

```python
from fastapi.responses import JSONResponse

def post():
  return JSONResponse(status_code=201, content={"message": "created"})
```

## Shared Code

You'll notice each endpoint has two tabs. The `main` endpoint is under _*Endpoint Code*_. _Shared Code_ belongs to the project level, and can be accessed by any endpoint under that project.

![image](static/docsMedia/sharedCode1.png ":size=550")

You can access the shared code via the `shared` object in any endpoint. So the following will return pancakes

```python
shared.breakfast # will return "pancakes"
```

## Variables

- `variables` is the Shortstack secret/environment variables manager. You can access it [here](https://app.getshortstack.com/variables)

  1. Add any environment variable or secret on the Variables page. Note: these variables are protected per project.
     ![image](static/docsMedia/variables1.png ":size=550")

  2. You can use any of your variables by the `variables` object in your endpoint code.
     For example:

  ```python
    variables.THIS_IS_MY_KEY
  ```

## Storage

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

# Shortstack Tools

Shortstack comes with many services and out-of-the-box! Rather than waste time making accounts, managing multiple bills, configuring services, etc, you can just code :)

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

# Examples

## Upload a Image

So let's upload this picture of our puppy ![Toulouse](static/docsMedia/Toulouse.JPG ":size=150")

POST /myEndpoint

```json
{
file: (the file)
}
```

The endpoint will respond with

```json
{
  "uploaded": True,
  "fileName": Toulouse.JPG,
  "fileURL": LINK_TO_TOULOUSE
}
```

Since this was a file upload, we could just add the file directly
as a function paremter `def post(file = File(...))`

## Use of multiple parameter types

If we want to send more meta data alongn with the file, we could use a request body with json. To do so all we need to do is declare a pydantic moddel and use it as a type hint for a function argument.

```python
from pydantic import BaseModel
from fastapi import File

# The class for the json body to be matched
class AwesomePhoto(BaseModel):
  location: str
  name: str

# Your Endpoint function with the class as a parameter
def post(photo_meta: AwesomePhoto, file_content = File(...)):
  link = upload_blob(file_content)

  return {
  "photo_name": photo_meta.name,
  "location": photo_meta.location,
  "file_url": link
  }
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

### Runtime and Environment

Shortstack environments are running Python 3.7+. We highly recommend type hints, which you can read more about [here](/typehints.md)

# Release Notes

[Latest Version v0.5](/release.md?id=v05)

Older Versions:

[v0.4](/release.md?id=v04)

[v0.3](/release.md?id=v03)

[v0.2](/release.md?id=v02)

# Python Types

[Learn more here](/typehints.md)
