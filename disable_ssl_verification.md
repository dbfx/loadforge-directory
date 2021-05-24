Title: Disable SSL Verification
Description: Examples of how to prevent SSL verification on your tests.

## Overview

If you have an SSL certificate that is failing a verification (self-signed, or something strange) you can disable checks 
in your test easily. Below are some examples of ways you can disable it, not an entire test configuration. If you 
need further help contact us for support. 

## Notes on SSL verification
SSL verification is not really necessary on a load test, and you shouldn't panic about disabling it if your certificate is invalid. 
We still have to simulate the full SSL negotiation, and the load test will still be completely valid. This is often useful 
during test or development cycles.

## Code

```python
def on_start(self):
    self.client.verify = False
```

```python
def login_page(self):
    self.client.post("/api/login", verify=False, {"Password":"xxxxx"})
```

The primary function is adding the "verify=False" section to either your on_start client definition, or, if your self.client.post or self.client.get requests.
