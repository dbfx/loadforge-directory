Title: Login and Crawl All Pages
Description: Login to a site and crawl all the pages on the index page.

## Overview

This test starts with a login and a request to the index page of the site. It then finds all 
anchor tags (a href) and randomly browses through them.

## Code

```python
import random
from locust import HttpLocust, TaskSet, task
from pyquery import PyQuery

class AwesomeUser(HttpUser):    
    def login(l):
        l.client.post("/login", {
            "username":"EXAMPLE_USER", 
            "password":"PASSWORD"
        })

    def index_page(self):
        r = self.client.get("/")
        pq = PyQuery(r.content)
        
        link_elements = pq("a")
        self.toc_urls = []
        
        for l in link_elements:
          if "href" in l.attrib:
            self.toc_urls.append(l.attrib["href"])

    def on_start(self):
        self.login()   
        self.index_page()

    @task
    def load_page(self):
        url = random.choice(self.toc_urls)
        r = self.client.get(url)
```
