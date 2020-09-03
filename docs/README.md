## Getting Started Guide

### A quick summary from the founders

Brev is a platform that let's you focus on the essence of your backend while we do all the heavy lifting. Ignore anyting about environments, infrastructure, etc, and just... code. We hope you build something great and make Brev your best friend, it sure is ours :')

To clarify: this is not just an abstraction, it's a powerful developer tool thats genuinely writing all of the backend/infrastructure code behind the scenes, allowing you to return your mindshare to your product and your customers!

We totally get that sometimes, frameworks are just too limiting. We've been there, so we designed Brev from the start with ability to eject. At any time, you can go under the hood, or even eject your code, including all of the backend code we wrote for you, and go host it yourself. If you do this, we'd love to know where we fell... well.. short :)

We hope you love it
-Nader & Alec

**Highlighted Features**:

- **Production ready by default**: Scale without extra configuration or additional knowledge
- **Deployless**: Deployments just happen instantly when you save.
- **Configureless**: We handle the environments and infrastructure. Whether you're coding from the CLI and your local environment or the web app, the environment is ready to go.
- **Ergonomic request/response validation**: Use python3 type hints to define the shape of your request args and body.
- **Openapi by default**: Openapi specification is automatically generated for you as you write and annotate your endpoints
- **Secrets management**: Store your applications secrets securely without extra effort
- **Package management**: Use the power of python's community with 3rd party packages found on pypi
- **Shortstore:** A super simple nosql database to prototype your applications -- backed by Dynamodb.

**Coming Soon**:

Our roadmap is constantly evolving with feedback from our users. Here are some of the things we are thinking about.

- **Eject:** Eject your project at any point in your project to host it yourself and get under the hood. Access all the configuration details and more without worrying about them at the start of your project.
- **Project Environments:** Create multiple different isolated environments to develop or experiment on your APIs ex. (staging, prod, dev)
- **Cron Jobs** Create and manage cron jobs without any extra setup.
- **Application monitoring, alerting:** get crash alerts and performance monitoring out of the box or hook up (Sentry, APM, etc).
- **Robust Logging and Querying** Search and query for relevant logs to quickly debug your application

Use this quickstart guide to jump into Brev. If you have any lingering questions, [please e-mail us](nader+docs@brev.dev)

**Project Status**

[✓] Alpha: We are testing Brev with a small set of users.

[✓] Public Alpha: Join our waitlist and we will get you access as soon as we can. We hope you build something great! The platform still has kinks please help us iron them out.

[ ] Public Beta: Anyone can create an account. API becomes stable.

[ ] Public launch: Stable for enterprise use cases.

### This doc

will go over the core features of Brev by building a sign up form that texts you everytime someone signs up.

**7 steps, eta: 10 min**

### Setup

#### Webapp Setup

You can get started right away by logging in to https://app.brev.dev, or set up the CLI.

#### CLI Setup

on Mac:

> brew install getBrev

on Windows:
The CLI is supported on WSL. Please follow the instructions here to install.

### Create a basic endpoint

**Step 1 of 7**

A huge benefit of Brev is that the concept of deploying has been abstracted. Simply create an endpoint, and by default, it's available.

From the webapp: click "+" to create an endpoint. Give it a name, and click save!
From the CLI: type `stack add endpoint <your EP name>`

Boom, your endpoint is saved and hosted!

> Webapp: At the top of the code editor, you'll see the hoted URL. It's ready to go and be used in any app you're building. Go to "home" and you'll see all your endpoints, including this one.

> CLI: You should now have a Brev directory at `~/GetBrev`. Creating a new endpoint will add a python file for the endpoint in that directory. `stack list` will get you the URLs for every endpoint. If something didn't work, make sure to login and initialize! `stack login` followed by `stack init`.

Now let's make the endpoint do something.

### GET request with a query args

To accept a query argument, simply add the argument to the function and use it like a variable!

```python
def get(greeting):
  return {"response": f"{greeting} world!"}
```

Now let's hit the endpoint!

> In the webapp: below the code editor, you can use the endpoint runner. Select GET and query params. Type greeting into the table, and hola as the value. Brev might've predicted the query args and type it in for you.

> In the CLI: `stack run endpointName GET --args greeting=hola`. Note if you set up shell autocomplete, you can hit tab and the CLI will suggest the available endpoints and HTTP request types! Also, you can type -a instead of --args.
> Note: you can also just open a new tab and manually type in the query argument.

The response should be as follows:

```json
{"response": "hola world"} // GET /endpointurl?greeting=hola

{"response": "marhaba world"} // GET /endpointurl?greeting=marhaba
```

Read more about how this works
<a href="/#/full?id=query-arguments" target="_blank">here</a>

Your endpoint can now accept data! Now let's do a POST.

### POST call with JSON body

The function name from the previous example was `get`. You can use any valid HTTP request type, and Brev will pattern match the appropriate function based on the incoming HTTP request. Read more
<a href="/#/full?id=handling-http-methods" target="_blank">here</a>
. So to handle a POST call, we just add a function called `post`.

```python
def post():
    return {}
```

For query arguments in the previous example, we just used a variable, but JSON bodies can be more complicated, so we can create a class representing the incoming data and pass it in to our `post` function.

```python
from pydantic import BaseModel

# type definition of incoming JSON body
class NewUser(BaseModel):
    name: str # yay types!
    phone: str # yay types!

# pass it in to function
def post(user: NewUser):
    return {
        "status": f"{user.name} says hi!"
    }
```

Now let's test it with the following JSON body:

```json
{
  "name": "Brev",
  "phone": "4158180207"
}
```

> CLI: create a new json file `touch input.json` and edit it with the contents. `stack run MyEndpoint POST --body /pathTo/input.json`. With shell autocomplete enabled, you can autocomplete the endpoint name, the HTTP request type, and the available json files.

> Webapp: select POST instead of GET from the drop down, select JSON for the payload type.

```json
{
  "status": "Brev says hi!"
}
```

We're defining a [Pydantic](https://pydantic-docs.helpmanual.io) model to define what our data should look like. Read more
<a href="/#/full?id=request-body" target="_blank">here</a>

Next we'll notify you everytime a new user gets created!

### Get an SMS text every signup!

Brev comes preconfigured with a lot of out-of-the-box functionality such as SMS. To send texts, just `import sms` and use `sms.send`. Read more
<a href="/#/full?id=Brev-tools" target="_blank">here</a>

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

### Use storage to persist data

The next step to your MVP is persisting data between endpoint calls. We can do this with the Shortstorage object.
<a href="/#/full?id=shortstorage" target="_blank">Read more about Shortstorage here.</a>

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

> Note: Shortstorage is a great way to get up and running, but it won't scale as elegantly as your endpoints. We're consistently improving it, but if you have an intense amount of data, it might be best to hook up your own database.

### Shared Code

Every Brev project has a Shared Code file, accessible by every endpoint in the project. This is a good place to isolate the storage code.

<a href="/#/full?id=shared-code" target="_blank">Read more here.</a>

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

Voila! Now we can modify our storage logic without disrupting your endpoint :)

### Variables

In the examples above, I've been using my phone number in the code.
This is a bad practice, so we can use Variables!
<a href="/#/full?id=variables" target="_blank">Read more here.</a>
(P.S. that is my real number, shoot me a text and let me know how it's going!)

> CLI: run `stack add variable PHONENUMBER` and it will prompt you for the value.

> Web app: Click on `Variables` on the nav bar on the left. Add the new variable to the table.

Now we just need to import variables in our code and we can use the values!

```python
    from pydantic import BaseModel
    import sms
    import shared
>   import variables

    class NewUser(BaseModel):
        name: str # yay types!
        phone: str # yay types!

    def post(user: NewUser):
        shared.storeUser(user)
>       sms.send(variables.PHONENUMBER, f"{user.name} created an account!")
        return {
            "status": f"{user.name} says hi!"
        }
```

### Final Thoughts

Hopefully this was a useful introduction to Brev!
<a href="/#/full" target="_blank">The full docs are here.</a>

Do you have any questions? Feature requests? Something missing from the docs? Wanna chat?

Talk to us!

Nader:
cell: 4158180207
email: nader+docs@brev.dev
