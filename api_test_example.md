Title: API Load Test Example
Description: Load test a REST based HTTP API with Bearer token authentication 

## Overview

Included is the test we use for load testing the LoadForge API. We use a Bearer token for authentication 
which you can see being set in the on_start startup definition, and then several GET requests to fetch 
data from our API. 

Tip! Remember to disable API rate limits on your token, or you will get loads of failures :)

## Code

```python
from locust import HttpUser, task, between

class QuickstartUser(HttpUser):
    wait_time = between(5, 9)
    
    def on_start(self):
        self.client.headers.update({'Authorization': 'Bearer xxx'})

    @task(1)
    def index_page(self):
        self.client.get("/api/hosts")
        self.client.get("/api/tests")
        self.client.get("/api/result/1488")
        self.client.get("/api/result/1463")          
```
