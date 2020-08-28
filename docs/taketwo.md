## Getting Started Guide

### A quick summary from the founders

Shortstack is a platform that let's you focus on the essence of your backend while we do all the heavy lifting. Ignore anyting about environments, infrastructure, etc, and just... code. We hope you build something great and make shortstack your best friend, it sure is ours :')

To clarify: this is not just an abstraction, it's a powerful developer tool thats genuinely writing all of the backend/infrastructure code behind the scenes, allowing you to return your mindshare to your product and your customers!

We totally get that sometimes, frameworks are just too limiting. We've been there, so we designed Shortstack from the start with ability to eject. At any time, you can go under the hood, or even eject your code, including all of the backend code we wrote for you, and go host it yourself. If you do this, we'd love to know where we fell... well.. short :)

We hope you love it
-Nader & Alec

This doc will go over the core features of Shortstack by building a sign up form that texts you everytime someone signs up. ETA: 10 min

[ ] Create basic endpoint
[ ] GET call with query arguments
[ ] POST call with JSON body
[ ] Outta the box functionality: SMS
[ ] Use State to link them
[ ] View Logs
[ ] Move state logic to Shared Code
[ ] Swap state for real database
[ ] Use Variables to do that^ securely
[ ] Auth flow with cotter (?)

### Setup

#### Webapp Setup

You can get started right away by logging in to https://app.getshortstack.com, or set up the CLI.

#### CLI Setup

on Mac:

> brew install getshortstack

on Windows:
The CLI is supported on WSL. Please follow the instructions here to install.

### Create a basic endpoint

A huge benefit of Shortstack is that the concept of deploying has been abstracted. Simply create an endpoint, and by default, it's available.

From the webapp: click "+" to create an endpoint. Give it a name, and click save!
From the CLI: type `stack add endpoint <your EP name>`

Boom, your endpoint is saved and hosted!

> Webapp: At the top of the code editor, you'll see the hoted URL. It's ready to go and be used in any app you're building. Go to "home" and you'll see all your endpoints, including this one.

> CLI: You should now have a shortstack directory at `~/GetShortstack`. Creating a new endpoint will add a python file for the endpoint in that directory. `stack list` will get you the URLs for every endpoint. If something didn't work, make sure to login and initialize! `stack login` followed by `stack init`.

Now let's make the endpoint do something.

[✓] Create basic endpoint
[ ] GET call with query arguments
[ ] POST call with JSON body
[ ] Outta the box functionality: SMS
[ ] Use State to link them
[ ] View Logs
[ ] Move state logic to Shared Code
[ ] Swap state for real database
[ ] Use Variables to do that^ securely
[ ] Auth flow with cotter (?)

### Handle a GET request with a query parameter

Simply add the parameter to the function and use it like a variable!

```python
def GET(greeting):
  return {"response": f"{greeting} world!"}
```

Now let's hit the endpoint!

> In the webapp: below the code editor, you can use the endpoint runner. Select GET and query params. Type greeting into the table, and hola as the value. Shortstack might've predicted the query args and type it in for you.

> In the CLI: `stack run endpointName GET --args greeting=hola`. Note if you set up shell autocomplete, you can hit tab and the CLI will suggest the available endpoints and HTTP request types! Also, you can type -a instead of --args.
> Note: you can also just open a new tab and manually type in the query argument.

The response should be as follows:

```json
{"response": "hola world"} // GET /endpointurl?greeting=hola

{"response": "marhaba world"} // GET /endpointurl?greeting=marhaba
```

Your endpoint can now accept data! Now let's do a POST.

[✓] Create basic endpoint
[✓] GET call with query arguments
[ ] POST call with JSON body
[ ] Outta the box functionality: SMS
[ ] Use State to link them
[ ] View Logs
[ ] Move state logic to Shared Code
[ ] Swap state for real database
[ ] Use Variables to do that^ securely
[ ] Auth flow with cotter (?)

### POST call with JSON body

Notice how your endpoint function name was called `get`. You can type any valid HTTP request type, and Shortstack will pattern match the appropriate function based on the incoming HTTP request. So to create a POST call, we just create a function called `post`.

```python
def post():
    return {}
```

For query parameters, we just used a variable, but JSON bodies can be more complicated, so let's create a class to pass in to our `post` function.

```python
from pydantic import BaseModel

class NewUser(BaseModel):
    name: str # yay types!
    phone: str # yay types!
```

```python
from pydantic import BaseModel

class NewUser(BaseModel):
    name: str # yay types!
    phone: str # yay types!

def post(user: NewUser):
    return {
        "status": f"{user.name} says hi!"
    }
```

Now let's test it with the following JSON body:

```json
{
  "name": "Shortstack",
  "phone": "4158180207"
}
```

> CLI: create a new json file `touch input.json` and edit it with the contents. `stack run MyEndpoint POST --body /pathTo/input.json`. With shell autocomplete enabled, you can autocomplete the endpoint name, the HTTP request type, and the available json files.

> Webapp: select POST instead of GET from the drop down, select JSON for the payload type.

```json
{
  "status": "Shortstack says hi!"
}
```

Next we'll notify you everytime a new user gets created!

[✓] Create basic endpoint
[✓] GET call with query arguments
[✓] POST call with JSON body
[ ] Outta the box functionality: SMS
[ ] Use State to link them
[ ] View Logs
[ ] Move state logic to Shared Code
[ ] Swap state for real database
[ ] Use Variables to do that^ securely
[ ] Auth flow with cotter (?)

### Out of the box functionality: SMS

```python
from pydantic import BaseModel
import sms

class NewUser(BaseModel):
    name: str # yay types!
    phone: str # yay types!

def post(user: NewUser):
    sms.send("4158180207", f"{user.name} created an account!")
    return {
        "status": f"{user.name} says hi!"
    }
```

Hit the endpoint again and you should get a text!

In just a few minutes you were able to get live, deployed, endpoints and SMS functionality! No configuration needed :tada:

[✓] Create basic endpoint
[✓] GET call with query arguments
[✓] POST call with JSON body
[✓] Outta the box functionality: SMS
[ ] View Logs
[ ] Use State to link them
[ ] Move state logic to Shared Code
[ ] Swap state for real database
[ ] Use Variables to do that^ securely
[ ] Auth flow with cotter (?)

### View Endpoint Logs

Getting logs and metrics for your endpoints is typically a hurdle within itself. This is built in to Shortstack!

> Webapp: Go to "Logs" from the navigation bar on the left. You'll see all the server logs per project!

> CLI: `stack logs` views all logs for a project. Optionally filter by endpoitn: `stack logs MyEndpoint` gets logs for the endpoint only

[✓] Create basic endpoint
[✓] GET call with query arguments
[✓] POST call with JSON body
[✓] Outta the box functionality: SMS
[✓] View Logs
[ ] Use State to link them
[ ] Move state logic to Shared Code
[ ] Swap state for real database
[ ] Use Variables to do that^ securely
[ ] Auth flow with cotter (?)

### Use storage to persist data

The next step to your MVP is being able to persist data between endpoint calls. We can do this with the storage object.

```python
    from pydantic import BaseModel
    import sms

    class NewUser(BaseModel):
        name: str # yay types!
        phone: str # yay types!

    def post(user: NewUser):
        if state['users'] == None:
            state['users'] = []
>       state['users'].append(user)
        sms.send("4158180207", f"{user.name} created an account!")
        return {
            "status": f"{user.name} says hi!"
        }
```

Every time you hit the endpoint, you'll store the users! If you're in the webapp, click on "storage" from the navbar to view the state object.

We can make a simple API to build a dashboard with your users. Create a new endpoint with the following:

```python

def get():
    return {"users": state['users']}

```

[✓] Create basic endpoint
[✓] GET call with query arguments
[✓] POST call with JSON body
[✓] Outta the box functionality: SMS
[✓] View Logs
[✓] Use State to link them
[ ] Move state logic to Shared Code
[ ] Swap state for real database
[ ] Use Variables to do that^ securely
[ ] Auth flow with cotter (?)

> Note: The storage object does have limitations. Unlike your endpoints, it won't handle scale well. It's a simple and great hack to get a proof of concept up and running. It's a simple dictionary that gets entirely overwritten everytime, so we highly recommend using a real database for your MVP. Let's do that next!

### Shared Code

Before we hook up the database, let's isolate the "storage" code by using Shared Code. Shared Code is code that's available to the entire project.

> CLI: every project folder will have a shared.py for shared code

> Webapp: every endpoint editor page has a tab for shared code

Let's move the storage logic to a shared code function

```python
# shared code

def storeUser(user)
    if state['users'] == None:
        state['users'] = []
    state['users'].append(user)

```

Now update the endpoint to use this function. Don't forget the import!

```python
    from pydantic import BaseModel
    import sms
>   import shared

    class NewUser(BaseModel):
        name: str # yay types!
        phone: str # yay types!

    def post(user: NewUser):
>       shared.storeUser(user)
        sms.send("4158180207", f"{user.name} created an account!")
        return {
            "status": f"{user.name} says hi!"
        }
```

Voila! Now we can swap out the storage logic without disrupting your endpoint :)

[✓] Create basic endpoint
[✓] GET call with query arguments
[✓] POST call with JSON body
[✓] Outta the box functionality: SMS
[✓] View Logs
[✓] Use State to link them
[✓] Move state logic to Shared Code
[ ] Swap state for real database
[ ] Use Variables to do that^ securely
[ ] Auth flow with cotter (?)
