# Readme

## Screenshots of program execution

I am using an HTTP client to give the create task and execute requests.
Here is the screenshot for the Create Task request:

![](https://github.com/thejasm/task-master/blob/main/Screenshots/create.png)

Here is the date and time directly from the windows widget.
My full name is present in the HTTP request body as the "owner".


---


Here is the screenshot for the Execute Task request:

![](https://github.com/thejasm/task-master/blob/main/Screenshots/execute.png)

Date and Time are visible, and I wrote my name in a separate notepad window.
The response for the execute command is visible at the very bottom, returning the response JSON as expected.

## Prerequisites

- Java 17+
- Maven
- MongoDB: A MongoDB server running locally (or accessible) on mongodb://localhost:27017.
- Kubernetes: A running Kubernetes cluster.

## How to run

1. Start your MongoDB server.
2. Make sure your Kubernetes cluster is running.
3. From the project's root directory, run:
```mvn spring-boot:run```
4. The application starts on http://localhost:8080.

## API Examples

Here are examples of the programs functions using curl.

 ### 1. Create a Task (PUT)

This will create a new task. The ID is 123.

Request:
```
curl -X PUT http://localhost:8080/tasks \
-H "Content-Type: application/json" \
-d '{
    "id": "123",
    "name": "Print Hello",
    "owner": "admin",
    "command": "echo Hello World!"
}'
```

Response (Example):
```
{
  "id": "123",
  "name": "Print Hello",
  "owner": "admin",
  "command": "echo Hello World!",
  "taskExecutions": []
}
```

### 2. Get All Tasks (GET)

This returns a list of all tasks in the database.

Request:
```
curl http://localhost:8080/tasks
```

Response (Example):
```
[
  {
    "id": "123",
    "name": "Print Hello",
    "owner": "admin",
    "command": "echo Hello World!",
    "taskExecutions": []
  }
]
```

### 3. Get Single Task by ID (GET)

Request:
```
curl "http://localhost:8080/tasks?id=123"
```

Response (Example):
```
{
  "id": "123",
  "name": "Print Hello",
  "owner": "admin",
  "command": "echo Hello World!",
  "taskExecutions": []
}
```

### 4.  Find Tasks by Name (GET)

Request:
```
curl "http://localhost:8080/tasks/find?name=Print"
```

Response (Example):
```
[
  {
    "id": "123",
    "name": "Print Hello",
    "owner": "John Smith",
    "command": "echo Hello World!",
    "taskExecutions": []
  }
]
```

### 5.  Execute a Task (PUT)

This executes the command for task 123.

Request:
```
curl -X PUT http://localhost:8080/tasks/123/execute
```

Response (Example):
```
{
  "id": "123",
  "name": "Print Hello",
  "owner": "admin",
  "command": "echo Hello World!",
  "taskExecutions": [
    {
      "startTime": "2025-11-14T02:34:10.123Z",
      "endTime": "2025-11-14T02:34:15.456Z",
      "output": "Hello World!\n"
    }
  ]
}
```

### 6.  Delete a Task (DELETE)

This deletes the task with the given ID.

Request:
```
curl -v -X DELETE http://localhost:8080/tasks/123
```

Response (Example):
```
< HTTP/1.1 204 No Content
```
