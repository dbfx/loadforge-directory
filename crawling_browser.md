Title: Crawling Real HTTP User
Description: Index a site and crawl all the pages with a random delay as a real user would.

## Overview

This test is an example from the Locust community. It goes to their documentation site (https://docs.locust.io/en/latest/) 
and finds all the links in the table of contents on that page. 

It then simulates crawling those by choosing a random one every 10 to 600 seconds. An excellent example of 
simulating real user load. 

## Code

```python
import random
from locust import HttpUser, between, task
from pyquery import PyQuery

class AwesomeUser(HttpUser):    
    # we assume someone who is browsing the Locust docs, 
    # generally has a quite long waiting time (between 
    # 10 and 600 seconds), since there's a bunch of text 
    # on each page
    wait_time = between(10, 600)
    
    def on_start(self):
        # start by waiting so that the simulated users 
        # won't all arrive at the same time
        self.wait()
        # assume all users arrive at the index page
        self.index_page()
        self.urls_on_current_page = self.toc_urls
    
    @task(10)
    def index_page(self):
        r = self.client.get("")
        pq = PyQuery(r.content)
        link_elements = pq(".toctree-wrapper a.internal")
        self.toc_urls = [
            l.attrib["href"] for l in link_elements
        ]
    
    @task(50)
    def load_page(self):
        url = random.choice(self.toc_urls)
        r = self.client.get(url)
        pq = PyQuery(r.content)
        link_elements = pq("a.internal")
        self.urls_on_current_page = [
            l.attrib["href"] for l in link_elements
        ]
    
    @task(30)
    def load_sub_page(self):
        url = random.choice(self.urls_on_current_page)
        r = self.client.get(url)
```
