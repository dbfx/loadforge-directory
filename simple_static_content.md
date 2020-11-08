Title: Index Get with Content
Description: Index page GET with several static content items included.

## Overview

The below example requests an index page, and then a CSS, JS, and image file as well. You'll see the 
image request has a custom weight (@task(4)) to make it request 4x more images. 

## Code

```python
from locust import HttpUser, task, between

class QuickstartUser(HttpUser):
    wait_time = between(5, 9)

    @task(1)
    def index_page(self):
        self.client.get("/")
        self.client.get("/app.js")
        self.client.get("/app.css")

    @task(4)
    def image_selection(self):
        self.client.get("/images/logo.jpg")
```
