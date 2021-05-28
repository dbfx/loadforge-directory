Title: Adding Sleeps
Description: How to add custom sleep times to a test

## Overview

By default, you should set your wait_time to handle "user timings" like reading the page, etc. However, 
you can have custom sleeps anywhere in the test should you need to. 

## Code

```python
import random
from random import randint
from locust import HttpUser, TaskSet, task, between
import time

class AwesomeUser(HttpUser):
    wait_time = between(10, 20)
    
    def load_page(self):        
        self.client.get('/page1')
        
        # Sleep for 10 - 60 seconds
        time.sleep(random.randint(10, 60))
        
        self.client.get('/page2')        
```


