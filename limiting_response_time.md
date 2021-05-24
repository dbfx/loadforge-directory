Title: Checking Response Text and Time
Description: Check for a specific word in the result, and that it doesn't take over 0.5 seconds.

## Overview

Here we can see a snippet that checks for the word "Success" in a response, and then generates a 
failure if its taken more than half a second to reply. 

This is useful as it shows an example of monitoring the actual timings around a response. With LoadForge 
you can parse the content that comes back, read headers, monitor timings and more. 

This test will report failures in your LoadForge test when anything is slow. 

## Code

```python
import time
from locust import HttpUser, task, between

class QuickstartUser(HttpUser):
    wait_time = between(3, 5)

    @task(1)
    def index_page(self):        
        with self.client.get("/", catch_response=True) as response:
            if response.text != "Success":
                response.failure("Got wrong response")
            elif response.elapsed.total_seconds() > 0.5:
                response.failure("Request took too long")    
```
