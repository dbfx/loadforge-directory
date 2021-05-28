Title: Custom HTTP Headers
Description: How to set custom headers with your requests

## Overview

You can easily set any headers you need by tweaking your get() or post() calls to include a header set. 

We have an example of this below.

## Code

```python
self.client.post(        
    "/login", 
    data=json.dumps(payload),
    headers={"Content-Type": "application/vnd.api+json"}
)

self.client.get("/api/test", headers={"Content-Type": "application/json"}
```

You can also set it globally by defining the on_start() section like so: 

```python
def on_start(self):
    self.client.headers.update({'Authorization': 'Bearer xxx'})
       
def on_start(self):
    self.client.headers.update({'Content-Type': 'application/json'})    
```


