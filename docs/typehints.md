# Python Type Hints

Python added optional type hints in 3.6+. Brev endpoints highly leverage typehints, which you can see in JSON params section.

By typing the NewUser class, we're able to automatically match fields
from an incoming JSON body, so your function only has to deal with the incoming `newUser` parameter.

```python
from pydantic import BaseModel # don't forget the import

# The class for the json body to be matched
class NewUser(BaseModel):
  name: str
  email: str
  phone: Optional[str] = None

# add the class as a parameter. Yay types! (it let's us know how to match the JSON) :)
def post(newUser: NewUser):
  return {
    "created": True,
    "newUser": newUser.name,
  }
```

## Simple Types

The basic python types are

- int
- float
- bool
- bytes
- str

## Data Structure types

To use these types, you'll need to import them from `typing`

```python
from typing import Set, Tuple`

anEmptyList: List[str] = []

```
