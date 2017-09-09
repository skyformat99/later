## later ![](https://travis-ci.org/btfak/later.svg?branch=master)
later is a redis base delay queue



### Usege

```
go install github.com/btfak/later
$: ./later -h
Usage of ./later:
  -address string
    	serve listen address (default ":8080")
  -redis string
    	redis address (default "redis://127.0.0.1:6379/0")
```



### Feature

- Delay push message to target
- At-lease-once delivery
- Fail and retry
- reliable
- performance


### Frontend API

Response http code: **200** success, **400** request invalid, **404** task not found, **500** internal error

- Create Task

  ```
  Request:
  POST /create
  {
  	"topic":"order",
  	"delay":15, // second
  	"retry":3,  // max retry 3 times, interval 10,20,40... seconds
  	"callback":"http://127.0.0.1:8888/", // http post to target url
  	"content":"hello" // content to post
  }
  Response:
  {
      "id": "35adbde5-77c4-4d65-adac-0082d91f2554"
  }
  ```

- Delete Task

  ```
  Request:
  POST /delete
  {
  	"id":"35adbde5-77c4-4d65-adac-0082d91f2554"
  }
  ```

- Query Task

  ```
  Request:
  POST /query
  {
  	"id":"35adbde5-77c4-4d65-adac-0082d91f2554"
  }
  Response:
  {
      "id": "cb9aefdd-5bd1-4bf3-8c94-1ed5c2ea638e",
      "topic": "order",
      "execute_time": 1504934230,
      "max_retry": 1,
      "has_retry": 0,
      "callback": "http://127.0.0.1:8888/success",
      "content": "hello",
      "creat_time": 1504934220
  }
  ```

### Backend API

- Callback

  ```
  Request:
  POST /?
  {
    "id": "57e177ff-454c-42d6-93ab-65895b950dbf",
    "topic": "order",
    "content": "hello"
  }
  Response:
  {
      "code":100 // 100: success,101: too many request,other: fail
  }
  ```

  At-lease-once delivery, may repeat delivery.  Backend api should idempotent and always return response.