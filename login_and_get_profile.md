Title: Login and get profile
Description: Test that logs in and then requests a profile page.

## Overview

A test which posts to a login page when it starts, then requests /hello and /world normally. It also 
requests /item?id={item_id} with the item ID being between 1 and 10. You can also use random numbers in 
locustfiles.

## Code

```python
from locust import HttpLocust, TaskSet, task

class UserBehavior(TaskSet):
    def on_start(self):        
        self.login()    

    def login(self):
        self.client.post("/login", {"username":"foo", "password":"bar"})
    
    @task(2)
    def index(self):
        self.client.get("/")   
    
    @task(1)
    def profile(self):
        self.client.get("/profile")

class WebsiteUser(HttpLocust):
    task_set = UserBehavior
    min_wait = 5000
    max_wait = 9000
```
