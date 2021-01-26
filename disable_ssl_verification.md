Title: Disable SSL Verification
Description: Examples of how to prevent SSL verification on your tests.

## Overview

If you have an SSL certificate that is failing a verification (self signed, or something strange) you can disable checks 
in your test easily. Below are some examples of ways you can disable it, not an entire test configuration.

## Code

```python
def on_start(self):
    self.client.verify = False
```

```python
def login_page(self):
    self.client.post("/api/login", verify=False, {"Password":"xxxxx"})
```
