Title: Getting Auth Token
Description: Send a login request to get an authorization token for the rest of your test

## Overview

Below is a partial test (just the on_start section) that shows how to set an authorization (or really any) header 
based on response content at launch with your load test.

Naturally, you would not do all of these. They are 3 examples of getting a Bearer token in different ways. 

## Code

```python
from locust import HttpLocust, TaskSet, task
import requests

class UserBehavior(TaskSet):
  def on_start(self):
  	  # Send login request
      response = requests.post("http://mysite.com/login", {"username": "user", "password": "pass"})
      
      # set "token" from response header
      self.client.headers.update({'Authorization': response.headers.get('token')})

      # OR set "token" from response cookies
      self.client.cookies.set('Authorization', response.cookies.get('token'))
      
      # OR set "token" from response body
      self.client.headers.update({'Authorization': str(response.content.decode().find('token'))})      
```
