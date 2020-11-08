Title: Login at Start
Description: Test that logs in for each simulated user before testing.

## Overview

A test which posts to a login page when it starts, then requests /hello and /world normally. It also 
requests /item?id={item_id} with the item ID being between 1 and 10. You can also use random numbers in 
locustfiles.

## Code

```python
import time
from locust import HttpUser, task, between

class QuickstartUser(HttpUser):
    wait_time = between(3, 5)

    @task
    def index_page(self):
        self.client.get("/hello")
        self.client.get("/world")

    @task(3)
    def view_item(self):
        for item_id in range(10):
            self.client.get(f"/item?id={item_id}", name="/item")
            time.sleep(1)

    def on_start(self):
        self.client.post("/login", {
            'username': 'foo', 'password': 'bar'
        })
```
