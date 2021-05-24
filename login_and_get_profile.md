Title: Login and get profile
Description: Test that logs in and then requests a profile page.

## Overview

A test which posts to a login page when it starts, then requests /profile and some static content normally.  
This is a great example for logging into a site and then requesting content after login.

The on_start definition will be run once by each individual simulated user. So they all login first, and then 
execute your various tests. 

## Code

```python
import time
from locust import HttpUser, task, between

class QuickstartUser(HttpUser):
    def on_start(self):        
        self.client.post("/login", {'email': 'user@domain.com', 'password': 'passw0rd'})
    
    @task(2)
    def profiles(self):
        self.client.get("/profile")     
        
    @task(1)
    def statics(self):
        self.client.get("/img/logo.png")
        self.client.get("/css/styles.css")
        self.client.get("/js/scripts.js")
```
