Title: WordPress Load Test Example
Description: Load test a WordPress based Blog or CMS with automatic crawling 

## Overview

Load testing (aka stress testing) your WordPress Blog or CMS is made extremely easy by LoadForge, and you can test it 
with a free account today. We are going to be showing how to simulate a real set of users, browsing through your 
latest blog posts at random.

We have a [full WordPress load testing guide available](https://loadforge.com/guides/wordpress-load-testing) as well. 

## Code

```python
from locust import HttpUser, task, between
from pyquery import PyQuery
import random

class QuickstartUser(HttpUser):
    wait_time = between(5, 9)

    def index_page(self):
        r = self.client.get("/blog/")
        pq = PyQuery(r.content)

        link_elements = pq(".blog-list-post h2 a")
        self.blog_urls = []

        for l in link_elements:
          if "href" in l.attrib:
            self.blog_urls.append(l.attrib["href"])

    def on_start(self):
        self.index_page()

    @task(1)
    def static_content(self):
        # images
        self.client.get("/img/logo/logo.png")
        self.client.get("/img/images/cta_img.png")
        self.client.get("/img/logo/logo.png")
        self.client.get("/img/images/footer_fire.png")
        # styles
        self.client.get("/css/compiled.css?id=629e6d86d0748098936a")
        # scripts
        self.client.get("/js/compiled.js?id=1a927dbcfa8d96520959")

    @task(3)
    def blog_post(self):
        url = random.choice(self.blog_urls)
        r = self.client.get(url)


    @task(3)
    def home_page(self):
        self.client.get("/blog/")        
```
