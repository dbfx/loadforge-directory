Title: Login with CSRF Token
Description: Support CSRF tokens, for example with the Laravel framework. 

## Overview

A test which posts to a login page when it starts, then requests /hello and /world normally. However, it also 
specifically handles getting a CSRF token for logging in with (in this example) Laravel. 

Laravel automatically checks for a CSRF token when you submit data (by default). 
CSRF is designed to stop cross-site scripting against your site, and involves having a temporary token on 
each page that is submitted with every post. You'll know you've hit this issue if you receive an HTTP 419 
error. 

The below example can also be used for many other frameworks, or altered to suit them.

## Code

```python
import time
from locust import HttpUser, task, between

class QuickstartUser(HttpUser):
    wait_time = between(3, 5)

    def on_start(self):
        response = self.client.get("/login")
        csrftoken = response.cookies['XSRF-TOKEN']
    
        self.client.post("/login", 
                         {"username": "my_user", "password": "my_password"}, 
                         headers={"X-XSRF-TOKEN": csrftoken})

    @task
    def index_page(self):
        self.client.get("/hello")
        self.client.get("/world")    
```
