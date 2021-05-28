Title: Multiple User Logins
Description: How to create a test that logs in as different users

## Overview

It's quite common to need to login to a site, and we already have several guides on that: 

- [Login and Crawl Site](https://loadforge.com/directory/login_crawl_pages)
- [Login and Get Profile](https://loadforge.com/directory/login_and_get_profile)
- [Login at Test Start](https://loadforge.com/directory/login_at_start)
- [Login using CSRF](https://loadforge.com/directory/login_with_csrf)

This guide will give you an example of how to login as different users throughout the test.

## Code

```python
from locust import HttpUser, TaskSet, task, between
import time

class AwesomeUser(HttpUser):    
    wait_time = between(10, 20)

    # On start populate our user list
    def on_start(self): 
        self.createUsernames()

    # Create an array of user1 -> user1000
    def createUsernames(self): 
        self.users = []
        for i in range(1000):
            self.users.append("user"+str(i+1))

    # Fetch the next user in our list
    def getUsername(self):
        return self.users.pop()


    # Login to the site
    def login(self):        
        username = self.getUsername()
        loginInformation = {
            "username": username,
            "password":"passw0rd"
        }
    
        self.client.post("/login", loginInformation)

    # Logout from the site
    def logout(self):
        self.client.get('/logout', name="logout")


    # Define our tasks here
    @task(1)
    def load_page(self):
        # Login first
        self.login()

        # Put your logged in tasks here
        self.client.get('/profile/info')    
        
        # Logout now
        self.logout()
```

The above code creates 1000 users named "user1" through "user1000" and logs in with each of those. It's designed for a 
1000 user test of course. You can easily modify this. You can also pick user names at random like so:

```python
def getUsername(self):
    return "user"+str(randomint(1,1000))
```


