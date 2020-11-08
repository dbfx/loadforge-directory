Title: Simple Index Get
Description: Index page with a simple HTTP GET and a simulated user wait time between pages.

## Overview

This test requests your index page with a simple HTTP GET. It has a simulated user wait time between pages of 
between 5 and 9 seconds. 

You can easily add additional urls by duplicating the "self.client.get" line.

## Code

```python
from locust import HttpUser, task, between

class QuickstartUser(HttpUser):
    wait_time = between(5, 9)

    @task(1)
    def index_page(self):
        self.client.get("/")
```
