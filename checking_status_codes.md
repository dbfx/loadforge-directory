Title: Checking Status Codes
Description: Test that checks for a 404 response or considers it a failure.

## Overview

Below is a snippet that allows you to test, for example, that the webserver actually returns a 404 not found. It 
can be useful to look for specific status codes that you want in replies.

## Code

```python
import time
from locust import HttpUser, task, between

class QuickstartUser(HttpUser):
    wait_time = between(3, 5)

    @task(1)
    def index_page(self):
        self.client.get("/")
    
        with self.client.get("/does_not_exist/", catch_response=True) as response:
            if response.status_code == 404:
                response.success()        
```
