Title: Testing websockets
Description: How to use LoadForge to load test websockets based applications

## Overview

Websocket testing is naturally more complicated than simple webserver, or web application load testing. However, 
with LoadForge and locust files you can connect to and load test websockets easily. 

## Example

```python
import time
import json
import gevent

from uuid import uuid4

from locust import HttpLocust, TaskSet, task, events, User
import websocket

class SocketClient(object):
    def __init__(self, host):
        self.host = host
        self.session_id = uuid4().hex
        self.connect()

    def connect(self):
        self.ws = websocket.WebSocket()
        self.ws.settimeout(10)
        self.ws.connect(self.host)

        events.quitting += self.on_close

        self.attach_session()

    def attach_session(self):
        payload = {'action': 'attach_session', 'session_id': self.session_id}
        json_data = self.send_with_response(payload)

        assert json_data['action'].lower() == 'attach_session'
        assert json_data['message_status'].lower() == 'ok'
        assert json_data['session_id'] == self.session_id

    def send_with_response(self, payload):
        json_data = json.dumps(payload)

        g = gevent.spawn(self.ws.send, json_data)
        g.get(block=True, timeout=2)
        g = gevent.spawn(self.ws.recv)
        result = g.get(block=True, timeout=10)

        json_data = json.loads(result)
        return json_data

    def on_close(self):
        self.ws.close()

    def send(self, payload):
        message_id = uuid4().hex
        payload.update({'_messageId': message_id,
                        'session_id': self.session_id})
        start_time = time.time()
        e = None
        try:
            data = self.send_with_response(payload)
            assert data['_messageId'] == message_id
            assert data['session_id'] == self.session_id
        except AssertionError as exp:
            e = exp
        except Exception as exp:
            e = exp
            self.ws.close()
            self.connect()
        elapsed = int((time.time() - start_time) * 1000)
        if e:
            events.request_failure.fire(request_type='sockjs', name='send',
                                        response_time=elapsed, exception=e)
        else:
            events.request_success.fire(request_type='sockjs', name='send',
                                        response_time=elapsed,
                                        response_length=0)

class WSBehavior(TaskSet):
    @task(1)
    def action(self):
        data = {
            "action": "do_stuff",
            "param": "123",
        }
        self.client.send(data)

class WSUser(User):
    task_set = WSBehavior
    min_wait = 1000
    max_wait = 3000

    def __init__(self, *args, **kwargs):
        super(WSUser, self).__init__(*args, **kwargs)
        self.client = SocketClient('ws://%s/rt/websocket' % self.host)
```

