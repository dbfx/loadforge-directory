Title: GraphQL Load Test Example
Description: Load test a GraphQL based API (SpaceX in this example) 

## Overview

Below we have an example of a load test to query the missions from the open SpaceX GraphQL API. You 
can adapt this to test your own API. We also have more advanced uses below that. 

## Code

Below is a standard GraphQL load test that requests 
```
{
  missions {
    id
    name
  }
}
```

From the SpaceX API. 

```python
import time
from locust import HttpUser, task, between

class QuickstartUser(HttpUser):
    wait_time = between(3, 5)

    @task(1)
    def index_page(self):
        query = '''
            {
              missions {
                id
                name
              }
            }
            '''

        response = self.client.post(
            "/graphql",
            name="GraphQL",            
            json={"query": query }
        )          
```



## Advanced GraphQL Load Tests

You may need to authenticate against the GraphQL API, and you can do so by setting headers.

```python
    response = self.client.post(
        "/graphql",
        name="GraphQL",
        headers={
            "Accept": "application/graphql",
            "Authorization": "<Authorization-Token>"
        },
        json={"query": query }
)
```

You can also automate receiving an authorization token by setting an on_start declaration, like so: 

```python
    def on_start(self):
      # Send login request
      response = requests.post("http://mysite.com/login", {"username": "user", "password": "pass"})
      
      # set "token" from response header
      self.client.headers.update({'Authorization': response.headers.get('token')})
```

## Further Reading

Read our [GraphQL load testing blog post](https://loadforge.com/blog/14/load-testing-graphql-apis) for more information.
