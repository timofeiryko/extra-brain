[[Technical documentaion]] example
# Backend

## Modules

- **models.py**: main classes (User, Group, BuyerFeedback, SellerFeedback), scheme for SQLAlchemy
- **parsing.py**: functions for extrating json data from Ton.Space API
- **back.py**: functions for getting data from the database and updating it
- **config.py**: configs for the project (GLOBAL, BACKEND, FRONTEND, PARSING)

## Main classes

- **User**: every user of the bot is saved here
- **Group**: each user can add groups to sell adds in it, data about the groups is stored here
- **BuyerFeedback**: sellers can add feedbacks about the buyers here
- **SellerFeedback**: buyers can add feedbacks about the sellers here

## Workflow

At the beginning of every chain/action, where getting data from the database or inserting data into it, Session object should be created with the function `connect()` from **back.py**. Technically, this is a structure that helps you to extract data from the database, as well as make commits to the database at the moment you want.

For easier getting the objects from the database or creating it if the object with given parmeters (id usually) don't exist yet, several functions in **back.py** are defined. Each function takes the necessary information about the obkects and also session (if this argument isn't taken, the function just creates its separated session).

To get the information about the retrieved objects, you can just refer to the attributes of the main classes. To get the parent or child objects, check  `back_populates=`  in the **models.py**.

After all manipulations, each edited object should be added to the session with `session.add(object)` and commited with `session.commit(object)` or `session.commit_all([first_object, second_object])`.

```python
from models import BuyerFeedback, Group, SellerFeedback, User
from back import *

session = connect()

# we want to save this data
tg_username = '@example'
group_id = '0'
cost1000 = 3.14

# choose the user
user = select_user(tg_username, session)

# create the froup for this user
group = select_group(group_id, user, session)
# add some information about this group
group.cost1000 = cost1000

# commit
session.add()
session.commit()

# get some data
feedbacks = [el for el in user.groups]
# commit is not needed!
```

