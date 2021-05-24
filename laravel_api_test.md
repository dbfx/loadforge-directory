Title: Laravel REST API Example
Description: Load testing a REST based Laravel (or other) API example with auth and CRUD.

## Overview

This test is an actual example from us load testing the LoadForge REST API. We have added detailed comments in the test file 
to help you understand what we are doing and why. 

There are four main parts to it: 

1. Get hosts and tests at random
2. When starting, get all the results and save the result ids for later as well as set a bearer token 
3. Get a random result from the list of results we received earlier
4. Create and then delete (clean up after yourself) a host via the API.

You can see how easily we are able to load test our Laravel API, or any REST-based API really. Nice features are being 
able to fetch all the results, and then get random result ids as we can parse the responses in your test. 

## Code

```python
import random
from locust import HttpUser, task, between

class QuickstartUser(HttpUser):
    wait_time = between(5, 9)

    def get_results(self):
        self.results = []

        # Fetch all the results and store their IDs for later
        response = self.client.get("/api/results")
        for result in response.json():
            self.results.append(result['id'])


    def on_start(self):
        # Spread out starts
        self.wait()

        # Set out Bearer auth token
        self.client.headers.update({'Authorization': 'Bearer TOKEN_HERE'})

        # Load up all the possible results
        self.get_results()


    @task(1)
    def read_hosts(self):
        self.client.get("/api/hosts")

    @task(2)
    def read_tests(self):
        self.client.get("/api/tests")

    @task(5)
    def read_results(self):
        # Select a random result from the get_results query to fetch
        self.client.get("/api/result/" + str(random.choice(self.results)))

    @task(1)
    def crud_host(self):
        # Make a new Host and get the json reply
        response = self.client.post("/api/hosts/create", {'url': 'www.test.com', 'port': '443', 'protocol': 'https'})
        host = response.json()

        # Delete what we just made
        self.client.post("/api/hosts/delete", {"host_id": host['host_id']})
```
